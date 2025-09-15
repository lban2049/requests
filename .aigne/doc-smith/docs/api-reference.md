# API Reference

This document provides a detailed reference to the public classes, methods, and functions in the Requests library. It is intended for developers who need a comprehensive understanding of the library's capabilities.

For practical examples and use-case-driven guides, please see the [User Guide](./user-guide.md).

## Main Interface

The easiest way to use Requests is through the methods provided directly by the top-level `requests` package.

### `requests.request()`

This is the foundational function that all other request methods (`get`, `post`, etc.) are built upon. It allows you to construct and send any type of HTTP request.

```python Request function icon=logos:python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req)
# <Response [200]>
```

**Parameters**

<x-field data-name="method" data-type="string" data-required="true" data-desc="HTTP method for the new Request object: GET, OPTIONS, HEAD, POST, PUT, PATCH, or DELETE."></x-field>
<x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
<x-field data-name="params" data-type="dict, list, or bytes" data-required="false" data-desc="Data to be sent in the query string of the Request."></x-field>
<x-field data-name="data" data-type="dict, list, bytes, or file-like object" data-required="false" data-desc="Data to send in the body of the Request."></x-field>
<x-field data-name="json" data-type="object" data-required="false" data-desc="A JSON-serializable Python object to send in the body of the Request."></x-field>
<x-field data-name="headers" data-type="dict" data-required="false" data-desc="Dictionary of HTTP Headers to send with the Request."></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="Cookies to send with the Request."></x-field>
<x-field data-name="files" data-type="dict" data-required="false" data-desc="Dictionary for multipart encoding upload, e.g., {'name': file-like-object}."></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="Authentication object to enable Basic/Digest/Custom HTTP Auth."></x-field>
<x-field data-name="timeout" data-type="float or tuple" data-required="false" data-desc="Seconds to wait for the server to send data before giving up. Can be a single float or a (connect, read) tuple."></x-field>
<x-field data-name="allow_redirects" data-type="bool" data-default="true" data-required="false" data-desc="Enable or disable redirection. Defaults to True."></x-field>
<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="Dictionary mapping protocol to the URL of the proxy."></x-field>
<x-field data-name="verify" data-type="bool or string" data-default="true" data-required="false" data-desc="Controls TLS certificate verification. Can be a boolean or a path to a CA bundle."></x-field>
<x-field data-name="stream" data-type="bool" data-default="false" data-required="false" data-desc="If False, the response content will be immediately downloaded."></x-field>
<x-field data-name="cert" data-type="string or tuple" data-required="false" data-desc="Path to an SSL client cert file (.pem) or a ('cert', 'key') tuple."></x-field>

**Returns**

<x-field data-name="response" data-type="requests.Response" data-desc="A Response object containing the server's response."></x-field>

### Convenience Methods

Requests provides shorthand methods for all common HTTP verbs.

- `requests.get(url, params=None, **kwargs)`: Sends a GET request.
- `requests.post(url, data=None, json=None, **kwargs)`: Sends a POST request.
- `requests.put(url, data=None, **kwargs)`: Sends a PUT request.
- `requests.patch(url, data=None, **kwargs)`: Sends a PATCH request.
- `requests.delete(url, **kwargs)`: Sends a DELETE request.
- `requests.head(url, **kwargs)`: Sends a HEAD request.
- `requests.options(url, **kwargs)`: Sends an OPTIONS request.

These methods accept the same arguments as `requests.request()`, with the `method` parameter pre-filled.

```python Convenience Methods icon=logos:python
import requests

# GET request with URL parameters
response = requests.get('https://httpbin.org/get', params={'key': 'value'})

# POST request with a JSON body
response = requests.post('https://httpbin.org/post', json={'user': 'kenneth'})

print(response.status_code)
# 200
```

## Session Object

For making multiple requests to the same host, the `Session` object allows you to persist certain parameters, such as cookies and headers, across requests. It also uses a connection pool, which can result in a significant performance increase.

