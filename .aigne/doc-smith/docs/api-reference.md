# API Reference

This section provides a detailed and comprehensive reference for all public classes, methods, and functions in the Requests library. For more practical examples and narrative-style guides, please see the [User Guide](./user-guide.md).

## Top-Level Functions

The `requests` module provides a set of top-level functions that mirror the most common HTTP methods. These are simple wrappers that manage a temporary `Session` object for you.

### `requests.request(method, url, **kwargs)`

Constructs and sends a `Request`. This is the foundational function that all other top-level functions call.

**Parameters**

| Parameter | Description |
|---|---|
| `method` | The HTTP method for the new `Request` object (e.g., `'GET'`, `'POST'`). |
| `url` | The URL for the new `Request` object. |
| `params` | (optional) A dictionary, list of tuples, or bytes to be sent in the query string. |
| `data` | (optional) A dictionary, list of tuples, bytes, or file-like object to send in the request body. |
| `json` | (optional) A JSON-serializable Python object to send in the request body. |
| `headers` | (optional) A dictionary of HTTP headers to send with the request. |
| `cookies` | (optional) A dictionary or `CookieJar` object to send with the request. |
| `files` | (optional) A dictionary for multipart encoding uploads (e.g., `{'name': file-like-object}`). |
| `auth` | (optional) An authentication object to enable Basic/Digest/Custom HTTP Auth. |
| `timeout` | (optional) The number of seconds to wait for the server to send data. Can be a float or a `(connect, read)` tuple. |
| `allow_redirects` | (optional) A boolean to enable or disable redirection. Defaults to `True`. |
| `proxies` | (optional) A dictionary mapping protocol to the URL of the proxy. |
| `verify` | (optional) A boolean to control SSL certificate verification or a string path to a CA bundle. Defaults to `True`. |
| `stream` | (optional) If `False`, the response content will be immediately downloaded. Defaults to `False`. |
| `cert` | (optional) A path to an SSL client certificate file (`.pem`). Can be a single file or a `('cert', 'key')` tuple. |

**Returns:** A `requests.Response` object.

### Convenience Functions

For convenience, Requests provides functions for common HTTP methods that call `request()` with the appropriate method argument.

- `requests.get(url, params=None, **kwargs)`: Sends a GET request.
- `requests.post(url, data=None, json=None, **kwargs)`: Sends a POST request.
- `requests.put(url, data=None, **kwargs)`: Sends a PUT request.
- `requests.patch(url, data=None, **kwargs)`: Sends a PATCH request.
- `requests.delete(url, **kwargs)`: Sends a DELETE request.
- `requests.head(url, **kwargs)`: Sends a HEAD request.
- `requests.options(url, **kwargs)`: Sends an OPTIONS request.

These functions accept the same keyword arguments as `requests.request()`.

```python Using Top-Level Functions icon=logos:python
import requests

response = requests.get('https://api.github.com/events')
print(response.status_code)

payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.post('https://httpbin.org/post', data=payload)
print(response.json())
```

## Session Object

For making multiple requests to the same host, the `Session` object allows you to persist certain parameters, such as cookies and headers, across requests. It also utilizes connection pooling from `urllib3`, which can result in a significant performance increase.

### `requests.Session()`

A Requests session that provides cookie persistence, connection-pooling, and configuration.

