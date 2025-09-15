# User Guide

Welcome to the Requests User Guide! This guide is designed to help you explore the core functionalities of the library through practical, code-first examples. We'll cover everything from making your first request to handling complex scenarios like authentication and session management.

If you haven't installed the library yet, please start with our [Getting Started](./getting-started.md) guide. For an exhaustive list of all classes and methods, refer to the [API Reference](./api-reference.md).

This guide is broken down into the following sections:

<x-cards>
  <x-card data-title="Making a Request" data-href="/user-guide/making-a-request" data-icon="lucide:send">
    Learn how to use various HTTP methods like GET, POST, PUT, and how to pass URL parameters, headers, and request bodies.
  </x-card>
  <x-card data-title="Handling Responses" data-href="/user-guide/handling-responses" data-icon="lucide:arrow-down-left">
    Understand how to access response content (text, JSON, binary), check status codes, and read response headers.
  </x-card>
  <x-card data-title="Session Objects" data-href="/user-guide/session-objects" data-icon="lucide:database">
    Utilize Session objects to persist parameters, cookies, and headers across multiple requests for improved performance.
  </x-card>
  <x-card data-title="Authentication" data-href="/user-guide/authentication" data-icon="lucide:key-round">
    Implement various authentication schemes, including Basic and Digest Auth, to secure your requests.
  </x-card>
  <x-card data-title="Error Handling" data-href="/user-guide/error-handling" data-icon="lucide:shield-alert">
    Learn to anticipate and handle potential request errors, such as connection issues, timeouts, and bad HTTP statuses.
  </x-card>
</x-cards>

## A Simple Example

Here is a quick example that demonstrates the basic workflow of making a request and processing the response, touching on concepts from the first few chapters of this guide.

```python Basic Workflow Example icon=logos:python
import requests

# Make a GET request to a public API
response = requests.get('https://httpbin.org/get')

# You can check the status code to see if the request was successful
if response.status_code == 200:
    print('Success!')
    # The response content can be decoded as JSON
    data = response.json()
    print('Response JSON:')
    print(data)
else:
    print(f'Request failed with status code: {response.status_code}')

# The Response object has many useful attributes
print(f"\nResponse Headers: {response.headers['Content-Type']}")
```

## Next Steps

This guide provides a structured path to mastering Requests. Each section builds upon the last, giving you a comprehensive understanding of the library's capabilities.

When you're ready, dive into the first chapter, [Making a Request](./user-guide-making-a-request.md). If you're looking for solutions to more specific problems, you may want to explore our [Advanced Usage](./advanced-usage.md) guide.