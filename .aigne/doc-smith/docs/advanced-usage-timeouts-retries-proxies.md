# Timeouts, Retries, and Proxies

Building robust applications requires handling network variability and specific environmental constraints. Requests allows you to configure advanced network behaviors such as setting timeouts to prevent indefinite hangs, automatically retrying failed requests, and routing your traffic through proxies.

## Timeouts

By default, requests do not have a timeout, which means they can hang indefinitely if the server is unresponsive. To prevent this, you should always specify a `timeout` for your requests.

Most requests to external servers should have a timeout attached. You can set the timeout as a single float value, which will be applied to both the `connect` and `read` stages of the request.

```python Timeout Example icon=logos:python
# Set a timeout of 5 seconds for the entire request
response = requests.get('https://api.github.com/events', timeout=5)
```

### Connect vs. Read Timeouts

For more granular control, you can specify different timeouts for connecting and reading. The `connect` timeout is the number of seconds to wait for a connection to be established with the server. The `read` timeout is the number of seconds to wait for the server to send a response after the connection is established.

To set these individually, pass a tuple to the `timeout` parameter.

```python Connect and Read Timeouts icon=logos:python
# Wait 3.5 seconds to connect and 10 seconds to receive data
response = requests.get('https://api.github.com/events', timeout=(3.5, 10))
```

If you want a request to wait indefinitely (not recommended for most production scenarios), you can pass `None` as the timeout value.

## Retries

Requests does not automatically retry failed requests. To implement a retry mechanism, you need to use a `Session` object and mount a custom `HTTPAdapter` with a configured retry strategy.

The `max_retries` parameter of `HTTPAdapter` can be either an integer or an instance of `urllib3.util.retry.Retry` for more advanced control.

Here’s how to configure a session to retry a request up to 3 times on connection-related errors:

```python Basic Retries with HTTPAdapter icon=logos:python
import requests
from requests.adapters import HTTPAdapter

session = requests.Session()

# Configure the adapter to retry 3 times
adapter = HTTPAdapter(max_retries=3)

# Mount the adapter for both HTTP and HTTPS
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    response = session.get('http://a.server.that.does.not.exist')
except requests.exceptions.ConnectionError as e:
    print(f"Request failed after retries: {e}")
```

For more advanced scenarios, such as retrying on specific HTTP status codes (e.g., `503 Service Unavailable`), you can pass a `Retry` object from `urllib3`.

```python Advanced Retry Strategy icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()

retry_strategy = Retry(
    total=5,
    status_forcelist=[429, 500, 502, 503, 504], # Status codes to retry on
    backoff_factor=1  # A delay factor between retries
)

adapter = HTTPAdapter(max_retries=retry_strategy)

session.mount('https://', adapter)
session.mount('http://', adapter)

try:
    response = session.get('https://httpbin.org/status/503')
    print(f"Request succeeded with status code: {response.status_code}")
except requests.exceptions.RetryError as e:
    print(f"Request failed after multiple retries: {e}")

```

## Proxies

If you need to route your requests through a proxy server, you can use the `proxies` argument. This is common in corporate environments or for accessing geo-restricted content.

The `proxies` argument takes a dictionary mapping the URL scheme (e.g., `http`, `https`) to the URL of the proxy.

```python Using an HTTP Proxy icon=logos:python
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'https://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### Authentication

If your proxy requires authentication, you can include it in the proxy URL:

```python Proxy with Authentication icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://example.org', proxies=proxies)
```

### SOCKS Proxies

Requests also supports SOCKS proxies. To use them, you first need to install the `PySocks` library:

```bash
pip install PySocks
```

Once installed, you can specify a SOCKS proxy in the `proxies` dictionary using the `socks5` or `socks5h` scheme.

```python SOCKS Proxy icon=logos:python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```

### Environment Variables

Requests will automatically detect and use proxies configured in your environment variables (`HTTP_PROXY` and `HTTPS_PROXY`). You can disable this behavior by setting the `trust_env` property of a `Session` object to `False`.

You can also use the `NO_PROXY` environment variable to specify hosts that should bypass the proxy.


With these configurations, you can build more resilient and adaptable HTTP clients. For more advanced customization, you might want to explore SSL certificate handling.

Next, learn how to manage [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md).