```python Session Usage icon=logos:python
import requests

# Using a context manager ensures the session is closed properly
with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get('https://httpbin.org/cookies')

    print(r.text)
    # '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

### Session Methods

A `Session` object has all the methods of the top-level API:
- `Session.request()`
- `Session.get()`
- `Session.post()`
- `Session.put()`
- `Session.patch()`
- `Session.delete()`
- `Session.head()`
- `Session.options()`
- `Session.send()`: Sends a `PreparedRequest`.

### Session Attributes

You can configure a `Session` object by setting its attributes before making requests:

| Attribute | Description |
|---|---|
| `headers` | A case-insensitive dictionary of headers to be sent on each request. |
| `cookies` | A `RequestsCookieJar` object containing cookies for the session. |
| `auth` | A default authentication tuple or object. |
| `proxies` | A dictionary of proxies to use for requests. |
| `hooks` | A dictionary of event-handling hooks. The only supported hook is `'response'`. |
| `params` | A dictionary of query string data to attach to each request. |
| `verify` | Default SSL verification setting (`True`, `False`, or a path to a CA bundle). |
| `cert` | Default SSL client certificate. |
| `max_redirects` | The maximum number of redirects allowed. Defaults to 30. |
| `stream` | Default for streaming response content. Defaults to `False`. |
| `trust_env` | If `True`, trusts environment settings for proxies, etc. Defaults to `True`. |
| `adapters` | A dictionary of mounted transport adapters. |

## Main Interface Objects

These are the primary objects you interact with when using Requests.

### `requests.Request(method, url, **kwargs)`

A user-created `Request` object, used to prepare a `PreparedRequest` which is sent to the server. It holds all the information for a request before it is processed.

### `requests.PreparedRequest`

The fully mutable object containing the exact bytes that will be sent to the server. You typically don't create this manually but receive it from `Session.prepare_request()` or `Request.prepare()`. Its attributes include `method`, `url`, `headers`, and `body`.

### `requests.Response`

The `Response` object contains a server's response to an HTTP request.

**Attributes**

| Attribute | Description |
|---|---|
| `status_code` | Integer code of the HTTP status (e.g., `200`, `404`). |
| `headers` | Case-insensitive dictionary of response headers. |
| `encoding` | The encoding to use when decoding `r.text`. If `None`, it will be guessed. |
| `text` | The content of the response, in Unicode. |
| `content` | The content of the response, in bytes. |
| `url` | The final URL location of the response after any redirects. |
| `history` | A list of `Response` objects from the history of the request (redirects). |
| `reason` | The textual reason of the HTTP status (e.g., `'OK'`, `'Not Found'`). |
| `cookies` | A `RequestsCookieJar` of cookies the server sent back. |
| `elapsed` | A `timedelta` object representing the time elapsed between sending the request and the arrival of the response. |
| `request` | The `PreparedRequest` object to which this is a response. |
| `ok` | A boolean that is `True` if `status_code` is less than 400. |
| `is_redirect` | A boolean that is `True` if the response is a redirect. |

**Methods**

| Method | Description |
|---|---|
| `json(**kwargs)` | Decodes the response body as a Python object if it contains valid JSON. |
| `raise_for_status()` | Raises an `HTTPError` if the HTTP request returned an unsuccessful status code (4xx or 5xx). |
| `close()` | Releases the connection back to the pool. Not usually needed when not using `stream=True`. |
| `iter_content(chunk_size=1, decode_unicode=False)` | Iterates over the response data in chunks. |
| `iter_lines(chunk_size=512, decode_unicode=False)` | Iterates over the response data, one line at a time. |

## Exceptions

Requests raises exceptions for various errors. All exceptions are available in the `requests.exceptions` module and inherit from `requests.exceptions.RequestException`.

Here is a diagram showing the exception hierarchy:

```d2 Exception Hierarchy
direction: down

RequestException: { 
  shape: class 
}

InvalidJSONError: { 
  shape: class 
}
HTTPError: { 
  shape: class 
}
ConnectionError: { 
  shape: class 
}
Timeout: { 
  shape: class 
}
URLRequired: { 
  shape: class 
}
TooManyRedirects: { 
  shape: class 
}
MissingSchema: { 
  shape: class 
}
InvalidSchema: { 
  shape: class 
}
InvalidURL: { 
  shape: class 
}
ChunkedEncodingError: { 
  shape: class 
}
ContentDecodingError: { 
  shape: class 
}
StreamConsumedError: { 
  shape: class 
}
RetryError: { 
  shape: class 
}
UnrewindableBodyError: { 
  shape: class 
}

