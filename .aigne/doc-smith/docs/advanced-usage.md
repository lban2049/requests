# Advanced Usage

While Requests is celebrated for its simplicity in handling common HTTP tasks, it also provides a robust set of features for more complex and demanding scenarios. This section delves into the advanced capabilities that give you granular control over network behavior, security protocols, and the library's core functionality.

Whether you need to configure specific network timings, manage SSL certificates, or extend Requests with custom logic, the tools are available. Below is an overview of the advanced topics covered in this guide.

<x-cards data-columns="3">
  <x-card data-title="Timeouts, Retries, and Proxies" data-icon="lucide:network" data-href="/advanced-usage/timeouts-retries-proxies">
    Learn how to control connection timeouts, automatically retry failed requests, and route your traffic through proxy servers.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    Manage SSL/TLS verification, use custom Certificate Authority (CA) bundles, and provide client-side certificates.
  </x-card>
  <x-card data-title="Custom Adapters and Hooks" data-icon="lucide:plug-zap" data-href="/advanced-usage/adapters-and-hooks">
    Extend the functionality of Requests by creating custom Transport Adapters and using the event hook system to modify request behavior.
  </x-card>
</x-cards>

## Timeouts, Retries, and Proxies

Network conditions can be unpredictable. Requests allows you to prevent your application from hanging indefinitely by setting a `timeout` on your requests. You can specify timeouts for connecting to the server and for waiting for a response.

For handling transient network errors, you can configure Requests to automatically retry a failed request. This is accomplished by mounting an `HTTPAdapter` with a custom `Retry` strategy to a `Session` object.

Additionally, if you need to route your requests through an intermediary, Requests supports HTTP and SOCKS proxies. You can configure proxies on a per-request basis or for an entire `Session`.

For a detailed guide on these features, see [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md).

## SSL Certificate Verification

Security is a primary concern in network communication. By default, Requests verifies the SSL certificates for HTTPS requests to ensure you are communicating with the server you expect. You can customize this behavior by passing the `verify` parameter. This can be set to a boolean to enable or disable verification, or to a string path for a custom CA bundle file or directory.

For services that require client-side certificate authentication (mTLS), you can provide a certificate using the `cert` parameter.

Explore these security configurations in the [SSL Certificate Verification](./advanced-usage-ssl-cert-verification.md) section.

## Custom Adapters and Hooks

Requests features a modular design that allows for significant customization. Transport Adapters are the core of this system, providing the logic for handling HTTP and HTTPS requests. You can create your own Transport Adapter to implement custom transport protocols or modify how connections are managed.

Requests also provides a hook system that lets you attach callbacks to specific points in the request-response cycle. The primary hook available is `response`, which allows you to inspect or modify the response object before it's returned from the initial request call.

Learn how to extend Requests for your specific needs in [Custom Adapters and Hooks](./advanced-usage-adapters-and-hooks.md).

---

By mastering these advanced features, you can adapt Requests to a wide range of complex networking tasks. For a complete breakdown of all classes and methods, proceed to the [API Reference](./api-reference.md).