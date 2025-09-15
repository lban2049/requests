# Timeouts, Retries, and Proxies

Building robust applications that interact with web services requires handling network instability and diverse network configurations. Requests provides powerful, yet straightforward, mechanisms for controlling connection timeouts, automatically retrying failed requests, and routing traffic through proxies. This guide covers how to configure these advanced network behaviors to make your application more resilient and adaptable.

## Timeouts

You can prevent your program from hanging indefinitely on network requests by setting a timeout. Most requests to external servers should have a timeout attached.

By default, requests do not time out unless a `timeout` value is set explicitly. Without a timeout, your code could hang for an indefinite amount of time if the server is unresponsive.

The `timeout` parameter can be configured in two ways:

<x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="How long to wait for the server to send data before giving up. A single float sets both connect and read timeouts. A tuple can be used to set them separately as (connect_timeout, read_timeout)."></x-field>

### Single Value Timeout

You can specify a single float value for the `timeout`, which will be applied to both the `connect` and `read` stages of the request.

```python Setting a Global Timeout icon=logos:python
import requests

try:
    # Wait for a maximum of 3.05 seconds for the entire request
    response = requests.get('https://httpbin.org/delay/5', timeout=3.05)
except requests.exceptions.Timeout:
    print("The request timed out.")

```

### Separate Connect and Read Timeouts

For more granular control, you can provide a tuple with two float values. The first value is the `connect` timeout (the time allowed to establish the initial connection), and the second is the `read` timeout (the time allowed between bytes from the server).

```python Setting Separate Connect and Read Timeouts icon=logos:python
import requests

try:
    # Wait 2 seconds to connect, and 5 seconds to receive the first byte
    response = requests.get('https://httpbin.org/delay/10', timeout=(2, 5))
except requests.exceptions.ConnectTimeout:
    print("The connection timed out.")
except requests.exceptions.ReadTimeout:
    print("The read timed out.")

```

If the remote server is very slow, you can tell Requests to wait forever for a response by passing `None` as the timeout value.

## Retries

In case of transient network failures, such as a failed DNS lookup or a connection timeout, you might want Requests to automatically retry the request. While Requests does not do this by default, you can configure this behavior using a `HTTPAdapter`.

The `max_retries` parameter on an `HTTPAdapter` allows you to specify the number of times a request should be retried for connection-related errors.

```python Configuring Retries with HTTPAdapter icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# Create a session
session = requests.Session()

# Configure an adapter with a retry strategy
# This will retry failed connections up to 3 times.
adapter = HTTPAdapter(max_retries=3)

# Mount the adapter to the session for both http and https
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    # This request will use the retry strategy
    response = session.get('http://a.domain.that.does.not.exist')
except requests.exceptions.ConnectionError as e:
    print(f"Failed after multiple retries: {e}")

```

For more complex retry logic (e.g., custom backoff factors, retrying on specific HTTP status codes), you can pass an instance of `urllib3.util.retry.Retry` to the `max_retries` parameter.

## Proxies

If you need to route your requests through a proxy server, you can use the `proxies` argument.

<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="A dictionary mapping protocol schemes (e.g., 'http', 'https') to the URL of the proxy."></x-field>

### Basic Proxies

You can configure proxies on a per-request basis by passing a dictionary to the `proxies` parameter.

```python Using HTTP and HTTPS Proxies icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https' 'http://10.10.1.10:1080',
}

# This request will be sent through the http proxy
requests.get('http://httpbin.org/get', proxies=proxies)

# This request will be sent through the https proxy
requests.get('https://httpbin.org/get', proxies=proxies)
```

### Environment Variables

Requests also respects standard proxy environment variables like `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`. If these are set, Requests will use them automatically for `Session` objects where `trust_env` is `True` (the default). You can still override them by passing the `proxies` argument explicitly.

### Authentication

If your proxy requires authentication, you can include the credentials in the proxy URL.

```python Proxies with Basic Authentication icon=logos:python
import requests

proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://httpbin.org/get', proxies=proxies)
```

### SOCKS Proxies

To use a SOCKS proxy, you need to install an extra dependency.

```bash Terminal icon=lucide:terminal
pip install pysocks
```

Once installed, you can use the `socks5` or `socks5h` scheme in your proxy URL. `socks5h` indicates that DNS resolution should also be handled by the proxy.

```python Using a SOCKS Proxy icon=logos:python
import requests

proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

---

With these tools, you can fine-tune the network behavior of your application to handle various conditions gracefully. For more advanced network control, you may want to explore how to manage SSL certificates.

<x-card data-title="Next: SSL Certificate Verification" data-href="/advanced-usage/ssl-cert-verification" data-icon="lucide:shield-check">
  Learn how to manage SSL/TLS verification, use custom CA bundles, or provide client-side certificates.
</x-card>