```python Session Object icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# Both requests will have the 'x-test' header
s.get('https://httpbin.org/headers')
s.get('https://httpbin.org/headers')
```

### `requests.Session`

A Requests session that provides cookie persistence, connection-pooling, and configuration.

#### Session Attributes

<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of headers sent with every request from this session."></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar containing all cookies set on this session."></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="Default authentication object to attach to every request."></x-field>
<x-field data-name="proxies" data-type="dict" data-desc="Dictionary of proxies to use for each request."></x-field>
<x-field data-name="params" data-type="dict" data-desc="Dictionary of query string data to attach to each request."></x-field>
<x-field data-name="verify" data-type="bool or string" data-desc="Default SSL verification setting."></x-field>
<x-field data-name="cert" data-type="string or tuple" data-desc="Default SSL client certificate."></x-field>
<x-field data-name="max_redirects" data-type="int" data-default="30" data-desc="Maximum number of redirects allowed."></x-field>

#### Session Methods

The `Session` object has the same request methods as the top-level API (`get`, `post`, `request`, etc.). Additionally, it provides the following methods:

- `prepare_request(request)`: Prepares a `Request` object with the session's settings (cookies, headers, etc.), returning a `PreparedRequest`.
- `send(request, **kwargs)`: Sends a `PreparedRequest` object.
- `mount(prefix, adapter)`: Registers a Transport Adapter to a URL prefix.
- `close()`: Closes all adapters and the session.

## Core Objects

These are the primary objects that power Requests.

### `requests.Request`

Represents a user-created HTTP request. It is typically created and then passed to `Session.prepare_request()`.

**Constructor Parameters**

<x-field data-name="method" data-type="string" data-desc="HTTP method."></x-field>
<x-field data-name="url" data-type="string" data-desc="URL for the request."></x-field>
<x-field data-name="headers" data-type="dict" data-desc="Dictionary of headers."></x-field>
<x-field data-name="files" data-type="dict" data-desc="Dictionary of files for multipart upload."></x-field>
<x-field data-name="data" data-type="dict, list, bytes, or file-like" data-desc="Request body."></x-field>
<x-field data-name="json" data-type="object" data-desc="JSON data for the request body."></x-field>
<x-field data-name="params" data-type="dict" data-desc="URL parameters."></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="Auth handler."></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-desc="Cookies to attach to the request."></x-field>

### `requests.PreparedRequest`

Represents a fully prepared request, containing the exact bytes that will be sent to the server. You should not instantiate this class manually.

**Attributes**

<x-field data-name="method" data-type="string" data-desc="The HTTP verb."></x-field>
<x-field data-name="url" data-type="string" data-desc="The full URL."></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="The request headers."></x-field>
<x-field data-name="body" data-type="bytes or file-like" data-desc="The request body."></x-field>

### `requests.Response`

Contains the server's response to an HTTP request.

**Attributes**

<x-field data-name="status_code" data-type="int" data-desc="The integer representation of the HTTP status code (e.g., 200, 404)."></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of the response headers."></x-field>
<x-field data-name="encoding" data-type="string" data-desc="The encoding used to decode `response.text`."></x-field>
<x-field data-name="url" data-type="string" data-desc="The final URL location of the response, after any redirects."></x-field>
<x-field data-name="history" data-type="list[Response]" data-desc="A list of Response objects from the history of the request (redirects)."></x-field>
<x-field data-name="reason" data-type="string" data-desc="The textual reason of the HTTP status (e.g., 'OK', 'Not Found')."></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar of cookies the server sent back."></x-field>
<x-field data-name="elapsed" data-type="timedelta" data-desc="The time elapsed between sending the request and the arrival of the response."></x-field>
<x-field data-name="request" data-type="PreparedRequest" data-desc="The PreparedRequest object to which this is a response."></x-field>
<x-field data-name="ok" data-type="bool" data-desc="Returns True if `status_code` is less than 400, False if not."></x-field>
<x-field data-name="is_redirect" data-type="bool" data-desc="True if this response is a well-formed HTTP redirect."></x-field>
<x-field data-name="content" data-type="bytes" data-desc="The content of the response, in bytes."></x-field>
<x-field data-name="text" data-type="string" data-desc="The content of the response, in Unicode."></x-field>
<x-field data-name="links" data-type="dict" data-desc="Returns the parsed header links of the response, if any."></x-field>

