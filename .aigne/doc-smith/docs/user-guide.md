# User Guide

This guide provides a practical exploration of the core functionalities of the Requests library. It moves beyond the basics to cover the most common use cases you'll encounter, from making various types of requests to handling responses and managing sessions. The examples are designed to be practical and ready to use in your projects.

If you haven't installed Requests yet or made your first request, please start with the [Getting Started](./getting-started.md) guide.

### The Standard Workflow

A typical interaction with the Requests library follows a simple pattern: you craft a request, send it, and then process the response. This workflow allows you to interact with web services and APIs efficiently.

```d2
direction: down

"Start" -> "Craft a Request\n(e.g., requests.get)"
"Craft a Request\n(e.g., requests.get)" -> "Send the Request to a URL"
"Send the Request to a URL" -> "Receive a Response Object"
"Receive a Response Object" -> inspect_response: "Inspect the Response"

inspect_response -> "Check status code\n(r.raise_for_status())" -> "Handle Potential HTTPError" -> "End"
inspect_response -> "Access content\n(r.text, r.json(), r.content)" -> "Use the data from the response" -> "End"
```

This guide is broken down into the following sections, each focusing on a key aspect of the library.

<x-cards data-columns="2">
  <x-card data-title="Making a Request" data-icon="lucide:send" data-href="/user-guide/making-a-request">
    Learn how to send various HTTP requests like GET, POST, and PUT. This section covers passing URL parameters, custom headers, and different types of request bodies, including form data and JSON payloads.
  </x-card>
  <x-card data-title="Handling Responses" data-icon="lucide:arrow-down-left" data-href="/user-guide/handling-responses">
    Once a request is sent, you receive a `Response` object. Understand how to access the response body in different formats (text, JSON, binary), check status codes, and read headers.
  </x-card>
  <x-card data-title="Session Objects" data-icon="lucide:book-copy" data-href="/user-guide/session-objects">
    Use `Session` objects to persist parameters and cookies across multiple requests. This improves performance by reusing TCP connections, which is essential for efficient API clients.
  </x-card>
  <x-card data-title="Authentication" data-icon="lucide:key-round" data-href="/user-guide/authentication">
    Many APIs require authentication. Requests provides several built-in schemes, such as Basic and Digest Authentication, to help you secure your requests easily.
  </x-card>
  <x-card data-title="Error Handling" data-icon="lucide:shield-alert" data-href="/user-guide/error-handling">
    Network requests can fail. A robust application must handle connection issues, timeouts, and bad HTTP statuses. Learn to catch and manage exceptions to build more resilient applications.
  </x-card>
</x-cards>

---

After mastering these core concepts, you will be well-equipped to handle most common HTTP interactions. When you're ready for more complex scenarios, such as configuring timeouts, using proxies, or verifying SSL certificates, proceed to the [Advanced Usage](./advanced-usage.md) guide.
