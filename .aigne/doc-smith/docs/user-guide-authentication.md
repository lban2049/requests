# Authentication

Many web services require authentication to access their resources. Requests simplifies this by providing several built-in authentication mechanisms and a straightforward way to use them. Authentication is typically passed to the `auth` parameter of a request.

## Basic Authentication

Basic Authentication is a widely used, simple authentication scheme. With Requests, you can supply your credentials by passing a two-item tuple of `(username, password)` to the `auth` parameter.

```python
import requests
from requests.auth import HTTPBasicAuth

# Using a tuple as a shorthand
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(response.status_code)
# 200
```

This shorthand is convenient for quick use. Under the hood, Requests converts this tuple into an `HTTPBasicAuth` object. You can also create and pass an instance of `HTTPBasicAuth` directly, which can be useful if you want to reuse the authentication object.

```python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('user', 'pass')
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=auth)

print(response.status_code)
# 200
```

## Digest Authentication

Digest Authentication is another common form of HTTP authentication that provides a more secure way to transmit credentials than Basic Authentication. Using it is just as simple.

To use Digest Authentication, you need to import `HTTPDigestAuth` and pass an instance of it to the `auth` parameter.

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(response.status_code)
# 200
```

Requests will automatically handle the multi-step challenge-response process required for Digest Authentication.

## Proxy Authentication

If you are routing your requests through a proxy that requires authentication, Requests has you covered. You can provide proxy credentials using the `HTTPProxyAuth` helper.

```python
import requests
from requests.auth import HTTPProxyAuth

proxies = {
   'http': 'http://proxy.example.com:8080',
   'https': 'https://proxy.example.com:8080',
}

# Assuming the proxy requires authentication
auth = HTTPProxyAuth('proxy_user', 'proxy_pass')

response = requests.get('https://httpbin.org/get', proxies=proxies, auth=auth)

print(response.status_code)
```

This will send the appropriate `Proxy-Authorization` header with your request.

## Other Authentication Schemes

Requests is designed with extensibility in mind. If you need to implement a more complex authentication scheme (like OAuth), you can create your own custom authentication handler. Any callable object that accepts a `Request` object and returns a modified `Request` object can be used. This allows for integration with virtually any authentication mechanism.

---

Now that you know how to authenticate your requests, it's important to understand how to manage potential problems. Continue to the next section to learn about [Error Handling](./user-guide-error-handling.md).