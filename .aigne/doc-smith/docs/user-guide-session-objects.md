# Session Objects

The Session object allows you to persist certain parameters across requests. It also persists cookies over all requests made from the instance, and will use `urllib3`'s connection pooling. If you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

While you have learned how to make individual requests in the [Making a Request](./user-guide-making-a-request.md) guide, a Session object is essential for more complex interactions with an API or website where state needs to be maintained.

## Basic Usage

To get started, simply create an instance of the `Session` class. It can be used in the same way as the top-level `requests` module.

```python
import requests

s = requests.Session()

response = s.get('https://httpbin.org/get')
print(response.status_code)

response = s.post('https://httpbin.org/post', json={'key': 'value'})
print(response.json())
```

## Cookie Persistence

A primary use case for sessions is to maintain cookies across multiple requests. The Session object automatically handles this for you. Any cookies set by the server on a response will be captured and sent on subsequent requests made with the same session. This is critical for interacting with services that use cookie-based authentication or session tracking.

Here is a workflow demonstrating how a session manages cookies:

```d2
shape: sequence_diagram

Client
Session
Server

Client -> Session: s.get("https://httpbin.org/cookies/set/sessioncookie/12345")
Session -> Server: "GET /cookies/set/sessioncookie/12345"
Server -> Session: "Response with 'Set-Cookie' header"
note: {
  "Cookie 'sessioncookie=12345' is stored in session.cookies"
  target: Session
}
Session -> Client: "Response Object"

Client -> Session: s.get("https://httpbin.org/cookies")
Session -> Server: "GET /cookies (sends stored 'Cookie' header)"
Server -> Session: "Response containing received cookies"
Session -> Client: "Response Object with cookie data"
```

**Example Code**

```python
import requests

with requests.Session() as s:
    # The first request to this URL sets a cookie in the session
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

    # The second request to a different URL on the same domain will automatically send the cookie
    response = s.get('https://httpbin.org/cookies')

    # The response will show the cookie sent by the session
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

The `session.cookies` object is an instance of `RequestsCookieJar`, which can be used like a dictionary but offers more advanced features.

## Persisting Parameters Across Requests

Sessions are also useful for setting default data that will be included in every request. This is done by setting attributes on the `Session` object.

| Attribute     | Description                                               |
|---------------|-----------------------------------------------------------|
| `headers`     | A dictionary of headers to send with every request.       |
| `auth`        | An authentication tuple or callable.                      |
| `params`      | A dictionary of query string parameters to add to the URL.|
| `proxies`     | A dictionary of proxies to use.                           |
| `verify`      | SSL verification setting (boolean or path to CA bundle).  |
| `cert`        | Path to an SSL client certificate.                        |

**Example: Persisting Headers**

If you want to ensure a specific set of headers is sent with every request, you can update the session's `headers` dictionary.

```python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'true'})

# This request will have the 'x-test-header'
response1 = s.get('https://httpbin.org/headers')
print('Response 1 Headers:', response1.json()['headers']['X-Test-Header'])

# This request will also have the same header
response2 = s.get('https://httpbin.org/headers')
print('Response 2 Headers:', response2.json()['headers']['X-Test-Header'])
```

**Merging Parameters**

If you provide parameters at the method level (e.g., in `s.get()`), they will be merged with the session-level parameters. Method-level parameters will override session parameters if there is a key conflict for that specific request.

```python
import requests

with requests.Session() as s:
    s.params.update({'param1': 'session_value'})
    s.headers.update({'X-Custom': 'SessionHeader'})

    # This request will include both session and method params.
    # 'X-Custom' will be overridden for this request only.
    response = s.get('https://httpbin.org/get', 
                     params={'param2': 'request_value'},
                     headers={'X-Custom': 'RequestHeader'})

    print("URL Arguments:", response.json()['args'])
    print("Custom Header:", response.json()['headers']['X-Custom'])

    # A subsequent request will revert to the session's default header.
    response2 = s.get('https://httpbin.org/get')
    print("Subsequent Custom Header:", response2.json()['headers']['X-Custom'])
```

**Example Output**

```text
URL Arguments: {'param1': 'session_value', 'param2': 'request_value'}
Custom Header: RequestHeader
Subsequent Custom Header: SessionHeader
```

## Session as a Context Manager

The `Session` object can be used as a context manager, which will automatically call `session.close()` when the `with` block is exited. This is the recommended practice as it ensures that all underlying connections in the pool are closed properly.

```python
import requests

with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Inside with block, status: {response.status_code}")

# The session 's' is now closed and its connections are released.
```

Using a session effectively manages state and improves performance by reusing connections. For interactions requiring secure access, continue to the next section to learn how to manage [Authentication](./user-guide-authentication.md).
