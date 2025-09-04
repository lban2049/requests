# Authentication

Many web services require authentication, and Requests provides several ready-to-use methods for this. Authentication allows you to provide credentials to verify your identity when making a request.

In Requests, authentication is handled by passing an `auth` object to the request method. The library includes built-in classes for common authentication schemes like HTTP Basic and Digest authentication, and also allows for custom authentication mechanisms.

## Basic Authentication

Basic Authentication is a widely used, straightforward method. It sends a username and password with your request. Requests provides a convenient shorthand for this using a two-item tuple.

To use Basic Authentication, pass a tuple of `(username, password)` to the `auth` parameter:

```python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

This simple tuple is a shorthand for the `HTTPBasicAuth` class. The code above is equivalent to the following:

```python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

Requests handles the necessary Base64 encoding of the username and password and adds the `Authorization` header to your request automatically.

## Digest Authentication

Digest Authentication offers a more secure alternative to Basic Authentication by using a challenge-response mechanism that avoids sending the password in cleartext.

To use Digest Authentication, you can utilize the `HTTPDigestAuth` class:

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

Requests manages the entire handshake process for you, including handling the server's nonce and constructing the correct `Authorization` header for subsequent requests to the same realm.

## Proxy Authentication

If you are routing your requests through a proxy that requires authentication, you can use the `HTTPProxyAuth` class. This works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header instead.

```python
import requests
from requests.auth import HTTPProxyAuth

# This is a fictional proxy URL. Replace with your actual proxy.
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

# Create a proxy auth object
proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# The request will be sent through the proxy with the specified authentication
response = requests.get(
    'https://httpbin.org/get',
    proxies=proxies,
    auth=proxy_auth
)

print(response.status_code)
```

## Custom Authentication

If you need an authentication scheme that isn't built-in, you can create your own. Any callable that modifies a `PreparedRequest` object can function as an authentication handler. Requests provides the `requests.auth.AuthBase` class to make this easier.

To create a custom authentication mechanism, inherit from `AuthBase` and implement the `__call__` method. This method should modify the request object (usually by adding headers) and return it.

Here is an example of a custom authentication class that adds a token to a custom header:

```python
import requests

class TokenAuth(requests.auth.AuthBase):
    """Attaches a custom token to the X-API-Token header."""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        r.headers['X-API-Token'] = f'{self.token}'
        return r

response = requests.get(
    'https://httpbin.org/get',
    auth=TokenAuth('my-secret-api-token')
)

print(response.status_code)
print(response.json()['headers']['X-Api-Token'])
# my-secret-api-token
```

This demonstrates the flexibility of the authentication system, allowing you to integrate with virtually any authentication scheme.

Now that you know how to secure your requests, the next step is to learn how to handle situations where things don't go as planned. For that, see the [Error Handling](./user-guide-error-handling.md) guide.