RequestException -> InvalidJSONError
RequestException -> HTTPError
RequestException -> ConnectionError
RequestException -> Timeout
RequestException -> URLRequired
RequestException -> TooManyRedirects
RequestException -> MissingSchema
RequestException -> InvalidSchema
RequestException -> InvalidURL
RequestException -> ChunkedEncodingError
RequestException -> ContentDecodingError
RequestException -> StreamConsumedError
RequestException -> RetryError
RequestException -> UnrewindableBodyError

JSONDecodeError: { 
  shape: class 
}
ProxyError: { 
  shape: class 
}
SSLError: { 
  shape: class 
}
ReadTimeout: { 
  shape: class 
}
ConnectTimeout: { 
  shape: class 
}

InvalidJSONError -> JSONDecodeError
ConnectionError -> ProxyError
ConnectionError -> SSLError
Timeout -> ReadTimeout

ConnectionError -> ConnectTimeout
Timeout -> ConnectTimeout
```

**Common Exceptions**

- `requests.exceptions.RequestException`: The base exception class from which all other exceptions in the library inherit.
- `requests.exceptions.ConnectionError`: Raised for network-related problems, such as DNS failure or a refused connection.
- `requests.exceptions.HTTPError`: Raised by the `raise_for_status()` method for unsuccessful status codes (4xx or 5xx).
- `requests.exceptions.Timeout`: Raised when a request times out. This is a base class for more specific timeouts.
- `requests.exceptions.ConnectTimeout`: Raised when a timeout occurs while trying to connect to the remote server.
- `requests.exceptions.ReadTimeout`: Raised when the server does not send any data in the allotted amount of time.
- `requests.exceptions.TooManyRedirects`: Raised when a request exceeds the configured number of maximum redirections.

```python Handling Exceptions icon=logos:python
import requests

try:
    response = requests.get('https://example.com/nonexistent', timeout=1)
    response.raise_for_status() # Raises an HTTPError for 404
except requests.exceptions.Timeout:
    print('The request timed out')
except requests.exceptions.HTTPError as err:
    print(f'HTTP error occurred: {err}')
except requests.exceptions.RequestException as err:
    print(f'An error occurred: {err}')
```

## Authentication

Requests provides several built-in authentication handlers. These are passed to the `auth` parameter in a request.

- `requests.auth.HTTPBasicAuth(username, password)`: Attaches HTTP Basic Authentication to a request.
- `requests.auth.HTTPProxyAuth(username, password)`: Attaches HTTP Proxy Authentication to a request.
- `requests.auth.HTTPDigestAuth(username, password)`: Attaches HTTP Digest Authentication to a request.
- `requests.auth.AuthBase`: The base class for creating custom authentication schemes.

```python Basic Authentication icon=logos:python
from requests.auth import HTTPBasicAuth

# Using the class explicitly
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
print(response.status_code)

# A convenient shorthand is to pass a tuple
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
print(response.status_code)
```

## Lower-Level Classes & Objects

These components provide advanced control and form the building blocks of the library.

- **`requests.adapters.HTTPAdapter`**: A transport adapter that allows you to configure connection pooling, retries, and other low-level HTTP settings. You can mount an adapter to a `Session` object using `Session.mount()`.
- **`requests.structures.CaseInsensitiveDict`**: A dictionary-like object that is case-insensitive for key lookups. Used for request and response headers.
- **`requests.cookies.RequestsCookieJar`**: A `CookieJar` that also exposes a dict-like interface for managing cookies.
- **`requests.codes`**: A lookup object that provides access to HTTP status codes by their common names (e.g., `requests.codes.ok` is `200`, `requests.codes.not_found` is `404`).

```python Using Status Codes icon=logos:python
import requests

response = requests.get('https://httpbin.org/status/418')
if response.status_code == requests.codes.im_a_teapot:
    print("I'm a teapot!")
```