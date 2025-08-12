# Error Handling

When making HTTP requests, various issues can arise, from network disconnections to server-side problems or invalid responses. The Requests library provides a comprehensive set of custom exceptions to help you anticipate and handle these issues gracefully, making your applications more resilient. For a complete list and detailed descriptions of all exception types, refer to the [Exceptions](./api-reference-exceptions.md) section.

## Catching All Requests Exceptions

All exceptions raised by Requests inherit from `requests.exceptions.RequestException`. This base class allows you to catch any error specific to the Requests library with a single `try-except` block.

```python
import requests

try:
    response = requests.get('https://httpbin.org/get')
    response.raise_for_status() # Raise an exception for bad status codes
    print("Request successful!")
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")
```

This example demonstrates how to catch any `RequestException` that might occur during the `GET` request or when `raise_for_status()` is called.

## Handling Specific Error Types

While catching `RequestException` is useful for general error handling, you often need more granular control to respond differently to various types of problems. Requests offers specific exception classes for different error scenarios.

### HTTP Status Code Errors

Requests can raise an `requests.exceptions.HTTPError` if an HTTP request returns an unsuccessful status code (4xx or 5xx). You can easily check for and handle these errors using the `Response.raise_for_status()` method.

`Response.raise_for_status()` raises an `HTTPError` if the response's status code is between 400 and 600 (client error or server error). If the status code is less than 400, no error is raised.

```python
import requests

try:
    # This URL will return a 404 Not Found error
    response = requests.get('https://httpbin.org/status/404')
    response.raise_for_status() # This will raise an HTTPError
    print("Request successful!")
except requests.exceptions.HTTPError as e:
    print(f"HTTP Error: {e}")
    print(f"Status Code: {e.response.status_code}")
    print(f"Reason: {e.response.reason}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

This example specifically catches `HTTPError` to extract the status code and reason from the response, allowing you to react appropriately to client or server errors.

### Connection and Timeout Errors

These errors occur when there are issues connecting to the server or when the server takes too long to respond. Requests provides several exceptions for these scenarios:

*   `requests.exceptions.ConnectionError`: Raised for network-related errors (e.g., DNS failure, refused connection, etc.). This is a base class for `ProxyError` and `SSLError`.
*   `requests.exceptions.Timeout`: The base class for timeout exceptions, useful if you want to catch both connection and read timeouts.
*   `requests.exceptions.ConnectTimeout`: The request timed out while trying to connect to the remote server.
*   `requests.exceptions.ReadTimeout`: The server did not send any data in the allotted amount of time after the connection was established.
*   `requests.exceptions.SSLError`: An SSL error occurred during the request.
*   `requests.exceptions.ProxyError`: A proxy connection error occurred.

```python
import requests

try:
    # Simulate a connection error by trying to connect to a non-existent host
    # or a service that refuses connections.
    # For a timeout, set a very short timeout value.
    response = requests.get('http://nonexistent-domain-12345.com', timeout=0.001)
    print("Request successful!")
except requests.exceptions.ConnectTimeout:
    print("Connection timed out!")
except requests.exceptions.ReadTimeout:
    print("Server did not send data in time!")
