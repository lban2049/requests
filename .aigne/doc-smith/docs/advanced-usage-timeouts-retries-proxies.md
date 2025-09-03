# Timeouts, Retries, and Proxies

Fine-tuning network behavior is essential for building resilient applications that can handle unreliable network conditions and complex corporate environments. This guide covers how to configure request timeouts, automatic retries for failed connections, and how to route your requests through proxies.

## Timeouts

You can configure Requests to stop waiting for a response after a given number of seconds. The `timeout` parameter can prevent your program from hanging indefinitely when network issues occur.

Most requests to external servers should have a timeout. Without one, your code might hang for minutes or more.

```python
import requests

# Wait for a maximum of 2.5 seconds before giving up
requests.get('https://httpbin.org/delay/3', timeout=2.5)
# Raises a ReadTimeout error
```

The `timeout` value can be a single float, representing the total time to wait for the server to send data. For more granular control, you can provide a tuple of two floats: `(connect_timeout, read_timeout)`.

- **Connect Timeout**: The time allowed for the client to establish a connection to the server.
- **Read Timeout**: The time allowed for the client to receive data from the server after the connection is established.

```python
import requests

# 1 second to connect, 3 seconds to wait for the first byte of the response
r = requests.get('https://httpbin.org/delay/2', timeout=(1.0, 3.0))
print(r.status_code)

# This will raise a ConnectTimeout
try:
    requests.get('https://httpbin.org/', timeout=(0.001, 3.0))
except requests.exceptions.ConnectTimeout:
    print("Connection timed out.")
```

If you want to wait indefinitely, you can pass `None` as the timeout value. However, this is generally not recommended for production code.


## Retries

By default, Requests does not retry failed connections. To implement a retry strategy, you need to use a `Session` object and mount a custom `HTTPAdapter` with a configured retry policy.

The `HTTPAdapter` allows you to specify the maximum number of retries for a connection. This retry logic applies to specific failures like DNS lookups, socket connection errors, and connection timeouts. It does not apply to requests where data has already been sent to the server.

Here is how to configure a `Session` to retry requests up to 3 times:

```python
import requests
from requests.adapters import HTTPAdapter

# Create a session object
s = requests.Session()

# Create an adapter with a retry strategy
# In this case, it will retry 3 times on failed connections
a = HTTPAdapter(max_retries=3)

# Mount the adapter to the session for both http and https prefixes
s.mount('http://', a)
s.mount('https://', a)

# Make a request using the session
try:
    response = s.get('http://a.non.existent.domain/')
except requests.exceptions.ConnectionError as e:
    print(f"Failed after multiple retries: {e}")

```

For more advanced control over which status codes to retry on or to implement exponential backoff, you can import and configure `urllib3.util.retry.Retry` and pass an instance of it to the `max_retries` parameter.


## Proxies

If you need to route your requests through a proxy server, you can use the `proxies` argument.

### Basic Proxy Usage

The `proxies` argument is a dictionary mapping the protocol scheme to the URL of the proxy.

```python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### Authentication

If your proxy requires authentication, you can include it in the proxy URL:

```python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS Proxies

To use a SOCKS proxy, you need to install the `PySocks` library:

```bash
pip install pysocks
```

Once installed, you can specify the proxy scheme as `socks5`, `socks5h`, `socks4`, or `socks4a`.

```python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```
`socks5h` indicates that DNS resolution should happen on the proxy server, which is often the desired behavior.

### Environment Variables

Requests automatically reads and uses proxy settings from environment variables like `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`. You can disable this behavior on a `Session` object by setting `trust_env=False`.

```bash
export HTTP_PROXY="http://10.10.1.10:3128"
export HTTPS_PROXY="http://10.10.1.10:1080"
```

With these variables set, the following Python code will automatically use the defined proxies without needing the `proxies` argument:

```python
import requests

# This request will be sent through http://10.10.1.10:3128
requests.get('http://example.org') 
```

### Bypassing Proxies

You can use the `NO_PROXY` environment variable to specify hosts that should bypass the proxy. This variable should be a comma-separated list of domain names, domain suffixes, or IP addresses.

For example, to bypass the proxy for `internal.example.com` and all hosts in the `192.168.0.0/16` network:

```bash
export NO_PROXY="internal.example.com,192.168.0.0/16"
```

Requests will check this variable and send requests to matching hosts directly, bypassing the configured proxy.

---

With these configurations, you can build more robust applications that handle network failures gracefully and operate within various network architectures. For securing your connections, proceed to the next section on [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md).
