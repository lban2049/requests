# Authentication

Many web services require authentication to grant access to resources. Requests simplifies this process by providing several built-in authentication mechanisms and a straightforward way to create custom ones. Authentication is handled by passing an `auth` object to the request method.

This guide covers the most common authentication schemes. For a deeper understanding of how Requests handles request and response cycles, you may want to review [Making a Request](./user-guide-making-a-request.md) and [Handling Responses](./user-guide-handling-responses.md).

```d2
direction: down

Request-Object: {
  label: "User's Request Object"
  shape: rectangle
  "URL, method, etc."
  "auth=(...) is set"
}

Auth-Handler: {
  label: "Auth Handler Class\n(e.g., HTTPBasicAuth)"
  shape: rectangle
}

Prepared-Request: {
  label: "PreparedRequest"
  shape: rectangle
  "Headers are modified"
}

Server: {
  shape: cylinder
}

Request-Object -> Auth-Handler: "1. Auth object is invoked"
Auth-Handler -> Prepared-Request: "2. Adds 'Authorization' header"
Prepared-Request -> Server: "3. Sent to server"
```

## Basic Authentication

Basic Authentication is a widely used, simple method that sends a username and password with your request. Requests provides a convenient shorthand for this using a two-item tuple.

To use Basic Authentication, pass a tuple of `(username, password)` to the `auth` parameter:

```python Basic Auth with a Tuple icon=logos:python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

This tuple is a shorthand for the `HTTPBasicAuth` class from the `requests.auth` module. The code above is equivalent to the following:

```python Basic Auth with HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

In both cases, Requests handles the Base64 encoding of the username and password and adds the appropriate `Authorization` header to your request.

## Digest Authentication

Digest Authentication offers a more secure alternative to Basic Authentication. It uses a challenge-response mechanism that avoids sending the password in cleartext.

To use Digest Authentication, you can utilize the `HTTPDigestAuth` class:

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
# Status Code: 200
```

Requests manages the entire handshake process for you. It automatically handles the initial `401 Unauthorized` response from the server, extracts the `nonce` and other details from the `WWW-Authenticate` header, constructs the correct `Authorization` header, and resends the request.

## Proxy Authentication

If you are routing your requests through a proxy that requires authentication, you can use the `HTTPProxyAuth` class. This works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header instead of the `Authorization` header.

```python Proxy Authentication icon=logos:python
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

If you need an authentication scheme that isn't built-in (such as a custom token-based system), you can create your own. Any callable object that modifies a `PreparedRequest` can function as an authentication handler. Requests provides the `requests.auth.AuthBase` class to make this process cleaner.

To create a custom authentication mechanism, inherit from `AuthBase` and implement the `__call__` method. This method should take the request object as an argument, modify it as needed (usually by adding headers), and return it.

Here is an example of a custom authentication class that adds a token to a custom `X-API-Token` header:

```python Custom Token Authentication icon=logos:python
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
# 200
# my-secret-api-token
```

This demonstrates the flexibility of the authentication system, allowing you to integrate with virtually any authentication scheme required by an API.

Now that you know how to secure your requests, the next step is to learn how to handle situations where things don't go as planned. For that, see the [Error Handling](./user-guide-error-handling.md) guide.