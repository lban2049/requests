# Exceptions

This page provides a complete listing of all public exceptions raised by the Requests library, explaining their inheritance and when they occur. All exceptions are available for import from `requests.exceptions`.

When a network or protocol error occurs, Requests will raise an exception. Understanding these exceptions allows you to build robust applications that can gracefully handle connection issues, timeouts, and invalid server responses.

## Exception Hierarchy

The following diagram illustrates the inheritance hierarchy of the exceptions in Requests. The base exception for the library is `requests.exceptions.RequestException`.

```d2
direction: down

IOError
ValueError

IOError -> RequestException

RequestException: {
  HTTPError
  URLRequired
  TooManyRedirects
  ChunkedEncodingError
  ContentDecodingError
  StreamConsumedError
  RetryError
  UnrewindableBodyError

  Timeout: {
    ReadTimeout
  }

  ConnectionError: {
    ProxyError
    SSLError
    ConnectTimeout
  }

  InvalidJSONError: {
    JSONDecodeError
  }

  InvalidURL: {
    InvalidProxyURL
  }
  
  MissingSchema
  InvalidSchema
  InvalidHeader
}

Timeout -> ConnectionError.ConnectTimeout

ValueError -> InvalidURL
ValueError -> MissingSchema
ValueError -> InvalidSchema
ValueError -> InvalidHeader
```

## Exception Classes

Below is a detailed list of all exception classes raised by the library.

| Exception | Description |
| :--- | :--- |
| `RequestException` | The base exception class for all other exceptions in this module. |
| `HTTPError` | Raised for unsuccessful HTTP responses (4xx or 5xx status codes). |
| `ConnectionError` | Raised for network-related errors (e.g., DNS failure, refused connection). |
| `ProxyError` | A specialized `ConnectionError` for proxy-specific issues. |
| `SSLError` | A specialized `ConnectionError` for SSL-related errors. |
| `Timeout` | The base class for timeout errors. Catches both `ConnectTimeout` and `ReadTimeout`. |
| `ConnectTimeout` | Raised when a timeout occurs while trying to establish a connection. |
| `ReadTimeout` | Raised when the server does not send any data in the allotted time. |
| `URLRequired` | Raised when a valid URL is not provided for a request. |
| `TooManyRedirects` | Raised when a request exceeds the configured number of maximum redirections. |
| `MissingSchema` | Raised when the URL scheme (e.g., `http` or `https`) is missing. |
| `InvalidSchema` | Raised when the provided URL scheme is invalid or unsupported. |
| `InvalidURL` | Raised when the provided URL is malformed. |
| `InvalidProxyURL` | Raised when the provided proxy URL is malformed. |
| `InvalidHeader` | Raised when an invalid value is provided for a request header. |
| `InvalidJSONError` | Base class for JSON-related errors. |
| `JSONDecodeError` | Raised when decoding a JSON response body fails. |
| `ChunkedEncodingError` | Raised when the server sends an invalid chunk in a chunked-encoded response. |
| `ContentDecodingError` | Raised when failing to decode the response content (e.g., from gzip). |
| `StreamConsumedError` | Raised when attempting to access the content of a response that has already been consumed. |
| `RetryError` | Raised when custom retry logic fails. |
| `UnrewindableBodyError` | Raised when Requests needs to rewind a request body, but cannot. |

### `class requests.exceptions.RequestException`

> There was an ambiguous exception that occurred while handling your request.

This is the base exception that all other exceptions raised by Requests inherit from. You can use it to catch any error originating from the library.

```python Catching the Base Exception
import requests

try:
    # An action that could fail
    response = requests.get('http://invalid-url-that-does-not-exist.local')
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")
```

### `class requests.exceptions.HTTPError`

> An HTTP error occurred.

This exception is raised when the `raise_for_status()` method is called on a `Response` object that has a 4xx or 5xx status code.

### `class requests.exceptions.ConnectionError`

> A Connection error occurred.

This is raised for any network-related problems, such as DNS failures or refused connections.

### `class requests.exceptions.ProxyError`

> A proxy error occurred.

This is a subclass of `ConnectionError` specifically for errors related to proxy connections.

### `class requests.exceptions.SSLError`

> An SSL error occurred.

This is a subclass of `ConnectionError` specifically for SSL handshake and verification errors.

### `class requests.exceptions.Timeout`

> The request timed out.

Catching this error will catch both `ConnectTimeout` and `ReadTimeout` errors. It is raised when a request does not complete within the specified timeout duration.

### `class requests.exceptions.ConnectTimeout`

> The request timed out while trying to connect to the remote server.

This error indicates that the connection to the server could not be established within the configured timeout. Requests that produce this error are safe to retry.

### `class requests.exceptions.ReadTimeout`

> The server did not send any data in the allotted amount of time.

This error occurs when a connection is successfully established, but the server fails to send a response within the configured read timeout.

### `class requests.exceptions.URLRequired`

> A valid URL is required to make a request.

This is raised if you attempt to make a request without providing a URL.

### `class requests.exceptions.TooManyRedirects`

> Too many redirects.

This is raised if a request exceeds the maximum number of allowed redirects, which can be configured on the `Session` object.

### `class requests.exceptions.MissingSchema`

> The URL scheme (e.g. http or https) is missing.

Raised for URLs like `'example.com/api'` instead of `'https://example.com/api'`.

### `class requests.exceptions.InvalidSchema`

> The URL scheme provided is either invalid or unsupported.

Raised for URLs with schemes that Requests does not handle, such as `'ftp://example.com'`.

### `class requests.exceptions.InvalidURL`

> The URL provided was somehow invalid.

This is a general exception for malformed URLs that don't fit the other, more specific URL-related exceptions.

### `class requests.exceptions.InvalidProxyURL`

> The proxy URL provided is invalid.

A subclass of `InvalidURL` raised specifically for malformed proxy URLs.

### `class requests.exceptions.JSONDecodeError`

> Couldn't decode the text into json

This exception is raised when you call `response.json()` but the response body does not contain valid JSON.

## Warning Classes

Requests also defines a set of custom warnings to indicate potential issues that don't necessarily warrant raising an exception.

### `class requests.exceptions.RequestsWarning`

> Base warning for Requests.

All other warnings in Requests inherit from this class.

### `class requests.exceptions.FileModeWarning`

> A file was opened in text mode, but Requests determined its binary length.

This warning indicates a potential issue when uploading files that were not opened in binary mode (`'rb'`).

### `class requests.exceptions.RequestsDependencyWarning`

> An imported dependency doesn't match the expected version range.

This warning is issued if a dependency like `urllib3` or `chardet` has an incompatible version, which could lead to unexpected behavior.