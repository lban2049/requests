# Advanced Usage

While the simple functional API of Requests is perfect for basic HTTP tasks, real-world applications often demand more control, performance, and robustness. The advanced features of Requests provide the tools you need to build sophisticated HTTP clients that can handle persistent connections, complex authentication schemes, and network failures gracefully.

This section explores these powerful capabilities. By mastering them, you can optimize your application's network performance, maintain state across multiple requests, and write resilient code that anticipates and handles potential issues.

<x-cards data-columns="2">
  <x-card data-title="Session Objects" data-icon="lucide:book-copy" data-href="/advanced-usage/session-objects">
    Learn how to persist cookies and settings across multiple requests, and benefit from connection pooling for significant performance gains using Session objects.
  </x-card>
  <x-card data-title="Authentication" data-icon="lucide:key-round" data-href="/advanced-usage/authentication">
    Dive into various authentication mechanisms, including built-in support for Basic and Digest Auth, and learn how to implement your own custom authentication schemes.
  </x-card>
  <x-card data-title="Proxies" data-icon="lucide:server" data-href="/advanced-usage/proxies">
    Discover how to route your HTTP and HTTPS requests through proxy servers, a common requirement for corporate environments and web scraping tasks.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-verification">
    Understand how Requests handles SSL certificate verification to ensure secure connections, and how you can use custom CA bundles or client-side certificates.
  </x-card>
  <x-card data-title="Timeouts" data-icon="lucide:timer" data-href="/advanced-usage/timeouts">
    Prevent your application from hanging indefinitely by setting connect and read timeouts, ensuring your network requests fail fast when a server is unresponsive.
  </x-card>
  <x-card data-title="Error Handling" data-icon="lucide:alert-triangle" data-href="/advanced-usage/error-handling">
    Explore the comprehensive exception hierarchy in Requests. Learn to catch and handle specific network, protocol, and timeout errors to build resilient applications.
  </x-card>
</x-cards>

By leveraging these advanced features, you can move beyond simple scripts and build professional-grade applications that interact with web services reliably and efficiently. Once you are familiar with these concepts, you may want to consult the [API Reference](./api-reference.md) for a detailed breakdown of all available classes and methods.