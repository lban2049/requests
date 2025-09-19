# Proxies

If you need to route your HTTP requests through a proxy server, Requests makes it simple. You can configure proxy settings on a per-request basis or for an entire `Session` object.

## Basic Usage

To use a proxy, you can pass a `proxies` dictionary to any request method. The dictionary should map URL schemes (like 'http' or 'httpss') to the URL of the proxy.

```python Proxies Example icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

response = requests.get('https://httpbin.org/get', proxies=proxies)
print(response.json())
```

In this example, requests to `http://` URLs will be routed through `http://10.10.1.10:3128`, and requests to `https://` URLs will go through `http://10.10.1.10:1080`.

You can also configure proxies on a `Session` object to apply them to all requests made with that session:

```python Session with Proxies icon=logos:python
import requests

session = requests.Session()
session.proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'https://10.10.1.10:1080',
}

# All subsequent requests made with this session will use the configured proxies
response = session.get('https://httpbin.org/get')
```

## Proxy Authentication

If your proxy requires authentication, you can include the username and password in the proxy URL, following the standard `user:password@host:port` syntax.

```python Authenticated Proxy icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://httpbin.org/get', proxies=proxies)
```

## SOCKS Proxies

Requests also supports SOCKS proxies. This functionality is not enabled by default and requires an external dependency. You can install the necessary package using pip:

```bash
pip install requests[socks]
```

Once installed, using a SOCKS proxy is as straightforward as using an HTTP proxy. The scheme for SOCKS proxies can be `socks5` or `socks5h`.

- `socks5`: Proxies your requests, but DNS resolution happens locally on your machine.
- `socks5h`: DNS resolution is performed by the proxy server, which can be useful for accessing hosts that are not resolvable from your local network.

```python SOCKS Proxy Example icon=logos:python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

## Environment Variables

Requests automatically detects and uses proxy settings from your system's environment variables. It looks for the following variables (lowercase versions are checked first):

- `HTTP_PROXY` or `http_proxy`
- `HTTPS_PROXY` or `https_proxy`
- `ALL_PROXY` or `all_proxy`

If these variables are set, Requests will use them for all requests by default. You can override this on a per-request basis by passing a `proxies` dictionary, or you can disable it entirely for a `Session`.

To disable environment variable proxy usage, set the `trust_env` attribute on a `Session` object to `False`:

```python Disabling Environment Proxies icon=logos:python
import requests

# This session will ignore any proxy settings from environment variables
session = requests.Session()
session.trust_env = False

# This request will be sent directly, without a proxy
response = session.get('https://httpbin.org/get')
```

### Bypassing Proxies with `NO_PROXY`

You can specify hosts that should bypass the proxy by setting the `NO_PROXY` (or `no_proxy`) environment variable. This should be a comma-separated list of domain names, hostnames, or IP addresses.

For example:

```bash
export NO_PROXY="localhost,127.0.0.1,example.com,192.168.1.0/24"
```

With this setting, any request to `localhost`, `127.0.0.1`, `example.com` (or any of its subdomains), or any IP within the `192.168.1.0/24` range will be sent directly, ignoring the proxy settings.

## Advanced Proxy Selection

For more granular control, the `proxies` dictionary allows you to specify proxies for specific schemes and hostnames. When selecting a proxy for a given URL, Requests searches for a key in the `proxies` dictionary in the following order:

1.  `scheme://hostname` (e.g., `https://httpbin.org`)
2.  `scheme` (e.g., `https`)
3.  `all://hostname` (e.g., `all://httpbin.org`)
4.  `all`

This allows for powerful routing rules. For instance, you could route traffic for a specific domain to one proxy while sending all other traffic to another.

```python Granular Proxy Rules icon=logos:python
proxies = {
    # Route traffic for this specific host to a SOCKS proxy
    'https://api.internal.corp': 'socks5://user:pass@host:port',

    # Route all other HTTPS traffic to a general HTTP proxy
    'https': 'http://proxy.example.com:8080'
}

# This request will use the SOCKS proxy
requests.get('https://api.internal.corp/data', proxies=proxies)

# This request will use the general HTTPS proxy
requests.get('https://httpbin.org/get', proxies=proxies)
```

Now that you've mastered proxies, you may want to learn about [SSL Certificate Verification](./advanced-usage-ssl-verification.md) or how to use [Session Objects](./advanced-usage-session-objects.md) to persist these settings across multiple requests.