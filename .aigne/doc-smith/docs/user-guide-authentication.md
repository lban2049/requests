# Authentication

Many web services require authentication to access their resources. Requests provides several built-in methods for handling authentication, making it simple to secure your HTTP requests. This guide covers the most common authentication schemes, including Basic, Digest, and custom authentication mechanisms.

## Basic Authentication

Basic Authentication is a widely used and straightforward method. To use it, you can provide the `auth` parameter with a tuple containing your username and password.

```python Basic Authentication with a Tuple icon=logos:python
import requests

response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f'Status Code: {response.status_code}')
print(response.json())
```

This is a convenient shorthand. Internally, Requests creates an `HTTPBasicAuth` object. You can also construct this object yourself, which can be useful if you need to reuse the same authentication across multiple requests or in a `Session` object.

```python Using the HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('user', 'pass')
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=auth)

print(f'Status Code: {response.status_code}')
```

When basic authentication credentials are provided, Requests automatically constructs and adds the `Authorization` header to your request with the properly encoded value.

## Digest Authentication

Digest Authentication offers a more secure alternative to Basic Authentication by using a challenge-response mechanism that avoids sending the password in cleartext. Requests handles the complexity of this flow for you. To use it, simply pass an instance of `HTTPDigestAuth` to the `auth` parameter.

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'
auth = HTTPDigestAuth('user', 'pass')

response = requests.get(url, auth=auth)

print(f'Status Code: {response.status_code}')
print(response.json())
```

Requests will first send the request without authentication, receive a `401 Unauthorized` response with a `WWW-Authenticate` header from the server, and then automatically retry the request with the correct Digest authentication headers.

## Proxy Authentication

If you need to authenticate with an HTTP proxy server, you can use the `HTTPProxyAuth` class. It works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header instead.

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# Note: This is a conceptual example. You need a running proxy that requires authentication.
proxies = {
   'http': 'http://proxy.example.com:8080',
   'https': 'http://proxy.example.com:8080',
}

proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# The 'auth' parameter is used for proxy auth here because we've provided a 'proxies' dict.
# If the target server also required auth, you'd need a more advanced setup.
response = requests.get('https://httpbin.org/get', proxies=proxies, auth=proxy_auth)

print(f'Status Code: {response.status_code}')
```

## Custom Authentication

The authentication system in Requests is designed to be extensible. If you need to implement an authentication scheme that isn't built-in (like OAuth1, Hawk, or a custom token-based system), you can create your own authentication handler.

An authentication handler is simply a callable that takes a `requests.Request` object and returns the modified object. The easiest way to create one is to subclass `requests.auth.AuthBase`.

Here is an example of a simple custom authentication handler that adds a token to a custom request header.

```python Custom Authentication Handler icon=logos:python
import requests

class ApiTokenAuth(requests.auth.AuthBase):
    """Attaches API Token Authentication to the given Request object."""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # Add the custom header to the request
        r.headers['X-API-Token'] = self.token
        return r


# Use the custom auth handler with a request
response = requests.get('https://httpbin.org/headers', auth=ApiTokenAuth('my-secret-api-token'))

print(f'Status Code: {response.status_code}')
print(response.json()['headers']['X-Api-Token'])

# Expected Output:
# Status Code: 200
# my-secret-api-token
```

This modular approach allows you to encapsulate complex authentication logic into a reusable class, keeping your request-making code clean and simple.

---

Now that you've mastered authenticating your requests, the next step is to learn how to gracefully manage network problems and bad responses. Proceed to the [Error Handling](./user-guide-error-handling.md) guide to learn more.