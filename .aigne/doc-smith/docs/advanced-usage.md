# Advanced Usage

Once you've mastered the basics, you might need to handle more complex networking scenarios. This section covers advanced features of the Requests library, allowing you to fine-tune your HTTP interactions for performance, reliability, and security.

You'll learn how to manage network timeouts, automatically retry failed requests, route traffic through proxies, customize SSL certificate verification, and even extend Requests' core functionality with custom adapters and hooks.

Explore the topics below to gain deeper control over your HTTP requests.

<x-cards data-columns="3">
  <x-card data-title="Timeouts, Retries, and Proxies" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    Control network behavior by setting timeouts to prevent requests from hanging, configuring automatic retries for transient network failures, and routing your requests through HTTP or SOCKS proxies.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:lock" data-href="/advanced-usage/ssl-cert-verification">
    Manage how Requests handles SSL/TLS certificates. Learn to use custom Certificate Authority (CA) bundles, provide client-side certificates for mutual TLS authentication, or disable verification when necessary.
  </x-card>
  <x-card data-title="Custom Adapters and Hooks" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    Extend Requests to meet unique requirements. Create custom Transport Adapters to handle different transport protocols or modify connection logic, and use the built-in hook system to inspect and modify response objects.
  </x-card>
</x-cards>

These advanced features provide the flexibility needed to build robust and reliable applications. After exploring these topics, you may want to consult the detailed [API Reference](./api-reference.md) for a comprehensive look at all available classes and methods.