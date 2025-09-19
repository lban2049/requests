# Authentication

Many web services require authentication, and Requests provides several ready-to-use methods for this. This guide covers the most common authentication schemes, including Basic, Digest, and custom authentication mechanisms.

## Basic Authentication

Basic authentication is a widely used, straightforward method where the username and password are sent with the request. Requests makes this incredibly simple.

You can provide the credentials by passing an `HTTPBasicAuth` instance to the `auth` parameter.

```python Basic Authentication Example icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

For convenience, Requests also provides a shorthand notation by passing a two-item tuple of `(username, password)` to the `auth` parameter.

```python Shorthand Basic Authentication icon=logos:python
import requests

response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

Behind the scenes, Requests will construct the appropriate `Authorization` header with the Base64-encoded credentials.

## Digest Authentication

Digest Authentication offers a more secure alternative to Basic Authentication by using a challenge-response mechanism that avoids sending the password in cleartext. While the process is more complex, using it with Requests is just as simple.

Simply use the `HTTPDigestAuth` class and pass it to the `auth` parameter. Requests will handle the entire multi-step authentication flow for you.

```python Digest Authentication Example icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'
response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
```

## Proxy Authentication

If you need to authenticate with an intermediate proxy server, you can use the `HTTPProxyAuth` class. This works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header instead.

This is typically used in combination with the `proxies` parameter.

```python Proxy Authentication Example icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# Note: This is a conceptual example. Replace with your actual proxy details.
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

response = requests.get('https://www.example.com', proxies=proxies, auth=proxy_auth)

print(f"Status Code: {response.status_code}")
```

## Custom Authentication

If you need to implement an authentication scheme that isn't built-in, Requests allows you to create your own. Any custom authentication handler must be a callable that takes a `Request` object as its only argument and returns the modified `Request` object.

The easiest way to create one is to inherit from `requests.auth.AuthBase` and implement the `__call__` method.

Here is an example of a custom authentication handler that adds a token to a custom header, a common pattern for API authentication.

```python Custom Token Authentication icon=logos:python
import requests

class TokenAuth(requests.auth.AuthBase):
    """Attaches a custom token to the X-API-Token header."""
    def __init__(self, token):
        # setup any auth-related data here
        self.token = token

    def __call__(self, r):
        # modify the request object to apply auth
        r.headers['X-API-Token'] = f'{self.token}'
        return r


response = requests.get('https://httpbin.org/headers', auth=TokenAuth('my-secret-api-token'))

print(response.json()['headers']['X-Api-Token'])
# 'my-secret-api-token'
```

This flexible system allows you to integrate with virtually any authentication scheme, such as OAuth1, OAuth2, Hawk, and more.

Now that you understand authentication, you may want to learn how to route your requests through an intermediary. Continue to the [Proxies](./advanced-usage-proxies.md) section to learn more.
