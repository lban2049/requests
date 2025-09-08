# Timeouts, Retries, and Proxies

Controlling the network behavior of your requests is crucial for building resilient applications. Requests allows you to configure timeouts to prevent indefinite hangs, set up automatic retries for transient failures, and route your traffic through proxies for security or access purposes.

## Timeouts

By default, requests do not have a timeout and can hang indefinitely if the remote server is unresponsive. You should always specify a timeout to prevent this.

Most requests to external servers should have a timeout attached, which is expressed in seconds. To set a timeout, use the `timeout` parameter. You can provide a single float value for both the connect and read timeouts, or a tuple to set them individually.

*   **Connect Timeout**: The time allowed for the client to establish a connection to the server.
*   **Read Timeout**: The time allowed for the client to wait for a response from the server after a connection has been established.

```python Setting a Timeout icon=logos:python
import requests

# Set a single timeout (5 seconds) for both connect and read
try:
    response = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.ReadTimeout:
    print('The request timed out while waiting for the server to respond.')

# Set separate timeouts for connect and read
try:
    # 2-second connect timeout, 6-second read timeout
    response = requests.get('https://httpbin.org/delay/10', timeout=(2, 6))
except requests.exceptions.ConnectTimeout:
    print('The connection to the server timed out.')
except requests.exceptions.ReadTimeout:
    print('The server did not send any data in the allotted time.')
```

If the timeout is set as a tuple, the values will be `(connect_timeout, read_timeout)`. If a single float is provided, it applies to both.

## Retries

Requests does not retry failed connections by default. To implement a retry strategy, you need to use a `requests.adapters.HTTPAdapter`. By mounting a configured `HTTPAdapter` to a `requests.Session` object, you can specify the retry behavior for requests made through that session.

This is particularly useful for handling temporary network issues or intermittent server errors.

```python Simple Retry Configuration icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# Create a session object
s = requests.Session()

# Create an adapter with a simple retry configuration.
# This will retry failed DNS lookups, socket connections, and
# connection timeouts up to 3 times.
a = HTTPAdapter(max_retries=3)

# Mount the adapter to the session for HTTP and HTTPS
s.mount('http://', a)
s.mount('https://', a)

try:
    # The request to a 503 endpoint will be retried 3 times
    response = s.get('https://httpbin.org/status/503')
    print(f'Request succeeded with status code: {response.status_code}')
except requests.exceptions.RetryError as e:
    print(f'Request failed after multiple retries: {e}')
```

For more granular control, you can instantiate `urllib3.util.retry.Retry` and pass it to the `HTTPAdapter`. This allows you to specify conditions like which HTTP status codes should trigger a retry, which methods to retry on, and backoff strategies.

```python Advanced Retry Strategy icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

s = requests.Session()

retry_strategy = Retry(
    total=3,  # Total number of retries
    status_forcelist=[429, 500, 502, 503, 504],  # HTTP status codes to retry on
    backoff_factor=1  # A delay factor for retries (e.g., 1s, 2s, 4s)
)

adapter = HTTPAdapter(max_retries=retry_strategy)

s.mount('https://', adapter)
s.mount('http://', adapter)

try:
    response = s.get('https://api.example.com/unreliable_endpoint')
except requests.exceptions.RequestException as e:
    print(f'Failed to connect to the endpoint: {e}')
```

## Proxies

If you need to route your requests through a proxy server, you can use the `proxies` argument. This argument takes a dictionary mapping the URL scheme to the URL of the proxy.

```python Using Proxies icon=logos:python
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('https://example.org', proxies=proxies)
```

You can also configure proxies using the `HTTP_PROXY` and `HTTPS_PROXY` environment variables. Requests will automatically use these if the `proxies` argument is not provided.

### Proxy Authentication

To use Basic HTTP Proxy Authentication, include the username and password in the proxy URL:

```python Proxies with Authentication icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
    'https': 'https://user:password@10.10.1.10:1080/',
}

requests.get('https://example.org', proxies=proxies)
```

### SOCKS Proxies

Requests also supports SOCKS proxies, but this requires the `PySocks` library to be installed.

```bash Install SOCKS support icon=lucide:terminal
pip install pysocks
```

Once installed, you can specify a SOCKS proxy. Use `socks5` for proxies that perform DNS resolution on the client side, or `socks5h` to have the proxy resolve DNS.

```python Using a SOCKS Proxy icon=logos:python
proxies = {
    'http': 'socks5://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://example.com', proxies=proxies)
```

### Bypassing Proxies

To disable proxies for specific hosts or domains, you can set the `NO_PROXY` environment variable. It should be a comma-separated list of hostnames, domains, or IP addresses (including CIDR notation).

```bash Bypassing Proxies via Environment Variable icon=lucide:terminal
export NO_PROXY='localhost,127.0.0.1,example.com,192.168.0.0/24'
```

Requests will honor this variable, ensuring that requests to the specified destinations are sent directly, bypassing any configured proxies.

---

Mastering timeouts, retries, and proxies is key to building robust and reliable HTTP clients. For more on securing your connections, see the next section on [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md).
