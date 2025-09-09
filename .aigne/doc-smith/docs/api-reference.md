# API Reference

This section provides a detailed and comprehensive reference for all public classes, methods, and functions in the Requests library. It is intended for developers who need to understand the specifics of the library's API.

For practical examples and common use cases, please refer to the [User Guide](./user-guide.md).

## Top-Level Interface

The `requests` module provides a set of top-level functions that mirror the most common HTTP request methods. These functions are simple wrappers around the `requests.request` function.

### `requests.request()`

This is the central function from which all other HTTP method functions are derived. It constructs and sends a `Request`.

```python
requests.request(method, url, **kwargs)
```

**Parameters**

| Parameter | Description |
|---|---|
| `method` | The HTTP method for the new `Request` object: `GET`, `OPTIONS`, `HEAD`, `POST`, `PUT`, `PATCH`, or `DELETE`. |
| `url` | URL for the new `Request` object. |
| `params` | (optional) A dictionary, list of tuples, or bytes to send in the query string for the `Request`. |
| `data` | (optional) A dictionary, list of tuples, bytes, or a file-like object to send in the body of the `Request`. |
| `json` | (optional) A JSON-serializable Python object to send in the body of the `Request`. |
| `headers` | (optional) A dictionary of HTTP Headers to send with the `Request`. |
| `cookies` | (optional) A dictionary or `CookieJar` object to send with the `Request`. |
| `files` | (optional) A dictionary for multipart encoding uploads. E.g., `{'name': file-like-object}`. |
| `auth` | (optional) An authentication tuple or object to enable Basic/Digest/Custom HTTP Auth. |
| `timeout` | (optional) The number of seconds to wait for the server to send data before giving up. Can be a float, or a `(connect_timeout, read_timeout)` tuple. |
| `allow_redirects` | (optional) A boolean. Set to `True` to enable GET/OPTIONS/POST/PUT/PATCH/DELETE/HEAD redirection. Defaults to `True`. |
| `proxies` | (optional) A dictionary mapping protocol to the URL of the proxy. |
| `verify` | (optional) Either a boolean (controls TLS certificate verification) or a string (path to a CA bundle). Defaults to `True`. |
| `stream` | (optional) If `False` (default), the response content will be immediately downloaded. |
| `cert` | (optional) If a string, the path to an SSL client cert file (.pem). If a tuple, `('cert', 'key')`. |

**Returns**

- A `requests.Response` object.

**Usage**

```python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req.status_code)
# 200
```

### Convenience Methods

For convenience, Requests provides functions for each HTTP method:

- **`requests.get(url, params=None, **kwargs)`**: Sends a GET request.
- **`requests.post(url, data=None, json=None, **kwargs)`**: Sends a POST request.
- **`requests.put(url, data=None, **kwargs)`**: Sends a PUT request.
- **`requests.patch(url, data=None, **kwargs)`**: Sends a PATCH request.
- **`requests.delete(url, **kwargs)`**: Sends a DELETE request.
- **`requests.head(url, **kwargs)`**: Sends a HEAD request.
- **`requests.options(url, **kwargs)`**: Sends an OPTIONS request.

## Session Object

The `Session` object allows you to persist certain parameters across requests. It also persists cookies over all requests made from the Session instance and uses `urllib3`'s connection pooling. If you are making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

```python
requests.Session()
```

**Usage**

