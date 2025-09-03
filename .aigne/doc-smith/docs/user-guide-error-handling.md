# Error Handling

When working with external services over a network, you must anticipate that things can and will go wrong. Requests uses exceptions to signal these issues. This guide covers the common exceptions raised by the library and how to handle them gracefully in your applications.

All exceptions specific to the Requests library inherit from `requests.exceptions.RequestException`, making it a convenient base class to catch for any error originating from the library.

### Exception Hierarchy

The following diagram illustrates the inheritance hierarchy for some of the most common exceptions in Requests. Understanding this structure can help you catch exceptions at the right level of granularity.

```d2
direction: down

"IOError": {
  shape: class
}

"requests.exceptions.RequestException": {
  shape: class
  style {
    stroke: "#4285F4"
    stroke-width: 2
  }
}

"IOError" -> "requests.exceptions.RequestException"

sub_exceptions: {
  grid-columns: 3
  "HTTPError": { shape: class }
  "ConnectionError": { shape: class }
  "Timeout": { shape: class }
  "TooManyRedirects": { shape: class }
  "URLRequired": { shape: class }
  "InvalidJSONError": { shape: class }
}

"requests.exceptions.RequestException" -> sub_exceptions

connection_sub: {
  "ProxyError": { shape: class }
  "SSLError": { shape: class }
}
"ConnectionError" -> connection_sub

timeout_sub: {
  "ConnectTimeout": { shape: class }
  "ReadTimeout": { shape: class }
}
"Timeout" -> timeout_sub
"ConnectionError" -> timeout_sub.ConnectTimeout
```


### Handling Bad Status Codes with `HTTPError`

When a server returns an unsuccessful HTTP status code (in the 4xx or 5xx range), Requests will not automatically raise an exception. However, you can use the `response.raise_for_status()` method to do so. If the response has a bad status code, it will raise an `HTTPError`.

```python
import requests

for url in ['https://httpbin.org/get', 'https://httpbin.org/status/500']:
    try:
        response = requests.get(url)
        # If the response was successful, no exception will be raised
        response.raise_for_status()
    except requests.exceptions.HTTPError as errh:
        print(f"Http Error: {errh}")
        # You can inspect the response on the exception object
        if errh.response is not None:
            print(f"Status Code: {errh.response.status_code}")
    except requests.exceptions.RequestException as err:
        print(f"Other Error: {err}")
    else:
        print(f"Successfully fetched {url}")

```
This approach is useful for quickly asserting that a request was successful while centralizing the handling of client and server errors.

### Network Problems with `ConnectionError`

If you encounter network-level issues, such as a DNS failure, a refused connection, or a proxy error, Requests will raise a `ConnectionError`.

```python
import requests

try:
    response = requests.get('https://not-a-real-domain.xyz')
except requests.exceptions.ConnectionError as errc:
    print(f"Error Connecting: {errc}")
```

More specific exceptions like `ProxyError` and `SSLError` inherit from `ConnectionError`, so you can catch them individually if you need to handle these scenarios differently.

### Handling `Timeout` Exceptions

A request can time out at two distinct phases: connecting to the server and reading data from the server. Requests provides a base `Timeout` exception that catches both.

- `ConnectTimeout`: The request timed out while trying to connect to the remote server.
- `ReadTimeout`: The server did not send any data in the allotted amount of time.

```python
import requests

try:
    # httpbin.org/delay/3 will take 3 seconds to respond
    response = requests.get('https://httpbin.org/delay/3', timeout=1)
except requests.exceptions.Timeout as errt:
    print(f"Timeout Error: {errt}")
```
In this example, the request will time out because the `timeout` parameter (1 second) is less than the server's response time (3 seconds). Catching `requests.exceptions.Timeout` is a reliable way to handle both connection and read timeouts.

### Excessive Redirection with `TooManyRedirects`

By default, Requests will follow up to 30 redirects. If this limit is exceeded, it will raise a `TooManyRedirects` exception to prevent an infinite loop.

```python
import requests

# This URL will redirect more than the default limit of 30.
try:
    response = requests.get('https://httpbin.org/redirect/31')
except requests.exceptions.TooManyRedirects as errr:
    print(f"Too Many Redirects: {errr}")
```

### Catching the Base `RequestException`

If you want to create a generic handler for any exception that the Requests library might throw, you can catch the base `requests.exceptions.RequestException`.

```python
import requests

try:
    response = requests.get('https://invalid-schema://example.com')
    response.raise_for_status()
except requests.exceptions.RequestException as err:
    print(f"Something went wrong with the request: {err}")
```

### Common Exceptions Quick Reference

Here is a summary table of the most common exceptions you might encounter.

| Exception | Description |
|---|---|
| `requests.exceptions.RequestException` | The base exception class. All other exceptions raised by Requests inherit from it. |
| `requests.exceptions.HTTPError` | Raised for unsuccessful status codes (4xx or 5xx) when `response.raise_for_status()` is called. |
| `requests.exceptions.ConnectionError` | Raised for network-related problems like DNS failures or refused connections. |
| `requests.exceptions.ProxyError` | A subclass of `ConnectionError` raised for proxy-specific issues. |
| `requests.exceptions.SSLError` | A subclass of `ConnectionError` raised for SSL handshake failures. |
| `requests.exceptions.Timeout` | The base exception for when a request times out. |
| `requests.exceptions.ConnectTimeout` | Raised when establishing a connection times out. These requests are safe to retry. |
| `requests.exceptions.ReadTimeout` | Raised when the server does not send data within the specified timeout period. |
| `requests.exceptions.TooManyRedirects` | Raised when a request exceeds the maximum number of allowed redirects. |
| `requests.exceptions.MissingSchema` | Raised when a URL is provided without a scheme (e.g., `http://` or `https://`). |
| `requests.exceptions.InvalidURL` | Raised if the provided URL is malformed in some other way. |
| `requests.exceptions.JSONDecodeError` | Raised when `response.json()` fails to parse the response content as valid JSON. |

By anticipating these common errors, you can build more resilient and reliable applications. To learn how to configure network behavior like timeouts and retries, see the [Advanced Usage](./advanced-usage.md) section.