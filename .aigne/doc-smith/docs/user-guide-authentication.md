# Authentication

Many web services require authentication to grant access to their resources. Requests provides a straightforward way to handle this using the `auth` parameter, supporting several common authentication schemes out of the box and allowing for custom implementations.

## Basic Authentication

Basic Authentication is a widely used, simple authentication method. It sends a username and password with your request. Requests provides a convenient shorthand for this using a `(username, password)` tuple.

```python Basic Auth with a Tuple icon=logos:python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
print(f'Response Text: {response.text}')
```

This tuple is a shortcut for the `HTTPBasicAuth` class. You can also use the class directly for more explicit code.

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

Digest Authentication is a more secure method than Basic Authentication because it doesn't send the password over the network in cleartext. Requests handles the complexity of this scheme seamlessly. To use it, you can pass an instance of the `HTTPDigestAuth` class to the `auth` parameter.

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
print(f'Response Text: {response.text}')
```

Requests will automatically handle the challenge-response handshake required for Digest Authentication.

## Proxy Authentication

If you are routing your requests through a proxy that requires authentication, you can use the `HTTPProxyAuth` class. This works similarly to `HTTPBasicAuth` but sets the `Proxy-Authorization` header.

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# Note: This is a placeholder URL for the proxy.
# Replace with your actual proxy address.
proxies = {
   'http': 'http://10.10.1.10:3128',
}

auth = HTTPProxyAuth('user', 'pass')

# This request will be sent through the proxy with authentication.
response = requests.get('https://httpbin.org/get', proxies=proxies, auth=auth)

print(f'Status Code: {response.status_code}')
```

For more details on configuring proxies, see the [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md) section.

## Custom Authentication

If you have an authentication scheme that isn't covered by the built-in methods, Requests allows you to create your own. Simply create a class that inherits from `requests.auth.AuthBase` and implement the `__call__` method. This method should take a request object and return the modified request object.

Here is an example of a custom authentication class that adds a custom header:

```python Custom Authentication Class icon=logos:python
import requests

class TokenAuth(requests.auth.AuthBase):
    """Attaches a custom token to the given Request object."""
    def __init__(self, token):
        # setup any auth-related data here
        self.token = token

    def __call__(self, r):
        # modify and return the request
        r.headers['X-TokenAuth'] = f'{self.token}'
        return r

# Usage
response = requests.get('https://httpbin.org/get', auth=TokenAuth('my-secret-token'))

print(response.json()['headers']['X-Tokenauth'])
```

This powerful feature allows you to integrate any authentication mechanism, including popular schemes like OAuth, which often have dedicated libraries that provide a Requests-compatible `AuthBase` implementation.

---

Now that you understand how to secure your requests, the next step is to learn how to handle situations when things go wrong. Continue to the [Error Handling](./user-guide-error-handling.md) section to learn about managing exceptions and bad responses.