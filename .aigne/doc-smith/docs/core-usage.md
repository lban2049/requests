# Core Usage

**Requests** is a simple, yet elegant, HTTP library designed to make HTTP requests extremely easy. This section covers the fundamental patterns for making requests and handling responses using the simple, functional API. It's the perfect starting point for learning how to interact with web services in Python.

For a high-level introduction, you might want to start with the [Overview](./overview.md).

### The Basic Request-Response Pattern

The core of the Requests library revolves around making a request to a URL and receiving a `Response` object. This object contains all the information sent back by the server.

A simple `GET` request, which is used to retrieve data from a URL, looks like this:

```python Making a simple GET request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')

# Check the status code to see if the request was successful
if r.status_code == 200:
    print('Success!')
else:
    print('An error occurred.')

# You can access response headers
print(r.headers['content-type'])

# And the response body as text
# print(r.text)

# Or, if the response is JSON, you can decode it automatically
print(r.json()[0]) # Print the first event from the GitHub API
```

This simple pattern—calling a function for an HTTP method and getting a response object back—is the foundation for everything you'll do with Requests.

### Exploring Core Functionality

To help you master the essentials, we've broken down the core usage into several detailed guides. Each guide focuses on a specific aspect of making requests and handling responses.

<x-cards data-columns="2">
  <x-card data-title="Making a Request" data-icon="lucide:send" data-href="/core-usage/making-a-request">
    Learn how to perform various HTTP methods like GET, POST, PUT, DELETE, and more. This guide covers the basic request-response cycle.
  </x-card>
  <x-card data-title="Passing URL Parameters" data-icon="lucide:link-2" data-href="/core-usage/passing-url-parameters">
    Discover how to send data in the URL's query string, a common requirement for APIs that use GET requests to filter or specify data.
  </x-card>
  <x-card data-title="Handling Response Content" data-icon="lucide:file-text" data-href="/core-usage/handling-response-content">
    Dive into the different ways to access the response body, whether it's raw bytes, decoded text, or structured JSON data.
  </x-card>
  <x-card data-title="POSTing Data" data-icon="lucide:upload-cloud" data-href="/core-usage/posting-data">
    Explore various methods for sending data in the body of a request, including form-encoded data, multipart file uploads, and JSON payloads.
  </x-card>
</x-cards>

### The Response Object

As you've seen, every request call returns a `Response` object. This object is packed with useful attributes and methods that allow you to inspect the server's reply. Key attributes include:

*   `status_code`: The HTTP status code (e.g., `200` for success, `404` for not found).
*   `headers`: A dictionary-like object of the response headers.
*   `content`: The response body in bytes.
*   `text`: The response body decoded as a string.
*   `json()`: A method that deserializes a JSON response body into a Python object.

Understanding how to work with this object is crucial for building effective applications, and the guides above will walk you through it in detail.

### Next Steps

After you've familiarized yourself with these core concepts, you'll be ready to tackle more complex interactions. The [Advanced Usage](./advanced-usage.md) section covers topics like session management, authentication, and error handling to help you build robust and reliable HTTP applications.