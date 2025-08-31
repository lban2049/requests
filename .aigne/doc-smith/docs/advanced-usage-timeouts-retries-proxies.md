# Timeouts, Retries, and Proxies

Controlling network behavior is essential for building robust applications that interact with web services. This section covers how to configure timeouts to prevent indefinite hangs, set up automatic retries for transient network failures, and route your requests through proxies.

## Timeouts

You can tell Requests to stop waiting for a response after a given number of seconds with the `timeout` parameter. In most cases, this is a crucial setting to prevent your program from hanging indefinitely when network issues arise.

The `timeout` value applies to both the connect and read phases of the request.

```python
# Set a timeout of 5 seconds for the entire request
response = requests.get('https://api.github.com/events', timeout=5)
```

For more granular control, you can specify the connect and read timeouts separately by passing a tuple:

*   **Connect timeout**: The time allowed for the client to establish a connection to the server.
*   **Read timeout**: The time allowed for the client to wait for a response from the server after the connection has been established.

```python
# Wait 3.05 seconds to connect, and then wait 27 seconds for the server to send a response
response = requests.get('https://api.github.com/events', timeout=(3.05, 27))
```

If the timeout is reached, Requests will raise a `Timeout` exception. You can catch this to handle the error gracefully.

```python
import requests
from requests.exceptions import Timeout

try:
    response = requests.get('https://api.github.com/events', timeout=0.001)
except Timeout:
    print('The request timed out.')
```

## Retries

By default, Requests does not automatically retry failed requests. To implement a retry strategy, you need to use a custom `HTTPAdapter` and mount it to a `Session` object.

### Simple Retries

The simplest way to configure retries is to provide an integer to the `max_retries` parameter of an `HTTPAdapter`. This will apply a default retry mechanism for failed DNS lookups, socket connections, and connection timeouts.

```python
import requests
from requests.adapters import HTTPAdapter

# Create a session
s = requests.Session()

# Configure an adapter with a simple retry strategy
# This will retry a request up to 3 times for connection-related errors.
a = HTTPAdapter(max_retries=3)

# Mount the adapter to the session for both HTTP and HTTPS
s.mount('http://', a)
s.mount('https://', a)

try:
    # This request will fail, but the adapter will retry it 3 times
    response = s.get('http://a-domain-that-does-not-exist.com')
except requests.exceptions.ConnectionError as e:
    print(f'Request failed after multiple retries: {e}')
```

### Advanced Retries

For more advanced control, such as retrying on specific HTTP status codes or implementing a backoff delay, you can pass a `urllib3.util.retry.Retry` object to `max_retries`. This gives you fine-grained control over the retry behavior.

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

s = requests.Session()

# Configure a more robust retry strategy
retries = Retry(total=5,
                backoff_factor=0.1,
                status_forcelist=[ 500, 502, 503, 504 ])

adapter = HTTPAdapter(max_retries=retries)

s.mount('http://', adapter)
s.mount('https://', adapter)

try:
    # This will retry on 503 errors with a backoff delay
    response = s.get('http://httpbin.org/status/503')
    response.raise_for_status()
except requests.exceptions.RequestException as e:
    print(f'Request failed: {e}')
```

Here is a visual representation of the retry flow with a backoff factor:

```d2
shape: sequence_diagram

Client
"Session with Adapter"
Server

Client -> "Session with Adapter": s.get('http://service.com/api')
"Session with Adapter" -> Server: GET /api
Server -> "Session with Adapter": 503 Service Unavailable

"Session with Adapter": {
  note: "Status in forcelist. Initiate retry with backoff (e.g., wait 0.1s)."
}

"Session with Adapter" -> Server: GET /api (Retry 1)
Server -> "Session with Adapter": 503 Service Unavailable

"Session with Adapter": {
  note: "Status in forcelist. Initiate retry with increased backoff (e.g., wait 0.2s)."
}

"Session with Adapter" -> Server: GET /api (Retry 2)
Server -> "Session with Adapter": 200 OK
"Session with Adapter" -> Client: Response (200 OK)
```

## Proxies

If you need to route your requests through a proxy server, you can use the `proxies` argument on any request method or configure it on a `Session` object.

```python
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### Authentication

If your proxy requires authentication, you can include the username and password in the proxy URL:

```python
proxies = {
   'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS Proxies

Requests also supports SOCKS proxies, but you first need to install the necessary third-party library:

```bash
pip install pysocks
```

Once installed, you can specify the SOCKS scheme in the proxy URL. Use `socks5` for local DNS resolution or `socks5h` to resolve DNS on the proxy server.

```python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```

### Environment Variables

Requests will automatically use proxies configured in your environment variables if the `proxies` argument is not explicitly set. It respects `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`.

You can configure these variables in your shell:

```bash
export HTTP_PROXY="http://10.10.1.10:3128"
export HTTPS_PROXY="https://10.10.1.10:1080"

# Bypass the proxy for specific hosts, domains, or IP ranges
export NO_PROXY="localhost,127.0.0.1,example.com"
```

With these environment variables set, the following Python code will automatically use the configured proxies without any extra parameters:

```python
# This request will be sent through the proxy defined in HTTPS_PROXY
requests.get('https://httpbin.org/ip')

# This request will bypass the proxy due to the NO_PROXY setting
requests.get('http://example.com')
```

With these network configurations mastered, you can further secure your connections. Learn more in the [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md) guide.
