# Error Handling

When building applications that rely on external services, it's crucial to anticipate and handle potential issues. Network problems can occur, servers can fail, and responses may not be what you expect. Requests provides a set of exceptions to help you gracefully manage these situations.

All exceptions raised by Requests inherit from the base class `requests.exceptions.RequestException`.

## HTTP Status Code Errors

For unsuccessful HTTP responses (i.e., status codes in the 4xx or 5xx range), you can use the `Response.raise_for_status()` method. This is a convenient way to check if a request was successful and raise an `HTTPError` if it was not.

```python Handling HTTP Errors icon=logos:python
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

When this code is run, the `raise_for_status()` call will raise an `HTTPError` with a message indicating the client error:

```text Expected Output
HTTP error occurred: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

## Connection Errors

If a network problem occurs (e.g., DNS failure, refused connection), Requests will raise a `ConnectionError`.

For instance, attempting to connect to an invalid or unreachable domain will trigger this exception.

```python Handling Connection Errors icon=logos:python
import requests
from requests.exceptions import ConnectionError

try:
    response = requests.get('https://example.invalid-domain')
except ConnectionError as e:
    print(f"Connection error occurred: {e}")
```

## Timeouts

You can configure requests to stop waiting for a response after a given number of seconds. If the server does not respond in time, a `Timeout` exception is raised.

The `requests.exceptions.Timeout` exception is a parent class for two more specific exceptions: `ConnectTimeout` and `ReadTimeout`. This allows you to catch both types of timeouts with a single `except` block.

```python Handling Timeouts icon=logos:python
import requests
from requests.exceptions import Timeout

try:
    # Attempt to connect to a slow endpoint with a very short timeout
    response = requests.get('https://httpbin.org/delay/5', timeout=1)
except Timeout:
    print('The request timed out')
```

For more detailed configuration of timeouts, see the [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md) section.

## Invalid URLs

If you provide a URL that is improperly formatted, Requests will raise an exception indicating the issue. The most common is `MissingSchema`, which occurs if you forget to include `http://` or `https://`.

```python Handling Invalid URLs icon=logos:python
import requests
from requests.exceptions import MissingSchema

try:
    response = requests.get('google.com')
except MissingSchema as e:
    print(f"Invalid URL: {e}")
```

This will output a helpful message suggesting the correct format:
`Invalid URL: Invalid URL 'google.com': No scheme supplied. Perhaps you meant https://google.com?`

## Redirection Errors

By default, Requests handles redirects. However, if a request exceeds the default limit of 30 redirects, it will raise a `TooManyRedirects` exception to prevent it from getting stuck in a redirect loop.

```python Handling Too Many Redirects icon=logos:python
import requests
from requests.exceptions import TooManyRedirects

try:
    # httpbin.org/redirect/N redirects N times
    response = requests.get('https://httpbin.org/redirect/35')
except TooManyRedirects:
    print('Too many redirects')
```

## JSON Decoding Errors

When you use `response.json()` to parse a response, you might encounter a `JSONDecodeError` if the response body does not contain valid JSON. This can happen if the server returns an error page (like HTML) instead of the expected JSON.

```python Handling JSON Errors icon=logos:python
import requests
from requests.exceptions import JSONDecodeError

url = 'https://httpbin.org/html' # This endpoint returns HTML, not JSON

try:
    response = requests.get(url)
    response.raise_for_status()
    data = response.json()
except JSONDecodeError:
    print("Failed to decode JSON from response.")
except HTTPError as http_err:
    print(f'HTTP error occurred: {http_err}')
```

## Exception Hierarchy

Understanding the exception hierarchy can help you write more effective error-handling logic. For example, since `SSLError` inherits from `ConnectionError`, an `except ConnectionError:` block will also catch SSL errors.

Here is a simplified diagram of the most common exceptions:

```d2
direction: down

RequestException: {
  label: "RequestException"
  shape: rectangle
}

HTTPError: { 
  label: "HTTPError"
  shape: rectangle 
}
ConnectionError: { 
  label: "ConnectionError"
  shape: rectangle 
}
Timeout: { 
  label: "Timeout"
  shape: rectangle 
}
TooManyRedirects: { 
  label: "TooManyRedirects"
  shape: rectangle 
}
MissingSchema: { 
  label: "MissingSchema"
  shape: rectangle 
}

RequestException -> HTTPError
RequestException -> ConnectionError
RequestException -> Timeout
RequestException -> TooManyRedirects
RequestException -> MissingSchema

ConnectTimeout: { 
  label: "ConnectTimeout"
  shape: rectangle 
}
ReadTimeout: { 
  label: "ReadTimeout"
  shape: rectangle 
}
ProxyError: { 
  label: "ProxyError"
  shape: rectangle 
}
SSLError: { 
  label: "SSLError"
  shape: rectangle 
}

ConnectionError -> ConnectTimeout
ConnectionError -> ProxyError
ConnectionError -> SSLError
Timeout -> ConnectTimeout
Timeout -> ReadTimeout
```

### Common Exceptions Summary

Here is a quick reference for the most common exceptions you'll encounter:

| Exception | Description |
|---|---|
| `RequestException` | The base exception class. All other exceptions raised by Requests inherit from it. |
| `HTTPError` | Raised for unsuccessful responses (4xx or 5xx status codes) via `response.raise_for_status()`. |
| `ConnectionError` | Raised for network-related problems like DNS failures or refused connections. |
| `Timeout` | Raised when a request times out. Catches both `ConnectTimeout` and `ReadTimeout`. |
| `TooManyRedirects` | Raised when a request exceeds the configured number of maximum redirections. |
| `MissingSchema` | Raised when a URL is provided without a scheme (e.g., `http://` or `https://`). |
| `JSONDecodeError` | Raised when `response.json()` fails to decode the response content. |

By handling these exceptions, you can make your application more resilient to network failures and unexpected server behavior. For more complex scenarios, you may want to explore [Advanced Usage](./advanced-usage.md).