```python
import requests

s = requests.Session()
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

### Session Methods

The `Session` object has the same method interface as the top-level `requests` module:

- `session.request(method, url, **kwargs)`
- `session.get(url, **kwargs)`
- `session.post(url, **kwargs)`
- `session.put(url, **kwargs)`
- `session.patch(url, **kwargs)`
- `session.delete(url, **kwargs)`
- `session.head(url, **kwargs)`
- `session.options(url, **kwargs)`

Other useful methods include:

- **`session.mount(prefix, adapter)`**: Registers a connection adapter to a prefix. This allows you to define special transport behavior for specific services.
- **`session.close()`**: Closes all adapters and the session.

## Response Object

Every time you make a call with Requests, you receive a `Response` object. This object contains the server's response to your HTTP request.

### Response Attributes

| Attribute | Description |
|---|---|
| `status_code` | Integer representation of the HTTP status code (e.g., `200`, `404`). |
| `headers` | A case-insensitive dictionary of the response headers. |
| `encoding` | The encoding used to decode `r.text`. |
| `text` | The content of the response, in Unicode. |
| `content` | The content of the response, in bytes. |
| `json()` | A method that returns the JSON-decoded content of the response, if any. |
| `url` | The final URL location of the response (after any redirects). |
| `ok` | A boolean that is `True` if `status_code` is less than 400, `False` otherwise. |
| `reason` | The textual reason of the HTTP status (e.g., "OK", "Not Found"). |
| `cookies` | A `RequestsCookieJar` of cookies the server sent back. |
| `elapsed` | A `timedelta` object representing the time elapsed between sending the request and the arrival of the response. |
| `request` | The `PreparedRequest` object to which this is a response. |
| `history` | A list of `Response` objects from the history of the request (redirects). |

### Response Methods

| Method | Description |
|---|---|
| `json(**kwargs)` | Decodes the response body as a Python object. Raises `requests.exceptions.JSONDecodeError` if the response body does not contain valid JSON. |
| `raise_for_status()` | Raises an `HTTPError` if the HTTP request returned an unsuccessful status code (4xx or 5xx). |
| `iter_content(chunk_size=1, decode_unicode=False)` | Iterates over the response data, useful for streaming large responses. |
| `iter_lines(chunk_size=512, decode_unicode=False, delimiter=None)` | Iterates over the response data, one line at a time. |
| `close()` | Releases the connection back to the pool. |

## Exceptions

Requests raises exceptions for various errors. All exceptions are subclasses of `requests.exceptions.RequestException`.

| Exception | Description |
|---|---|
| `RequestException` | The base exception for any ambiguous issue that occurred. |
| `ConnectionError` | Raised for network-related problems (e.g., DNS failure, refused connection). |
| `HTTPError` | Raised by `response.raise_for_status()` for unsuccessful status codes (4xx or 5xx). |
| `Timeout` | The request timed out. This is a parent for `ConnectTimeout` and `ReadTimeout`. |
| `ConnectTimeout` | The request timed out while trying to connect to the remote server. |
| `ReadTimeout` | The server did not send any data in the allotted amount of time. |
| `TooManyRedirects` | The request exceeded the configured number of maximum redirections. |
| `MissingSchema` | The URL scheme (e.g., `http` or `https`) is missing. |
| `InvalidURL` | The URL provided was somehow invalid. |
| `JSONDecodeError` | Raised when `response.json()` fails to decode the response content. |

## Authentication

Requests provides several built-in authentication handlers.

### `HTTPBasicAuth`

Attaches HTTP Basic Authentication to a request.

```python
from requests.auth import HTTPBasicAuth
requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
```

A shortcut is to pass a tuple `(username, password)` to the `auth` parameter:

```python
requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
```

### `HTTPDigestAuth`

Attaches HTTP Digest Authentication to a request.

```python
from requests.auth import HTTPDigestAuth
url = 'https://httpbin.org/digest-auth/qop/user/pass'
requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
```

## Other Classes & Objects

- **`requests.Request`**: A user-created object used to prepare a `PreparedRequest` which is then sent to the server.
- **`requests.PreparedRequest`**: A fully prepared request object, containing the exact bytes that will be sent to the server. You typically don't create this manually.
- **`requests.structures.CaseInsensitiveDict`**: A case-insensitive dictionary-like object, used for headers.
- **`requests.cookies.RequestsCookieJar`**: A `CookieJar` that also acts like a dictionary, used for `session.cookies` and `response.cookies`.
- **`requests.status_codes.codes`**: A special lookup object that provides access to HTTP status codes by name (e.g., `requests.codes.ok` is `200`).