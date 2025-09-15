# API Reference

Welcome to the Requests API Reference. This section provides detailed information on all of the public-facing classes, methods, and objects available in the Requests library. It is intended for users who need to look up specific parameters or understand the behavior of a particular function in detail.

For practical, task-oriented examples, please refer to the [User Guide](./user-guide.md).

## Top-Level API

The easiest way to use Requests is by calling the top-level methods. These functions are wrappers around a temporary `Session` object, making them perfect for simple, one-off requests.

### `requests.request(method, url, **kwargs)`

Constructs and sends a `Request`. This is the foundational method that all other top-level request methods call.

**Parameters**

<x-field data-name="method" data-type="string" data-required="true" data-desc="HTTP method for the new Request object: GET, OPTIONS, HEAD, POST, PUT, PATCH, or DELETE."></x-field>
<x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
<x-field data-name="params" data-type="dict, list of tuples, or bytes" data-required="false" data-desc="Data to be sent in the query string of the Request."></x-field>
<x-field data-name="data" data-type="dict, list of tuples, bytes, or file-like object" data-required="false" data-desc="Data to send in the body of the Request. Used for form-encoded data."></x-field>
<x-field data-name="json" data-type="object" data-required="false" data-desc="A JSON serializable Python object to send in the body of the Request."></x-field>
<x-field data-name="headers" data-type="dict" data-required="false" data-desc="Dictionary of HTTP Headers to send with the Request."></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="Object to send with the Request."></x-field>
<x-field data-name="files" data-type="dict" data-required="false" data-desc="Dictionary of 'name': file-like-objects for multipart encoding upload."></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="Auth tuple or callable to enable Basic/Digest/Custom HTTP Auth."></x-field>
<x-field data-name="timeout" data-type="float or tuple" data-required="false" data-desc="How many seconds to wait for the server to send data before giving up. Can be a single float (for both connect and read timeouts) or a (connect, read) tuple."></x-field>
<x-field data-name="allow_redirects" data-type="bool" data-default="True" data-required="false" data-desc="Enable or disable redirection."></x-field>
<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="Dictionary mapping protocol to the URL of the proxy."></x-field>
<x-field data-name="verify" data-type="bool or string" data-default="True" data-required="false" data-desc="Controls whether to verify the server's TLS certificate. Can also be a path to a CA bundle."></x-field>
<x-field data-name="stream" data-type="bool" data-default="False" data-required="false" data-desc="If False, the response content will be immediately downloaded."></x-field>
<x-field data-name="cert" data-type="string or tuple" data-required="false" data-desc="Path to an SSL client cert file (.pem). If a tuple, ('cert', 'key') pair."></x-field>

**Returns**

<x-field data-name="response" data-type="requests.Response" data-desc="A Response object."></x-field>

**Usage**

```python icon=logos:python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req.status_code)
# 200
```

### Convenience Methods

Requests provides shorthand methods for each HTTP verb.

- `requests.get(url, params=None, **kwargs)`: Sends a GET request.
- `requests.post(url, data=None, json=None, **kwargs)`: Sends a POST request.
- `requests.put(url, data=None, **kwargs)`: Sends a PUT request.
- `requests.patch(url, data=None, **kwargs)`: Sends a PATCH request.
- `requests.delete(url, **kwargs)`: Sends a DELETE request.
- `requests.head(url, **kwargs)`: Sends a HEAD request.
- `requests.options(url, **kwargs)`: Sends an OPTIONS request.

These methods accept the same `**kwargs` as the `requests.request()` function, excluding `method`.

## Session Object

For making multiple requests to the same host, a `Session` object allows you to persist certain parameters across requests. It also reuses the underlying TCP connection, which can result in a significant performance increase.

### `requests.Session()`

A Requests session that provides cookie persistence, connection-pooling, and configuration.

**Basic Usage**

```python icon=logos:python
import requests

s = requests.Session()
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# { "cookies": { "sessioncookie": "123456789" } }
```

### Session Attributes

A `Session` object has the same attributes as a `Request` object which can be set to apply to all requests made with that session.

<x-field data-name="headers" data-type="dict" data-desc="A case-insensitive dictionary of headers to be sent on each request."></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="Default Authentication object to attach to each request."></x-field>
<x-field data-name="proxies" data-type="dict" data-desc="Dictionary of proxies to be used on each request."></x-field>
<x-field data-name="hooks" data-type="dict" data-desc="Event-handling hooks."></x-field>
<x-field data-name="params" data-type="dict" data-desc="Dictionary of querystring data to attach to each request."></x-field>
<x-field data-name="verify" data-type="bool or string" data-desc="Default SSL verification setting."></x-field>
<x-field data-name="cert" data-type="string or tuple" data-desc="Default SSL client certificate."></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar containing all cookies set on this session."></x-field>

### Session Methods

Session objects have the same methods as the top-level API (`get`, `post`, `request`, etc.).

