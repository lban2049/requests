# API Reference

This section provides a detailed reference for the public classes, methods, and functions in the Requests library. It is intended for developers who need a comprehensive understanding of the available tools and their specific parameters.

## Top-Level API

The most common way to use Requests is through its simple, top-level API. These functions are convenient wrappers that handle the creation and sending of requests in a single call.

### `requests.request(method, url, **kwargs)`

Constructs and sends a `Request`. This is the foundational function that all other top-level HTTP method functions call.

**Parameters**

| Parameter | Description |
|---|---|
| `method` | The HTTP method for the new `Request` object (e.g., `'GET'`, `'POST'`, `'PUT'`). |
| `url` | The URL for the new `Request` object. |
| `params` | (optional) A dictionary, list of tuples, or bytes to be sent in the query string of the `Request`. |
| `data` | (optional) A dictionary, list of tuples, bytes, or file-like object to send in the body of the `Request`. |
| `json` | (optional) A JSON-serializable Python object to send in the body of the `Request`. |
| `headers` | (optional) A dictionary of HTTP headers to send with the `Request`. |
| `cookies` | (optional) A dictionary or `CookieJar` object to send with the `Request`. |
| `files` | (optional) A dictionary for multipart encoding uploads. Format: `{'name': file-like-object}` or `{'name': ('filename', fileobj, 'content_type', custom_headers)}`. |
| `auth` | (optional) An authentication tuple or callable to enable Basic/Digest/Custom HTTP Auth. |
| `timeout` | (optional) How many seconds to wait for the server to send data. Can be a float or a `(connect_timeout, read_timeout)` tuple. |
| `allow_redirects` | (optional) A boolean to enable or disable redirection. Defaults to `True`. |
| `proxies` | (optional) A dictionary mapping protocol to the URL of the proxy. |
| `verify` | (optional) Either a boolean to control TLS certificate verification or a string path to a CA bundle. Defaults to `True`. |
| `stream` | (optional) If `False` (default), the response content will be immediately downloaded. |
| `cert` | (optional) A path to an SSL client certificate file (`.pem`) or a `('cert', 'key')` tuple. |

**Returns:** A `requests.Response` object.

### Convenience Methods

These functions are shortcuts that call `requests.request()` with the specified method.

-   `requests.get(url, params=None, **kwargs)`: Sends a GET request.
-   `requests.post(url, data=None, json=None, **kwargs)`: Sends a POST request.
-   `requests.put(url, data=None, **kwargs)`: Sends a PUT request.
-   `requests.patch(url, data=None, **kwargs)`: Sends a PATCH request.
-   `requests.delete(url, **kwargs)`: Sends a DELETE request.
-   `requests.head(url, **kwargs)`: Sends a HEAD request. `allow_redirects` is set to `False` by default.
-   `requests.options(url, **kwargs)`: Sends an OPTIONS request.

**Example:**
```python
import requests

response = requests.get('https://httpbin.org/get', params={'key': 'value'})
print(response.url)
# Output: https://httpbin.org/get?key=value
```

## Session Object

For making multiple requests to the same host, the `Session` object allows you to persist certain parameters, such as cookies and headers, across requests. It also utilizes connection pooling, which can result in a significant performance increase.

### `requests.Session()`

A Requests session that provides cookie persistence, connection-pooling, and configuration.

**Basic Usage**
```python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# The 'x-test' header is sent on both requests
s.get('https://httpbin.org/get')
s.get('https://httpbin.org/headers')
```

