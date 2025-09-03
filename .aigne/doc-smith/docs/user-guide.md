# User Guide

Welcome to the Requests User Guide. This guide will walk you through the library's core features with practical, code-first examples. We'll cover everything from making your first request to handling responses, managing sessions, implementing authentication, and handling errors.

This guide is designed for developers who have already installed the library and want to learn its core functionalities. If you haven't installed Requests yet, please see the [Getting Started](./getting-started.md) guide first.

<x-cards data-columns="2">
  <x-card data-title="Making a Request" data-href="/user-guide/making-a-request" data-icon="lucide:send">
    Learn to send various HTTP requests like GET, POST, and PUT. We'll cover passing URL parameters, custom headers, and different types of request bodies.
  </x-card>
  <x-card data-title="Handling Responses" data-href="/user-guide/handling-responses" data-icon="lucide:arrow-down-circle">
    Explore how to process the server's response. Access response content in different formats (text, JSON, binary), check status codes, and read headers.
  </x-card>
  <x-card data-title="Session Objects" data-href="/user-guide/session-objects" data-icon="lucide:database">
    Use Session objects to persist parameters, cookies, and headers across multiple requests, and benefit from connection pooling for better performance.
  </x-card>
  <x-card data-title="Authentication" data-href="/user-guide/authentication" data-icon="lucide:key-round">
    Secure your requests by implementing common authentication schemes, including Basic and Digest Authentication.
  </x-card>
</x-cards>

<x-cards data-columns="1">
 <x-card data-title="Error Handling" data-href="/user-guide/error-handling" data-icon="lucide:shield-alert">
    Write robust code by learning to handle potential request errors, such as connection issues, timeouts, and non-successful HTTP statuses.
  </x-card>
</x-cards>

## A Complete Example

Here is a simple example that demonstrates a common workflow: making a GET request, checking for success, and parsing the JSON response. This pattern covers many everyday use cases.

```python
import requests

try:
    # Make a GET request to the httpbin.org API with URL parameters
    response = requests.get('https://httpbin.org/get', params={'key': 'value'})

    # Check if the request was successful (status code 200-399)
    # If not, this will raise an HTTPError
    response.raise_for_status()

    # Print the response content as JSON
    json_response = response.json()
    print("Request successful!")
    print("Request URL:", json_response['url'])
    print("Origin IP:", json_response['origin'])
    print("User-Agent Header:", json_response['headers']['User-Agent'])

except requests.exceptions.HTTPError as http_err:
    print(f"HTTP error occurred: {http_err}")  # e.g., 404 Not Found
except requests.exceptions.ConnectionError as conn_err:
    print(f"Connection error occurred: {conn_err}") # e.g., DNS failure, refused connection
except requests.exceptions.Timeout as timeout_err:
    print(f"Timeout error occurred: {timeout_err}")
except requests.exceptions.RequestException as err:
    print(f"An unexpected error occurred: {err}")

```
This example uses `requests.get` to send a request, `response.raise_for_status()` to check for HTTP errors, and `response.json()` to decode the JSON body. It also includes basic error handling for common network issues.

## Next Steps

This guide provides the foundation for using Requests effectively. To begin, dive into [Making a Request](./user-guide-making-a-request.md) to learn the fundamentals of sending data over the web. For more complex scenarios, refer to the [Advanced Usage](./advanced-usage.md) guide.