# Timeouts, Retries, and Proxies

Building robust applications requires handling unreliable network conditions gracefully. Requests provides powerful, yet easy-to-use mechanisms for configuring timeouts, retrying failed connections, and routing traffic through proxies. This guide will walk you through these advanced features to help you control the network behavior of your HTTP requests.

## Timeouts

You can configure Requests to stop waiting for a response after a specified number of seconds. This is a crucial feature to prevent your application from hanging indefinitely on slow or unresponsive network connections. 

Most requests to external servers should have a timeout attached. Without one, your code might hang for minutes or more.

### Basic Timeout

To set a timeout, use the `timeout` parameter. If the server has not sent any data in the specified time, a `requests.exceptions.Timeout` exception is raised.

```python Timeout for a single request icon=logos:python
import requests

try:
    response = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('The request timed out')
else:
    print('The request did not time out')
```

### Connect and Read Timeouts

The `timeout` value can also be a tuple to set different timeouts for connecting and reading.

<x-field data-name="timeout" data-type="float or tuple" data-desc="How long to wait for the server before giving up.">
  <x-field data-name="connect" data-type="float" data-desc="The timeout for establishing a connection to the server."></x-field>
  <x-field data-name="read" data-type="float" data-desc="The timeout for waiting for the server to send a response."></x-field>
</x-field>

If you specify a single float, the value will be applied to both the `connect` and `read` timeouts.

```python Connect and Read Timeouts icon=logos:python
import requests

# Wait 3.05 seconds to connect, and 27 seconds to receive data
try:
    response = requests.get('https://httpbin.org/delay/5', timeout=(3.05, 27))
except requests.exceptions.ConnectTimeout:
    print('Connection timeout occurred')
except requests.exceptions.ReadTimeout:
    print('Read timeout occurred')
```

## Retries

By default, Requests does not automatically retry failed requests. To implement a retry mechanism, you need to use a `Session` object and mount an `HTTPAdapter` with a configured retry strategy.

The `max_retries` parameter on an `HTTPAdapter` applies only to connection-level failures such as DNS errors, socket connection issues, and connection timeouts. It will not retry requests where data has already been successfully sent to the server.

### Configuring Retries

To configure retries, you create an `HTTPAdapter` instance, specifying the maximum number of retries, and then mount it to a `Session` for a specific protocol (e.g., `http://` or `https://`).

```python Configuring Retries with an Adapter icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# Create a session
session = requests.Session()

# Create an adapter with a retry strategy
# This will retry failed connections up to 3 times
adapter = HTTPAdapter(max_retries=3)

# Mount the adapter to the session for both HTTP and HTTPS
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    # Use the session to make a request
    response = session.get('http://a.very.nonexistent.domain.com')
except requests.exceptions.ConnectionError as e:
    print(f'Failed to connect after multiple retries: {e}')
```

For more granular control, you can import and pass a configured `urllib3.util.retry.Retry` object instead of an integer to `max_retries`.

## Proxies

If you need to route your requests through a proxy server, you can configure it using the `proxies` parameter.

### Basic Proxy Usage

The `proxies` argument is a dictionary mapping the URL scheme to the URL of the proxy.

```python Using an HTTP Proxy icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

response = requests.get('https://httpbin.org/get', proxies=proxies)
print(response.json())
```

### Proxies with Authentication

To use Basic HTTP Proxy Authentication, include the username and password in the proxy URL:

```python Proxy with Authentication icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS Proxies

To use a SOCKS proxy, you need to install an additional package:

```bash
pip install requests[socks]
```

Once installed, you can use `socks5://` or `socks5h://` in the proxy URL. `socks5h` indicates that DNS resolution should happen on the proxy server side.

```python SOCKS Proxy icon=logos:python
proxies = {
    'http': 'socks5h://user:password@host:port',
    'https': 'socks5h://user:password@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

### Environment Variables

Requests will automatically read and use proxy configurations from the standard `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables. You can disable this behavior on a `Session` object by setting `trust_env=False`.

The `NO_PROXY` environment variable can be set to a comma-separated list of hosts or IP addresses that should bypass the proxy.

```python Disabling Environment Proxies icon=logos:python
s = requests.Session()
s.trust_env = False

# This request will not use proxies from environment variables
s.get('https://httpbin.org/get')
```

---

By mastering timeouts, retries, and proxies, you can significantly enhance the reliability and flexibility of your applications. For more advanced network control, continue to the next section on [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md).