**Context Manager Usage**
```python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

**Session Methods**

A `Session` object has all the same HTTP method functions as the top-level API (`get`, `post`, `put`, etc.). When you call a method on a `Session` object, it uses the configuration set on that session.

**Key Attributes**

| Attribute | Description |
|---|---|
| `headers` | A `CaseInsensitiveDict` of headers to be sent on each request. |
| `cookies` | A `RequestsCookieJar` containing all cookies set on the session. |
| `auth` | Default authentication to attach to each request. |
| `proxies` | A dictionary of proxies to be used for each request. |
| `params` | A dictionary of query string data to attach to each request. |
| `verify` | Default SSL verification setting. Defaults to `True`. |
| `cert` | Default SSL client certificate. |
| `max_redirects` | Maximum number of redirects allowed. Defaults to 30. |

## Response Object

When you make a request, Requests returns a `Response` object which contains the server's response.

### `requests.Response`

This object contains the server's response to an HTTP request.

**Key Attributes and Methods**

| Attribute/Method | Description |
|---|---|
| `status_code` | The integer HTTP status code (e.g., `200`, `404`). |
| `headers` | A `CaseInsensitiveDict` of the response headers. |
| `encoding` | The encoding used to decode `r.text`. |
| `text` | The content of the response, in unicode. |
| `content` | The content of the response, in bytes. |
| `json(**kwargs)` | Decodes the response body as JSON. Raises `JSONDecodeError` on failure. |
| `ok` | A boolean that is `True` if `status_code` is less than 400. |
| `is_redirect` | A boolean that is `True` if the response is a well-formed HTTP redirect. |
| `url` | The final URL location of the response. |
| `reason` | The textual reason for the HTTP status (e.g., `'OK'`, `'Not Found'`). |
| `cookies` | A `RequestsCookieJar` of cookies the server sent back. |
| `elapsed` | A `timedelta` object representing the time elapsed between sending the request and the arrival of the response. |
| `history` | A list of `Response` objects from the history of the request (redirects). |
| `request` | The `PreparedRequest` object to which this is a response. |
| `raise_for_status()` | Raises an `HTTPError` if the HTTP request returned an unsuccessful status code (4xx or 5xx). |
| `iter_content()` | Iterates over the response data, useful for streaming large files. |
| `close()` | Releases the connection back to the pool. |

## Exceptions

Requests raises exceptions for various errors. All exceptions are available in the `requests.exceptions` module.

-   `requests.exceptions.RequestException`: The base class for all exceptions in Requests.
-   `requests.exceptions.ConnectionError`: Raised for network-related problems (e.g., DNS failure, refused connection).
-   `requests.exceptions.HTTPError`: Raised by `raise_for_status()` for unsuccessful status codes (4xx or 5xx).
-   `requests.exceptions.URLRequired`: Raised when a valid URL is not provided.
-   `requests.exceptions.TooManyRedirects`: Raised when a request exceeds the configured number of maximum redirections.
-   `requests.exceptions.ConnectTimeout`: Raised when a connection times out.
-   `requests.exceptions.ReadTimeout`: Raised when the server does not send any data in the allotted amount of time.
-   `requests.exceptions.Timeout`: The base class for both `ConnectTimeout` and `ReadTimeout`.
-   `requests.exceptions.SSLError`: Raised for SSL-related errors.
-   `requests.exceptions.ProxyError`: Raised for errors with the proxy.
-   `requests.exceptions.JSONDecodeError`: Raised when `response.json()` fails to decode the response body.

## Authentication

Requests provides several built-in authentication handlers.

-   `requests.auth.HTTPBasicAuth(username, password)`: Attaches HTTP Basic Authentication to a request.
    ```python
    from requests.auth import HTTPBasicAuth
    requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
    ```
-   `requests.auth.HTTPDigestAuth(username, password)`: Attaches HTTP Digest Authentication to a request.
-   `requests.auth.HTTPProxyAuth(username, password)`: Attaches HTTP Proxy Authentication to a request.

## Other Useful Components

-   `requests.codes`: A `LookupDict` object that provides access to HTTP status codes by common names (e.g., `requests.codes.ok` is `200`).
-   `requests.models.Request`: An object for creating a request with its parameters before it is prepared and sent.
-   `requests.models.PreparedRequest`: The object containing the exact bytes that will be sent to the server. `Session.send()` accepts this object.
-   `requests.adapters.HTTPAdapter`: A transport adapter that can be mounted to a `Session` to customize connection behavior, such as setting retry policies.