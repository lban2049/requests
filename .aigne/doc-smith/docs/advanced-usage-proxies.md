# Proxies

When making HTTP requests, you may need to route your traffic through a proxy server for various reasons, such as network security, privacy, or accessing resources behind a firewall. Requests provides flexible ways to configure and manage proxies, whether directly for individual requests, through a persistent Session, or by leveraging environment variables.

For general information on managing persistent connections and settings, refer to the [Session object](./api-reference-session-object.md) section. To understand the different types of requests, see [HTTP Methods](./core-concepts-http-methods.md).

## Setting Proxies

You can specify proxies for your requests using a dictionary that maps URL schemes to proxy URLs. Requests supports HTTP, HTTPS, and SOCKS proxies.

### Per-Request Proxies

To use a proxy for a single request, pass the `proxies` dictionary to the request method (e.g., `requests.get()`, `requests.post()`).

**Parameters**

| Name | Type | Description |
|---|---|---|
| `proxies` | `dict` | A dictionary mapping protocol or protocol and host to the URL of the proxy (e.g., `{'http': 'http://10.10.1.10:1234', 'https': 'http://10.10.1.10:4321'}`). |

**Example**

```python
import requests

proxies = {
    'http': 'http://10.10.1.10:1234',
    'https': 'http://10.10.1.10:4321',
}

try:
    response = requests.get('http://example.com', proxies=proxies)
    print(f"Response status code: {response.status_code}")
except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via proxy: {e}")
```

This example configures HTTP and HTTPS proxies for a GET request to `http://example.com`.

### Session-Level Proxies

For persistent proxy settings across multiple requests, configure the `proxies` attribute on a `Session` object. This is more efficient as it reuses the underlying connection pools.

**Example**

```python
import requests

s = requests.Session()
s.proxies = {
    'http': 'http://10.10.1.10:1234',
    'https': 'http://10.10.1.10:4321',
}

try:
    response1 = s.get('http://example.com/page1')
    print(f"Page 1 status: {response1.status_code}")

    response2 = s.get('https://example.com/page2')
    print(f"Page 2 status: {response2.status_code}")

except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via session proxy: {e}")
finally:
    s.close()
```

In this example, both `s.get()` calls will use the proxies configured on the `Session` object.

## Environment Variables and `NO_PROXY`

Requests can automatically detect and use proxy settings from environment variables. This behavior is controlled by the `trust_env` attribute of the `Session` object, which is `True` by default.

### Automatic Proxy Detection

Requests checks for the following environment variables (case-insensitive) to determine proxy settings:

*   `HTTP_PROXY` or `http_proxy`: For HTTP requests.
*   `HTTPS_PROXY` or `https_proxy`: For HTTPS requests.
*   `ALL_PROXY` or `all_proxy`: A fallback for any scheme if a specific proxy isn't defined.

If `trust_env` is `True` (the default), Requests will merge these environment proxies with any proxies explicitly set on the Session or per-request. Explicitly defined proxies always take precedence.

### Bypassing Proxies (`NO_PROXY`)

The `NO_PROXY` environment variable (or `no_proxy`) allows you to specify a list of hostnames or IP addresses for which proxies should not be used. This is useful for internal network addresses or local hosts that do not require proxying.

Requests evaluates `NO_PROXY` rules to determine if a given URL should bypass proxying. The bypass logic supports:

*   **Hostname matching**: If the URL's hostname ends with any entry in `NO_PROXY` (e.g., `example.com` in `NO_PROXY` will bypass `www.example.com`).
*   **IP address matching**: Direct IP matches for IPv4 addresses.
*   **CIDR notation**: IP ranges specified in CIDR format (e.g., `192.168.1.0/24`).

**Example for `NO_PROXY`**

If you set `NO_PROXY` in your environment:

```bash
# On Linux/macOS
export HTTP_PROXY="http://yourproxy:8080"
export NO_PROXY="localhost,127.0.0.1,example.internal,192.168.1.0/24"

# On Windows (PowerShell)
$env:HTTP_PROXY="http://yourproxy:8080"
$env:NO_PROXY="localhost,127.0.0.1,example.internal,192.168.1.0/24"
```

Requests will then automatically use the `HTTP_PROXY` for external URLs but bypass it for `localhost`, `127.0.0.1`, anything ending in `example.internal`, or IPs within `192.168.1.0/24`.

Here is how Requests resolves which proxy to use for a given request:

```mermaid
flowchart TD
    A["Start Request"] --> B{"Is 'proxies' argument provided?"}
    B -- "Yes" --> C["Use 'proxies' argument"]
    B -- "No" --> D{"Is Session 'proxies' attribute set?"}
    D -- "Yes" --> E["Use Session 'proxies'"]
    D -- "No" --> F{"Is Session 'trust_env' True?"}
    F -- "Yes" --> G["Check Environment Proxies\n(HTTP_PROXY, HTTPS_PROXY, ALL_PROXY)"]
    F -- "No" --> I["No Environment Proxies"]
    G --> H["Merge Environment Proxies with current settings"]
    C --> K["Resolve Final Proxies (request + session + env)"]
    E --> K
    H --> K
    I --> K
    K --> L{"Evaluate 'NO_PROXY' rules against URL"}
    L -- "URL matches NO_PROXY" --> M["Bypass Proxies"]
    L -- "URL does not match NO_PROXY" --> N["Select Proxy by URL Scheme"]
    M --> O["Send Request Directly"]
    N --> P["Send Request via Selected Proxy"]
    O --> Q["Receive Response"]
    P --> Q
```

## Proxy Authentication

If your proxy server requires authentication, you can include the username and password directly in the proxy URL. Requests will automatically construct and send the `Proxy-Authorization` header.

**Example**

```python
import requests

proxies = {
    'http': 'http://user:password@proxy.example.com:8080/',
    'https': 'http://user:password@proxy.example.com:8080/',
}

try:
    response = requests.get('http://httpbin.org/get', proxies=proxies)
    print(f"Response status code with authenticated proxy: {response.status_code}")
except requests.exceptions.ProxyError as e:
    print(f"Proxy authentication failed or proxy unreachable: {e}")
```

This example demonstrates specifying credentials directly within the proxy URL.

## SOCKS Proxies

Requests supports SOCKS proxies. To use them, you need to install the `PySocks` package (`pip install "requests[socks]"`). Once installed, you can specify SOCKS proxy URLs using the `socks5` or `socks4` scheme.

**Example**

```python
import requests

proxies = {
    'http': 'socks5://user:password@127.0.0.1:9050',
    'https': 'socks5://user:password@127.0.0.1:9050'
}

try:
    response = requests.get('http://example.com', proxies=proxies)
    print(f"Response status code via SOCKS proxy: {response.status_code}")
except requests.exceptions.InvalidSchema as e:
    print(f"SOCKS dependencies missing: {e}. Please install requests[socks].")
except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via SOCKS proxy: {e}")
```

This example configures Requests to use a SOCKS5 proxy with authentication.

---

Understanding and properly configuring proxies is crucial for managing network requests effectively. Requests offers a robust system for handling various proxy scenarios, from simple per-request settings to complex environment-driven configurations with bypass rules. Next, explore how to ensure secure communication by managing TLS certificates in the [SSL Verification & Client Certificates](./advanced-usage-ssl-verification-client-certificates.md) section.