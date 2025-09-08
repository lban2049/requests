# Session Objects

The `Session` object allows you to persist certain parameters across requests. It also persists cookies over all requests made from the Session instance and will use `urllib3`'s connection pooling. If you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

## Basic Usage

The `Session` object has all the methods of the main `requests` API. Let's start by persisting some cookies across requests.

```python Session Basic Usage icon=logos:python
import requests
s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

For proper resource management, it's recommended to use a `with` statement, which ensures the session is closed automatically, even if exceptions occur, by calling `Session.close()` upon exit.

```python Session with Context Manager icon=logos:python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    response = s.get('https://httpbin.org/cookies')
    print(response.json())
```

**Example Response**
```json
{
  "cookies": {
    "sessioncookie": "123456789"
  }
}
```
Any cookies set in the session will be sent with all subsequent requests made with that session. This is particularly useful for maintaining a logged-in state across multiple API calls.

## Persisting Parameters

Sessions can also provide default data to the request methods. This is done by setting properties on a `Session` object. Any dictionary you pass to a request method will be merged with the session-level values. Importantly, method-level parameters will always override session-level parameters.

### Headers

Here, we set a default header on the session, which is then merged with headers provided at the request level.

```python Persisting Headers icon=logos:python
import requests

with requests.Session() as s:
    s.headers.update({'x-test': 'true'})

    # Both 'x-test' and 'x-test2' are sent with the request
    response = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
    print(response.json()['headers'])
```

**Example Response**
```json
{
  "X-Test": "true", 
  "X-Test2": "true",
  ...
}
```

### Query Parameters

Session-level query parameters are also merged with any provided in a specific request.

```python Persisting Query Parameters icon=logos:python
import requests

with requests.Session() as s:
    s.params = {'api_key': 'shared_key'}

    # The request is sent to https://httpbin.org/get?api_key=shared_key
    response = s.get('https://httpbin.org/get')
    print(response.json()['args'])
```
**Example Response**
```json
{
  "api_key": "shared_key"
}
```

### Other Configurations

You can also set other request parameters at the session level, such as authentication credentials, proxies, and SSL verification settings.

```python Other Session Configurations icon=logos:python
import requests

with requests.Session() as s:
    # Set default authentication for all requests
    s.auth = ('username', 'password')

    # Set default proxies
    s.proxies = {
        'http': 'http://10.10.1.10:3128',
        'https': 'http://10.10.1.10:1080',
    }

    # Set default SSL certificate verification
    s.verify = '/path/to/my/ca.pem'

    # This request will use the auth, proxies, and verify settings
    # defined on the session.
    response = s.get('https://api.example.com/data')
```

## Performance and Connection Pooling

When you make multiple requests to the same host with a `Session` object, it reuses the underlying TCP connection. This process, known as connection pooling, avoids the overhead of establishing a new connection for every request, which is particularly beneficial for HTTPS traffic that requires a TLS handshake.

Under the hood, `Session` objects use a `requests.adapters.HTTPAdapter` which manages a pool of connections via `urllib3.PoolManager`.

The diagram below illustrates the difference in connection handling.

```d2
direction: down

subgraph "Individual Requests" {
  shape: rectangle
  label: "Individual Requests"
  App1: App {shape: rectangle}
  Server1: Server {shape: cylinder}
  
  App1 -> Server1: "Request 1: New TCP Connection"
  App1 -> Server1: "Request 2: New TCP Connection"
  App1 -> Server1: "Request 3: New TCP Connection"
}


subgraph "Session-based Requests" {
  shape: rectangle
  label: "Session-based Requests"
  App2: App {shape: rectangle}
  Session: Session {shape: rectangle}
  Server2: Server {shape: cylinder}

  App2 -> Session: creates
  Session -> Server2: "Request 1: New TCP Connection"
  Session -> Server2: "Request 2: Reuses Connection" {style.stroke-dash: 2}
  Session -> Server2: "Request 3: Reuses Connection" {style.stroke-dash: 2}
}
```

## Advanced Control

Beyond basic parameter persistence, `Session` objects offer more granular control over network behavior.

### Redirect Handling

Sessions automatically handle redirects. You can control this behavior using the `max_redirects` property on the `Session` object. By default, it is set to 30.

```python Controlling Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

with requests.Session() as s:
    s.max_redirects = 3 # The default is 30

    try:
        # This URL redirects 4 times.
        response = s.get('https://httpbin.org/redirect/4') 
    except TooManyRedirects as e:
        print(f"Redirect limit exceeded: {e}")

# Expected Output:
# Redirect limit exceeded: Exceeded 3 redirects.
```

### Transport Adapters

Requests can be extended with Transport Adapters, allowing you to define custom interaction methods for specific transport protocols. For instance, you can implement a custom retry strategy for HTTP requests.

The `Session` object allows you to mount these adapters for specific prefixes.

```python Custom Retry Strategy icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

# Create a session
s = requests.Session()

# Define the retry strategy
retry_strategy = Retry(
    total=3,
    status_forcelist=[429, 500, 502, 503, 504], # Retry on these status codes
    backoff_factor=0.3
)

# Create an adapter with the retry strategy and mount it
adapter = HTTPAdapter(max_retries=retry_strategy)
s.mount('https://', adapter)
s.mount('http://', adapter)

try:
    # Make a request to an endpoint that will fail
    response = s.get('https://httpbin.org/status/503')
    response.raise_for_status()
except requests.exceptions.RetryError as e:
    print(f"Request failed after multiple retries: {e}")
```
In this example, any `GET` request made through the session to an `http://` or `https://` URL will be retried up to 3 times if it returns one of the specified server error status codes.

By using `Session` objects, you can write cleaner code, manage state like cookies effortlessly, and improve your application's performance and resilience.

---

The next section covers how to handle different types of authentication, a task often simplified by using sessions.

Continue to the next section to learn about [Authentication](./user-guide-authentication.md).