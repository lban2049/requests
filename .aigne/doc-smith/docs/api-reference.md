# API Reference

Welcome to the official API reference for the Requests library. This section provides detailed documentation on all public classes, methods, functions, and exceptions. It is intended for developers who need a deep understanding of specific components to build robust HTTP applications.

For practical examples and common use cases, please see the [Core Usage](./core-usage.md) and [Advanced Usage](./advanced-usage.md) guides.

<x-cards data-columns="3">
  <x-card data-title="Request and Response Objects" data-icon="lucide:arrow-right-left" data-href="/api-reference/request-response-objects">
    Explore the core objects that power every interaction: `Request`, `PreparedRequest`, and `Response`. Understand their attributes and methods to finely control outgoing requests and process incoming data.
  </x-card>
  <x-card data-title="Session Object" data-icon="lucide:book-copy" data-href="/api-reference/session-object">
    Learn how to use the `Session` object to persist cookies, leverage connection pooling, and apply default settings across multiple requests for improved performance and cleaner code.
  </x-card>
  <x-card data-title="Exceptions" data-icon="lucide:shield-alert" data-href="/api-reference/exceptions">
    A complete guide to the exception hierarchy in Requests. Learn to anticipate and handle network problems, HTTP errors, and other potential issues gracefully.
  </x-card>
</x-cards>

## Top-Level API

The simplest way to use Requests is through the top-level functional API, which provides convenient wrappers for common HTTP methods. These functions are the primary entry point for most users.

| Function | Description |
|---|---|
| `requests.request(method, url, **kwargs)` | The main function that all other method functions call. Constructs and sends a `Request`. |
| `requests.get(url, params=None, **kwargs)` | Sends a GET request. |
| `requests.post(url, data=None, json=None, **kwargs)` | Sends a POST request. |
| `requests.put(url, data=None, **kwargs)` | Sends a PUT request. |
| `requests.patch(url, data=None, **kwargs)` | Sends a PATCH request. |
| `requests.delete(url, **kwargs)` | Sends a DELETE request. |
| `requests.head(url, **kwargs)` | Sends a HEAD request. |
| `requests.options(url, **kwargs)` | Sends an OPTIONS request. |

While these functions are powerful, for more advanced scenarios like maintaining cookies across requests, you should use a [Session object](./api-reference-session-object.md).