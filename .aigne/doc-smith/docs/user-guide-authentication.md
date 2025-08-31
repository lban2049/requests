# Authentication

Many web services require authentication to grant access to resources. Requests simplifies this by supporting various authentication schemes directly through the `auth` parameter.

## Basic Authentication

HTTP Basic Authentication is a widely used, straightforward authentication method. To use it with Requests, you can provide a tuple of `(username, password)` to the `auth` parameter.

```python
import requests

# Using the tuple shorthand for Basic Auth
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

This tuple is a convenient shorthand. Internally, Requests converts it into an `HTTPBasicAuth` object from the `requests.auth` module. You can also create and use this object directly for more explicit code.

```python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass', 
    auth=HTTPBasicAuth('user', 'pass')
)

print(f"Status Code: {response.status_code}")
# Status Code: 200
```
When you use Basic Authentication, Requests automatically constructs and adds the `Authorization` header to your request with the properly encoded credentials.

## Digest Authentication

Digest Authentication offers a more secure alternative to Basic Authentication by using a challenge-response mechanism that avoids sending the password in cleartext. Using it is just as simple as using Basic Auth.

First, import `HTTPDigestAuth` and then pass an instance of it to the `auth` parameter.

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f"Status Code: {response.status_code}")
# Status Code: 200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

Requests handles the entire challenge-response flow for you. The process involves an initial unauthorized request which receives a `401` response from the server. Requests then uses information from the server's `WWW-Authenticate` header to construct a second, authenticated request.

Here is a diagram illustrating the Digest Authentication flow:

```d2
shape: sequence_diagram
direction: down

Client: "Your Application"
Server: "Web Service"

Client -> Server: "GET /resource (no auth)"
Server -> Client: "401 Unauthorized\nWWW-Authenticate: Digest, nonce=..."

Client: {
  note: "Calculates response using credentials & server nonce"
}

Client -> Server: "GET /resource\nAuthorization: Digest, response=..."
Server -> Client: "200 OK"

```

## Custom Authentication

Requests features a pluggable authentication system, allowing you to implement authentication schemes that are not built-in. You can create a custom authentication handler by creating a callable class that modifies the `Request` object before it is sent.

The simplest way is to inherit from `requests.auth.AuthBase` and implement the `__call__` method. This method receives the `PreparedRequest` object, should modify it as needed (e.g., by adding custom headers), and must return the modified object.

Here is an example of a custom handler for a token-based authentication scheme:

```python
import requests
from requests.auth import AuthBase

class TokenAuth(AuthBase):
    """Attaches a custom token to the Authorization header."""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # Modify the request `r` by adding the Authorization header
        r.headers['Authorization'] = f'Token {self.token}'
        return r

# Use the custom auth handler
response = requests.get('https://httpbin.org/headers', auth=TokenAuth('12345abcde'))

print(response.json())

# Expected Response shows the custom Authorization header:
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Token 12345abcde", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/x.x.x", 
#     "X-Amzn-Trace-Id": "..."
#   }
# }
```

This modular approach provides the flexibility to integrate with any custom or complex authentication protocol.

---

Now that you can authenticate your requests, the next step is to learn how to manage situations where things don't go as planned. Continue to the [Error Handling](./user-guide-error-handling.md) guide for more details.
