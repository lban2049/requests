# Error Handling

When you build applications that rely on network requests, you must anticipate that things can and will go wrong. Networks can be unreliable, servers can go down, and URLs can be malformed. The Requests library provides a comprehensive set of exceptions to help you gracefully handle these situations.

All exceptions raised by Requests inherit from the base class `requests.exceptions.RequestException`. This allows you to create a single `try...except` block to catch any error originating from the library.

```python Handling Requests Exceptions icon=logos:python
import requests

try:
    # An action that could fail
    response = requests.get('https://a-very-unreliable-server.com', timeout=1)
    response.raise_for_status()
except requests.exceptions.RequestException as e:  
    # This will catch any exception raised by the Requests library.
    print(f"An error occurred: {e}")
```

Let's explore the most common types of errors and how to handle them specifically.

## Handling HTTP Status Code Errors

By default, Requests does not consider unsuccessful HTTP status codes (like `404 Not Found` or `500 Internal Server Error`) as errors that should crash your program. However, you often want to treat these responses as failures. The easiest way to do this is with the `Response.raise_for_status()` method.

If the response status code is between 400 and 599, `raise_for_status()` will raise an `HTTPError`.

```python Checking for HTTP Errors icon=logos:python
import requests
from requests.exceptions import HTTPError

urls = ['https://httpbin.org/get', 'https://httpbin.org/status/404', 'https://httpbin.org/status/500']

for url in urls:
    try:
        response = requests.get(url)
        # If the response was successful, no exception will be raised
        response.raise_for_status()
    except HTTPError as http_err:
        print(f'HTTP error occurred: {http_err}')
    except Exception as err:
        print(f'Other error occurred: {err}')
    else:
        print('Success!')
```

This code will successfully process the first URL, but it will catch `HTTPError` exceptions for the 404 and 500 status codes, preventing your application from proceeding with a bad response.

## Connection Errors

A `ConnectionError` occurs when a request fails due to network-level issues, such as a DNS resolution failure, a refused connection, or other problems that prevent your client from reaching the server.

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://this-domain-does-not-exist.org')
except ConnectionError as e:
    print(f"A connection error occurred: {e}")
```

This is a broad exception that covers many network problems. More specific exceptions like `ProxyError` and `SSLError` also inherit from `ConnectionError`.

## Timeouts

You can configure requests to stop waiting for a response after a given number of seconds. If the server does not respond in time, a `Timeout` exception is raised.

The `Timeout` exception is a general catch-all for two more specific timeout events:
*   `ConnectTimeout`: Occurs if the timeout is hit while trying to establish a connection to the remote server.
*   `ReadTimeout`: Occurs if the server has accepted the connection but fails to send any data back within the specified time.

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # httpbin.org/delay/5 takes 5 seconds to respond.
    response = requests.get('https://httpbin.org/delay/5', timeout=2)
except Timeout:
    print('The request timed out.')
```

## Too Many Redirects

When a server responds with a redirect status (3xx), Requests will automatically follow it. To prevent infinite redirect loops, Requests will stop after 30 redirects by default and raise a `TooManyRedirects` exception.

```python Handling Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

try:
    # This URL will redirect 11 times.
    response = requests.get('https://httpbin.org/redirect/11', allow_redirects=True)
    print(f"Final URL: {response.url}")

    # This will fail because the default limit is 10 for this endpoint.
    response_fail = requests.get('https://httpbin.org/absolute-redirect/11')
except TooManyRedirects:
    print('The request exceeded the redirect limit.')
```

## Exception Hierarchy

Understanding the hierarchy of exceptions can help you write more precise error-handling logic. For example, catching `ConnectionError` will also catch `ProxyError` and `SSLError`.

Here is a table of the most common exceptions and their relationships:

| Exception | Description | Parent Class(es) |
| :--- | :--- | :--- |
| `RequestException` | The base exception from which all others inherit. | `IOError` |
| `HTTPError` | Raised for unsuccessful status codes (4xx or 5xx) via `raise_for_status()`. | `RequestException` |
| `ConnectionError` | A base class for errors related to network connections. | `RequestException` |
| `ProxyError` | An error occurred with the configured proxy. | `ConnectionError` |
| `SSLError` | An SSL-related error occurred. | `ConnectionError` |
| `Timeout` | The request timed out. Catches both `ConnectTimeout` and `ReadTimeout`. | `RequestException` |
| `ConnectTimeout` | The request timed out while trying to connect to the server. | `ConnectionError`, `Timeout` |
| `ReadTimeout` | The server did not send any data in the allotted time. | `Timeout` |
| `URLRequired` | A valid URL was not provided to make a request. | `RequestException` |
| `TooManyRedirects` | The request exceeded the configured number of maximum redirections. | `RequestException` |
| `MissingSchema` | The URL was missing a schema (e.g., `http://` or `https://`). | `RequestException`, `ValueError` |
| `InvalidURL` | The provided URL was malformed. | `RequestException`, `ValueError` |
| `JSONDecodeError` | Raised when `response.json()` fails to parse the response content. | `RequestException` |

By leveraging these exceptions, you can build robust and resilient applications that can handle network failures and unexpected server responses effectively.

For more complex scenarios, you might want to explore advanced features. Continue to the [Advanced Usage](./advanced-usage.md) section to learn about custom timeouts, proxies, and SSL verification.