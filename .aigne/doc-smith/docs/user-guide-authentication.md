# Authentication

Many web services require authentication to access their data. Requests provides several ready-to-use authentication methods and a flexible system for creating custom authentication schemes. Authentication is passed to request methods using the `auth` parameter.

This guide covers the most common built-in authentication types. For a deeper dive into more complex scenarios, you might also want to consult the [Advanced Usage](./advanced-usage.md) section.

## Basic Authentication

Basic authentication is a widely used, straightforward method that relies on sending a username and password with your request. While it's simple, it's important to use it only over HTTPS to ensure the credentials are encrypted.

The most convenient way to provide basic authentication is by passing a `(username, password)` tuple to the `auth` parameter.

```python Basic Auth with a Tuple icon=logos:python
import requests

# Using httpbin's basic-auth endpoint, which expects 'user' and 'pass' as credentials.
response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# A successful authentication will return 200 OK.
if response.status_code == 200:
    print(f'Response JSON: {response.json()}')
else:
    print(f'Authentication failed. Reason: {response.reason}')

```

Under the hood, this tuple is a shortcut for creating an `HTTPBasicAuth` object. You can also use the class directly for a more explicit approach, which behaves identically.

```python Basic Auth with HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
```

## Digest Authentication

Digest authentication is a more secure challenge-response alternative to Basic authentication, as it does not send the password in cleartext. Requests seamlessly handles the complexity of this mechanism for you.

To use Digest authentication, import `HTTPDigestAuth` and pass an instance of it to the `auth` parameter.

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

# httpbin's digest-auth endpoint
url = 'https://httpbin.org/digest-auth/qop/user/pass'
response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
if response.ok:
    print(f'Response JSON: {response.json()}')
```

## Proxy Authentication

If you need to authenticate with an intermediary proxy server, you can use `HTTPProxyAuth`. This works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header instead of the `Authorization` header.

This authentication method should be used in conjunction with the `proxies` parameter, which is detailed further in the [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md) section.

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# This is a conceptual example. You would need a running proxy that requires authentication.
proxies = {
   "http": "http://proxy.example.com:8080",
   "https": "https://proxy.example.com:8080",
}

# This auth object provides credentials for the proxy server.
proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# The 'auth' parameter is used for the proxy, not the destination server.
# Note: This will fail without a real, configured proxy.
try:
    response = requests.get("https://httpbin.org/get", proxies=proxies, auth=proxy_auth)
    print(response.status_code)
except requests.exceptions.ProxyError as e:
    print(f'Failed to connect to proxy: {e}')
```

## Other Authentication Schemes

Requests features a modular authentication system. While it ships with the common schemes, other authentication types like OAuth1 and OAuth2 are provided by third-party libraries.

Creating your own authentication mechanism is straightforward. You can create a class that inherits from `requests.auth.AuthBase` or simply a callable that takes a `Request` object and returns it after modification. This is useful for implementing custom schemes, such as token-based authentication.

Here is an example of a custom authentication class for a simple token-based API.

```python Custom Authentication icon=logos:python
import requests
from requests.auth import AuthBase

class TokenAuth(AuthBase):
    """Attaches a custom token to the Authorization header."""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # Modify the request `r` by adding the Authorization header.
        r.headers['Authorization'] = f'Token {self.token}'
        return r

# Use the custom auth handler
response = requests.get('https://httpbin.org/headers', auth=TokenAuth('my-secret-token-123'))

print(response.json())
```

---

Now that you've secured your requests, you might encounter issues. Let's learn how to handle them in the [Error Handling](./user-guide-error-handling.md) section.