#### `mount(prefix, adapter)`

Registers a connection adapter to a prefix. This allows you to define special transport behavior for certain services. See [Advanced Usage](./advanced-usage-adapters-and-hooks.md) for more details.

#### `close()`

Closes all adapters and as such the session, releasing any pooled connections.

## Response Object

When you make a request, Requests returns a `Response` object, which contains the server's response.

### `requests.Response` Attributes

<x-field data-name="status_code" data-type="int" data-desc="The integer status code of the response (e.g., 200, 404)."></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of the response headers."></x-field>
<x-field data-name="encoding" data-type="string" data-desc="The encoding to use to decode the response text. If None, it will be guessed."></x-field>
<x-field data-name="url" data-type="string" data-desc="The final URL location of the response after any redirects."></x-field>
<x-field data-name="history" data-type="list" data-desc="A list of Response objects from the history of the request (redirects)."></x-field>
<x-field data-name="reason" data-type="string" data-desc="The textual reason for the status code (e.g., 'OK', 'Not Found')."></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar of cookies the server sent back."></x-field>
<x-field data-name="elapsed" data-type="datetime.timedelta" data-desc="The time elapsed between sending the request and the arrival of the response."></x-field>
<x-field data-name="request" data-type="PreparedRequest" data-desc="The PreparedRequest object to which this is a response."></x-field>
<x-field data-name="ok" data-type="bool" data-desc="Returns True if status_code is less than 400, False if not."></x-field>
<x-field data-name="is_redirect" data-type="bool" data-desc="Returns True if this response is a well-formed HTTP redirect."></x-field>
<x-field data-name="content" data-type="bytes" data-desc="The content of the response, in bytes."></x-field>
<x-field data-name="text" data-type="string" data-desc="The content of the response, in Unicode."></x-field>
<x-field data-name="links" data-type="dict" data-desc="Returns the parsed header links of the response, if any."></x-field>

### `requests.Response` Methods

#### `json(**kwargs)`

Decodes the JSON response body into a Python object. Any keyword arguments are passed to `json.loads`.

#### `raise_for_status()`

Raises an `HTTPError` if the HTTP request returned an unsuccessful status code (4xx or 5xx).

#### `iter_content(chunk_size=1, decode_unicode=False)`

Iterates over the response data. When `stream=True` on the request, this avoids reading the content at once into memory.

#### `iter_lines(chunk_size=512, decode_unicode=False, delimiter=None)`

Iterates over the response data, one line at a time.

#### `close()`

Releases the connection back to the pool. This is not normally needed to be called explicitly.

## Authentication

Requests provides several built-in authentication handlers.

### `requests.auth.HTTPBasicAuth(username, password)`

Attaches HTTP Basic Authentication to a given Request object. It can be passed to the `auth` parameter.

```python icon=logos:python
from requests.auth import HTTPBasicAuth
requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
```

A shortcut for this is to pass a tuple:

```python icon=logos:python
requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
```

### `requests.auth.HTTPDigestAuth(username, password)`

Attaches HTTP Digest Authentication to a given Request object.

```python icon=logos:python
from requests.auth import HTTPDigestAuth
url = 'https://httpbin.org/digest-auth/qop/user/pass'
requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
```

## Exceptions

Requests raises exceptions for various errors. Your code should anticipate these exceptions.

| Exception | Description |
|---|---|
| `requests.exceptions.RequestException` | The base exception class for all exceptions in Requests. |
| `requests.exceptions.ConnectionError` | Raised for network-related errors (e.g., DNS failure, refused connection). |
| `requests.exceptions.HTTPError` | Raised by `raise_for_status()` for unsuccessful status codes (4xx or 5xx). |
| `requests.exceptions.Timeout` | The request timed out. This is the base class for more specific timeout exceptions. |
| `requests.exceptions.ConnectTimeout` | The request timed out while trying to connect to the remote server. |
| `requests.exceptions.ReadTimeout` | The server did not send any data in the allotted amount of time. |
| `requests.exceptions.TooManyRedirects` | The request exceeded the configured number of maximum redirections. |
| `requests.exceptions.URLRequired` | A valid URL is required to make a request. |
| `requests.exceptions.MissingSchema` | The URL scheme (e.g., `http` or `https`) is missing. |
| `requests.exceptions.InvalidURL` | The URL provided was invalid. |
| `requests.exceptions.JSONDecodeError` | Raised when `response.json()` fails to decode the response content. |
| `requests.exceptions.SSLError` | An SSL error occurred. |

## Status Code Lookup

Requests provides a convenient object for looking up status codes by name.

### `requests.codes`

This is a `LookupDict` object that allows for case-insensitive access to HTTP status codes.

**Usage**

```python icon=logos:python
import requests

print(requests.codes.ok)          # 200
print(requests.codes.not_found)   # 404
print(requests.codes['teapot'])   # 418
print(requests.codes.teapot)      # 418
```