**Methods**

<x-field data-name="json(**kwargs)" data-type="method" data-desc="Decodes the JSON response body into a Python object."></x-field>
<x-field data-name="iter_content(chunk_size=1, decode_unicode=False)" data-type="method" data-desc="Iterates over the response data. Avoids reading the content at once into memory."></x-field>
<x-field data-name="iter_lines(chunk_size=512, decode_unicode=False)" data-type="method" data-desc="Iterates over the response data, one line at a time."></x-field>
<x-field data-name="raise_for_status()" data-type="method" data-desc="Raises an HTTPError if the HTTP request returned an unsuccessful status code."></x-field>
<x-field data-name="close()" data-type="method" data-desc="Releases the connection back to the pool."></x-field>

## Authentication

Requests provides several built-in authentication handlers.

- `requests.auth.HTTPBasicAuth(username, password)`: Attaches HTTP Basic Authentication to a request.
- `requests.auth.HTTPProxyAuth(username, password)`: Attaches HTTP Proxy Authentication to a request.
- `requests.auth.HTTPDigestAuth(username, password)`: Attaches HTTP Digest Authentication to a request.

## Exceptions

Requests raises exceptions for various errors. All exceptions are available in the `requests.exceptions` module and inherit from `requests.exceptions.RequestException`.

| Exception | Description |
|---|---|
| `RequestException` | The base class for all Requests exceptions. |
| `ConnectionError` | Raised for network-related problems (e.g., DNS failure, refused connection). |
| `HTTPError` | Raised by `response.raise_for_status()` for unsuccessful status codes (4xx or 5xx). |
| `ProxyError` | Raised for issues with the proxy server. |
| `SSLError` | Raised for SSL-related errors. |
| `Timeout` | The base class for timeout exceptions. |
| `ConnectTimeout` | Raised when a connection times out. |
| `ReadTimeout` | Raised when the server does not send any data in the allotted time. |
| `URLRequired` | Raised when a valid URL is not provided. |
| `TooManyRedirects` | Raised when a request exceeds the configured number of maximum redirections. |
| `MissingSchema` | Raised if the URL is missing a scheme (e.g., `http://`). |
| `InvalidURL` | Raised if the URL is malformed. |
| `JSONDecodeError` | Raised when `response.json()` fails to decode the response body. |

## Transport Adapters

Transport Adapters provide the mechanism for defining how Requests interacts with a transport protocol. The most common one is `HTTPAdapter`.

### `requests.adapters.HTTPAdapter`

The built-in HTTP Adapter for `urllib3`.

**Constructor Parameters**

<x-field data-name="pool_connections" data-type="int" data-default="10" data-desc="The number of urllib3 connection pools to cache."></x-field>
<x-field data-name="pool_maxsize" data-type="int" data-default="10" data-desc="The maximum number of connections to save in the pool."></x-field>
<x-field data-name="max_retries" data-type="int or Retry" data-default="0" data-desc="The maximum number of retries each connection should attempt."></x-field>
<x-field data-name="pool_block" data-type="bool" data-default="false" data-desc="Whether the connection pool should block for connections."></x-field>

## Other Utilities

### `requests.status_codes`

A lookup object that provides access to HTTP status codes by their common names.

```python Status Codes icon=logos:python
import requests

print(requests.codes.ok) # 200
print(requests.codes.not_found) # 404
print(requests.codes['im_a_teapot']) # 418
```

### `requests.structures.CaseInsensitiveDict`

A dictionary-like object that is case-insensitive for key lookups. This is used for headers.

```python CaseInsensitiveDict icon=logos:python
from requests.structures import CaseInsensitiveDict

headers = CaseInsensitiveDict()
headers['Accept'] = 'application/json'

print(headers['accept']) # 'application/json'
```