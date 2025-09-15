# Advanced Usage

While Requests is celebrated for its simplicity, it also packs powerful tools for navigating complex HTTP scenarios. When you need to move beyond standard requests, you can gain granular control over network behavior. This includes setting precise timeouts, implementing robust retry strategies for unreliable connections, routing traffic through proxies, managing SSL/TLS certificate verification, and even extending the library's core functionality with custom Transport Adapters and event hooks.

This section provides a high-level overview and links to detailed guides for these advanced features, empowering you to build more resilient and sophisticated applications.

<x-cards data-columns="3">
  <x-card data-title="Timeouts, Retries, and Proxies" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    Learn how to configure network behavior by setting request timeouts, automatic retries for failed connections, and routing requests through proxies.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    Manage SSL/TLS verification by using custom CA bundles, client-side certificates, or disabling verification for specific cases.
  </x-card>
  <x-card data-title="Custom Adapters and Hooks" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    Extend the functionality of Requests by creating custom Transport Adapters and using the event hook system to modify behavior.
  </x-card>
</x-cards>

Mastering these features will allow you to tackle nearly any HTTP communication challenge. Once you're familiar with these concepts, dive into the [API Reference](./api-reference.md) for a comprehensive breakdown of every class, method, and function available in the library.