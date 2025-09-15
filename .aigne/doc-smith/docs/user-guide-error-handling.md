# Error Handling

When you're building applications that interact with web services, robust error handling is crucial. Network connections can be unreliable, servers can be down, and responses may not always be what you expect. Requests is designed to handle these situations gracefully by raising exceptions for various error conditions. This guide will walk you through the common exceptions you might encounter and how to handle them effectively.

Nearly all exceptions raised by Requests inherit from the base exception `requests.exceptions.RequestException`. This makes it easy to catch all potential errors from the library with a single `try...except` block if needed.

## HTTP Status Code Errors

One of the most common tasks is to check if a request was successful. A successful response is typically indicated by a status code in the 2xx range. Any status code of 4xx or 5xx signifies a client or server error, respectively.

Instead of checking `response.status_code` manually, you can use the `Response.raise_for_status()` method. This method will raise an `HTTPError` if the request returned an unsuccessful status code.

```python http_error_example.py icon=logos:python
import requests

try:
    response = requests.get('https://httpbin.org/status/404')
    print(f"Request successful with status code: {response.status_code}")

    # This line will raise an HTTPError if the status is 4xx or 5xx
    response.raise_for_status()

except requests.exceptions.HTTPError as http_err:
    print(f'HTTP error occurred: {http_err}')
    # The original response object is attached to the exception
    print(f'Status Code: {http_err.response.status_code}')
    print(f'Reason: {http_err.response.reason}')
except Exception as err:
    print(f'An unexpected error occurred: {err}')
```

Running this code will attempt to fetch a URL that returns a 404 Not Found status, triggering the `HTTPError` exception.

## Connection and Timeout Errors

Network problems can occur at any time. A domain might not exist, a server might be down, or the connection could time out. Requests raises a `ConnectionError` for these types of network issues.

```python connection_error_example.py icon=logos:python
import requests

try:
    response = requests.get('https://this-is-not-a-real-domain.com')
except requests.exceptions.ConnectionError as conn_err:
    print(f'A connection error occurred: {conn_err}')
```

Timeouts are another common network issue. You can configure timeouts using the `timeout` parameter in your request. If the server doesn't respond within the specified time, Requests will raise a `Timeout` exception. The `Timeout` exception is a parent class for both `ConnectTimeout` (for when the initial connection times out) and `ReadTimeout` (for when the server stops sending data mid-response).

```python timeout_example.py icon=logos:python
import requests

try:
    # httpbin.org's /delay endpoint waits for the specified number of seconds
    # We set our timeout to be shorter than the delay.
    response = requests.get('https://httpbin.org/delay/5', timeout=2)
except requests.exceptions.Timeout as timeout_err:
    print(f'The request timed out: {timeout_err}')
```

## Redirection Errors

By default, Requests will automatically follow redirects. However, if a server is misconfigured and creates a redirect loop, your application could get stuck. To prevent this, Requests will raise a `TooManyRedirects` exception after 30 redirects by default.

```python redirect_error_example.py icon=logos:python
import requests

try:
    # This endpoint redirects 10 times.
    # If you set the limit lower, it would raise an error.
    # For this example, we'll assume the default limit was hit.
    response = requests.get('https://httpbin.org/absolute-redirect/35')
except requests.exceptions.TooManyRedirects as redirect_err:
    print(f'Too many redirects: {redirect_err}')
```

## Invalid URL Errors

If you provide a URL that is malformed or missing a required component like the scheme (`http://` or `https://`), Requests will raise an exception, typically `MissingSchema` or `InvalidURL`.

```python url_error_example.py icon=logos:python
import requests

try:
    response = requests.get('httpbin.org/get') # Missing the 'https://'
except requests.exceptions.MissingSchema as schema_err:
    print(f'Invalid URL: {schema_err}')
```

## Exception Hierarchy

Understanding the hierarchy of exceptions can help you write more precise error-handling logic. For example, catching `ConnectionError` will also catch `ProxyError` and `SSLError` because they are subclasses.

Here is a table of the most common exceptions and their relationships:

| Exception                  | Inherits From            | Description                                                        |
| -------------------------- | ------------------------ | ------------------------------------------------------------------ |
| `RequestException`         | `IOError`                | The base exception for any Requests-related issue.                 |
| `HTTPError`                | `RequestException`       | Raised for unsuccessful status codes (4xx or 5xx).                 |
| `ConnectionError`          | `RequestException`       | Wraps general network-level errors like DNS failures.              |
| `ProxyError`               | `ConnectionError`        | Indicates an issue with the configured proxy server.               |
| `SSLError`                 | `ConnectionError`        | An SSL handshake error occurred.                                   |
| `Timeout`                  | `RequestException`       | The request timed out. This is a base class for more specific timeouts. |
| `ConnectTimeout`           | `Timeout`, `ConnectionError` | Timeout occurred while trying to establish a connection.           |
| `ReadTimeout`              | `Timeout`                | The server did not send any data in the allotted amount of time.   |
| `URLRequired`              | `RequestException`       | A valid URL was not provided to make a request.                    |
| `TooManyRedirects`         | `RequestException`       | The request exceeded the configured redirect limit.                |
| `InvalidURL`               | `RequestException`       | The provided URL was malformed.                                    |
| `MissingSchema`            | `InvalidURL`             | The URL was missing a scheme (e.g., `http://`).                    |
| `JSONDecodeError`          | `RequestException`       | Raised when `response.json()` fails to decode the response body.   |

By leveraging this hierarchy, you can decide how granular your error handling needs to be. You can catch the specific `ConnectTimeout` to implement a retry mechanism, while catching the general `RequestException` for logging all other unforeseen issues.

---

With this knowledge, you can build more resilient applications that gracefully handle network failures and unexpected server responses. For more detailed control over network behavior, see the [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md) section.