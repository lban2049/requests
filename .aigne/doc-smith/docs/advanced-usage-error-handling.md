# Error Handling

When working with network requests, it's crucial to anticipate and handle potential issues, from network failures to server errors. The Requests library provides a comprehensive set of exceptions to help you write robust and reliable code. All exceptions specific to the library are subclasses of `requests.exceptions.RequestException`.

## Handling HTTP Status Code Errors

A common scenario is handling responses with error status codes (4xx for client errors, 5xx for server errors). While Requests doesn't automatically raise an exception for these, you can use the `Response.raise_for_status()` method to do so.

If the response status code is between 400 and 599, this method will raise an `HTTPError`.

```python Handling HTTP Errors icon=logos:python
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

try:
    response = requests.get(url)

    # If the response was successful, no exception will be raised
    response.raise_for_status()
except HTTPError as http_err:
    print(f'HTTP error occurred: {http_err}')  # Python 3.6+
    print(f'Status Code: {http_err.response.status_code}')
except Exception as err:
    print(f'Other error occurred: {err}')  # Python 3.6+
else:
    print('Success!')
```

## Handling Connection and Network Errors

For network-level problems like DNS failures, refused connections, or other connectivity issues, Requests will raise a `ConnectionError`.

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://this-is-not-a-real-domain.org')
except ConnectionError as e:
    print(f'A connection error occurred: {e}')
```

## Handling Timeouts

If a request takes too long to complete, a `Timeout` exception is raised. This single exception conveniently catches both connection timeouts and read timeouts.

- `ConnectTimeout`: Occurs if establishing a connection to the remote server times out.
- `ReadTimeout`: Occurs if the server fails to send data within the specified time.

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # Use a very short timeout to trigger the exception
    response = requests.get('https://httpbin.org/delay/5', timeout=1)
except Timeout:
    print('The request timed out')
```

## Handling Invalid URLs

If the URL provided is malformed (e.g., missing the schema like `http://`), Requests will raise an exception such as `MissingSchema` or `InvalidURL`. Since these all inherit from `RequestException`, you can catch the base exception to handle all such cases.

```python Handling URL Errors icon=logos:python
import requests
from requests.exceptions import RequestException

try:
    response = requests.get('invalid-url-without-schema')
except RequestException as e:
    # This will catch MissingSchema, InvalidURL, and other request-related errors
    print(f'An error occurred with the request: {e}')
```

## Exception Hierarchy

Understanding the hierarchy of exceptions helps in writing precise `try...except` blocks. The following diagram illustrates the inheritance structure of the primary exceptions in Requests.

```d2 Exception Hierarchy Diagram
direction: down

IOError

RequestException: {
  label: "requests.exceptions.RequestException"
}
IOError -> RequestException

ConnectionError -> RequestException
ProxyError -> ConnectionError
SSLError -> ConnectionError

Timeout -> RequestException
ReadTimeout -> Timeout
ConnectTimeout: {
  label: "ConnectTimeout"
}
ConnectTimeout -> ConnectionError
ConnectTimeout -> Timeout

HTTPError -> RequestException
TooManyRedirects -> RequestException
URLRequired -> RequestException

InvalidURL: {
  label: "InvalidURL (ValueError)"
}
InvalidURL -> RequestException
InvalidProxyURL -> InvalidURL

MissingSchema: {
  label: "MissingSchema (ValueError)"
}
MissingSchema -> RequestException

InvalidSchema: {
  label: "InvalidSchema (ValueError)"
}
InvalidSchema -> RequestException

ContentDecodingError -> RequestException
ChunkedEncodingError -> RequestException
StreamConsumedError -> RequestException

InvalidJSONError -> RequestException
JSONDecodeError -> InvalidJSONError

```

### Common Exceptions

Here is a list of the most common exceptions you might encounter and what they mean.

| Exception | Description |
|---|---|
| `RequestException` | The base exception. Any ambiguous exception during a request falls under this. |
| `HTTPError` | Raised by `raise_for_status()` for unsuccessful status codes (4xx or 5xx). |
| `ConnectionError` | A general exception for network problems (e.g., DNS failure, refused connection). |
| `ProxyError` | An error occurred with the configured proxy. |
| `SSLError` | An SSL-related error occurred. |
| `Timeout` | The request timed out. This catches both `ConnectTimeout` and `ReadTimeout`. |
| `ConnectTimeout` | The request timed out while trying to connect to the remote server. |
| `ReadTimeout` | The server did not send any data in the allotted amount of time. |
| `URLRequired` | A valid URL was not provided to make a request. |
| `TooManyRedirects` | The request exceeded the configured number of maximum redirections. |
| `MissingSchema` | The URL was missing a schema (e.g., `http://` or `https://`). |
| `InvalidURL` | The URL provided was somehow invalid. |
| `JSONDecodeError` | Raised when `response.json()` fails to decode the response content. |

For a complete list of all exceptions, see the [Exceptions API Reference](./api-reference-exceptions.md).