except requests.exceptions.ConnectionError as e:
    print(f"Network/Connection Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

This code differentiates between various network-related issues, allowing you to implement specific retry logic or fallback mechanisms.

### URL and Request Configuration Errors

These errors typically arise from invalid URLs or incorrect request parameters before the request is even sent over the network:

*   `requests.exceptions.MissingSchema`: The URL scheme (e.g., `http` or `https`) is missing.
*   `requests.exceptions.InvalidURL`: The URL provided was somehow invalid (e.g., malformed host).
*   `requests.exceptions.InvalidSchema`: The URL scheme provided is either invalid or unsupported.
*   `requests.exceptions.URLRequired`: A valid URL is required to make a request.
*   `requests.exceptions.TooManyRedirects`: Exceeded the maximum number of redirects.
*   `requests.exceptions.InvalidHeader`: A header value provided was somehow invalid.

```python
import requests

try:
    # This URL is missing a scheme
    requests.get('www.example.com/path')
except requests.exceptions.MissingSchema as e:
    print(f"URL Scheme Error: {e}")

try:
    # This URL is malformed
    requests.get('http://[::1]:invalid-port/')
except requests.exceptions.InvalidURL as e:
    print(f"Invalid URL Error: {e}")

try:
    # Too many redirects, default limit is 30
    requests.get('https://httpbin.org/redirect/31')
except requests.exceptions.TooManyRedirects as e:
    print(f"Too many redirects: {e}")
```

These examples show how to catch errors related to the request's setup, allowing you to validate inputs or adjust request parameters.

### Content Decoding Errors

These exceptions occur when Requests encounters issues while decoding the response body, such as problems with JSON parsing or general content decoding.

*   `requests.exceptions.JSONDecodeError`: Couldn't decode the text into JSON.
*   `requests.exceptions.ContentDecodingError`: Failed to decode response content.
*   `requests.exceptions.ChunkedEncodingError`: The server declared chunked encoding but sent an invalid chunk.
*   `requests.exceptions.StreamConsumedError`: The content for this response was already consumed (e.g., trying to iterate over a response twice when not streaming).

```python
import requests

try:
    # This endpoint returns plain text, not JSON
    response = requests.get('https://httpbin.org/plain')
    data = response.json() # This will raise JSONDecodeError
except requests.exceptions.JSONDecodeError as e:
    print(f"JSON Decoding Error: {e}")
    print(f"Response text: {response.text[:50]}...")

try:
    # Simulating a scenario where content decoding fails
    # (This is harder to reproduce directly with httpbin)
    # A valid use case is if the server sends malformed compressed data.
    response = requests.get('https://httpbin.org/image/png', stream=True)
    # Manually trigger a content decoding issue if possible, or illustrate the concept
    # If the PNG data was corrupted, this could raise ContentDecodingError
    _ = response.content # Accessing content may trigger decoding
except requests.exceptions.ContentDecodingError as e:
    print(f"Content Decoding Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

This section highlights how to handle issues when interpreting the response body, which is crucial for applications relying on structured data formats.

### Other Specific Errors

Requests also defines other specific exceptions for more niche scenarios:

*   `requests.exceptions.RetryError`: Raised if custom retry logic fails.
*   `requests.exceptions.UnrewindableBodyError`: Requests encountered an error when trying to rewind a body, typically a file-like object, which happens during redirects or retries.

```python
import requests
from requests.exceptions import RetryError

# Example for RetryError (requires custom retry logic, which is outside basic Requests usage)
# This is a placeholder to show how to catch it if your retry mechanism raises it.

try:
    # Assume some custom logic involving retries here
    # If that logic fails and raises RetryError:
    raise RetryError("Custom retry attempt failed after multiple tries")
except RetryError as e:
    print(f"Custom Retry Logic Failed: {e}")

# Example for UnrewindableBodyError (less common in simple GET/POST, more with large file uploads)
# If you provide a non-seekable file-like object as body and a redirect/retry occurs
class NonSeekableFile:
    def read(self, n):
        return b'data' * n
    # no .tell() or .seek() method

try:
    # This might raise UnrewindableBodyError if requests attempts to rewind it
    # during a redirect (e.g., POST with a non-seekable body to a redirect)
    requests.post('https://httpbin.org/redirect-to?url=https://httpbin.org/post', data=NonSeekableFile())
except requests.exceptions.UnrewindableBodyError as e:
    print(f"Unrewindable Body Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

These exceptions are useful for debugging and handling advanced scenarios related to request retries and streamed bodies.

---

Effectively handling exceptions is a cornerstone of building robust applications with Requests. By understanding and catching specific error types, you can implement precise recovery strategies, provide meaningful user feedback, or log issues for debugging. For a deeper dive into each exception type and its inheritance hierarchy, refer to the [Exceptions](./api-reference-exceptions.md) section.