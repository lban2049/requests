# Error Handling

When you use Requests, network connections can fail, servers can be unreachable, or responses might not be what you expected. Requests anticipates these issues and will raise an exception when they occur. This guide will walk you through the common exceptions and how to handle them gracefully.

All exceptions raised by Requests inherit from the base class `requests.exceptions.RequestException`.

## HTTP Status Code Errors

By default, Requests does not raise an exception for unsuccessful HTTP status codes (like `404 Not Found` or `500 Internal Server Error`). To make Requests raise an exception for these responses, you can use the `raise_for_status()` method on a `Response` object.

If the status code indicates an error (4xx for client errors, 5xx for server errors), `raise_for_status()` will raise an `HTTPError`.

```python Handling HTTP Errors
import requests
from requests.exceptions import HTTPError

url = 'https://httpbin.org/status/404'

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

## Connection Errors

For network-level problems, such as a DNS failure or a refused connection, Requests will raise a `ConnectionError`.

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

url = 'https://this-is-a-nonexistent-domain.com'

try:
    response = requests.get(url)
except ConnectionError as e:
    print(f"Connection error occurred: {e}")
```

## Timeouts

You can configure requests to stop waiting for a response after a specific number of seconds by using the `timeout` parameter. If the server does not respond in time, a `Timeout` exception is raised.

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

url = 'https://httpbin.org/delay/5' # This endpoint waits 5 seconds to respond

try:
    # Set a timeout of 3 seconds
    response = requests.get(url, timeout=3)
except Timeout:
    print('The request timed out')
```

The `Timeout` exception is a base class that catches both `ConnectTimeout` (for timeouts during connection establishment) and `ReadTimeout` (for timeouts while waiting for data from the server). If you need to handle these cases differently, you can catch them specifically.

## Redirection Errors

Requests automatically follows redirects. However, if a request chain exceeds the maximum number of redirects, it will raise a `TooManyRedirects` exception. This helps prevent infinite redirect loops.

```python Handling Too Many Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

# This endpoint redirects 10 times by default
url = 'https://httpbin.org/redirect/10'

try:
    # By default, the redirect limit is 30. Let's imagine a scenario with a lower limit.
    # For this example, we'll just catch the exception if it were to happen.
    response = requests.get(url)
except TooManyRedirects:
    print('The request exceeded the maximum number of redirects.')
```

## Invalid URLs

If you provide a URL that is improperly formatted, Requests will raise an exception. The most common is `MissingSchema`, which occurs if the URL does not include `http://` or `https://`.

```python Handling Invalid URLs icon=logos:python
import requests
from requests.exceptions import MissingSchema

try:
    response = requests.get('httpbin.org/get')
except MissingSchema as e:
    print(f'Invalid URL: {e}')
```

## Content Decoding Errors

When you attempt to parse a response body that is not valid JSON using the `response.json()` method, a `JSONDecodeError` will be raised.

```python Handling JSON Decode Errors icon=logos:python
import requests

url = 'https://httpbin.org/html' # This endpoint returns HTML, not JSON

try:
    response = requests.get(url)
    response.raise_for_status()
    data = response.json()
except requests.exceptions.JSONDecodeError:
    print("Failed to decode JSON from the response.")
except requests.exceptions.HTTPError as err:
    print(f'HTTP error occurred: {err}')
```

## Summary of Common Exceptions

Here is a quick reference table for the most common exceptions you might encounter:

| Exception | Reason for Being Raised |
|---|---|
| `RequestException` | The base exception that all other exceptions inherit from. |
| `HTTPError` | An HTTP error occurred (4xx or 5xx status code). Raised by `response.raise_for_status()`. |
| `ConnectionError` | A network problem occurred (e.g., DNS failure, connection refused). |
| `ProxyError` | A problem with the proxy server occurred. |
| `SSLError` | An SSL handshake error occurred. |
| `Timeout` | The request timed out. This includes both `ConnectTimeout` and `ReadTimeout`. |
| `TooManyRedirects` | The request exceeded the configured number of maximum redirections. |
| `MissingSchema` | The URL was missing the scheme (e.g., `http://` or `https://`). |
| `InvalidURL` | The URL was malformed. |
| `JSONDecodeError` | Failed to decode the response content as JSON using `response.json()`. |

By anticipating these potential errors and using `try...except` blocks, you can build resilient applications that handle network issues and unexpected server responses gracefully.

Now that you know how to handle errors, you are ready to explore more complex scenarios. See our [Advanced Usage](./advanced-usage.md) guide to learn about session objects, SSL verification, and more.