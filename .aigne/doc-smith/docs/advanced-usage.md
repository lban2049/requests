# Advanced Usage

Once you've mastered the basics of making requests, you'll often encounter real-world scenarios that require more sophisticated configurations. This section delves into the advanced features of Requests, empowering you to handle complex network behavior, security requirements, and custom logic with confidence.

You'll learn how to fine-tune network operations with timeouts and retries, manage secure connections with SSL certificates, and even extend the library's core functionality with custom adapters and hooks.

<x-cards data-columns="3">
  <x-card data-title="Timeouts, Retries, and Proxies" data-icon="lucide:timer" data-href="/advanced-usage/timeouts-retries-proxies">
    Gain granular control over network operations. Learn to prevent requests from hanging indefinitely by setting timeouts, automatically retry failed requests to build resilient applications, and route your traffic through proxies for security or access purposes.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    Manage the security of your HTTPS connections. This guide covers how to use custom CA bundles, provide client-side certificates for mutual TLS authentication, and when it might be appropriate (with caution) to disable SSL verification.
  </x-card>
  <x-card data-title="Custom Adapters and Hooks" data-icon="lucide:plug-zap" data-href="/advanced-usage/adapters-and-hooks">
    Extend Requests to fit your unique needs. Discover how to create custom Transport Adapters to implement different transport protocols or connection logic, and use the hook system to register callbacks that modify requests or inspect responses.
  </x-card>
</x-cards>

By mastering these advanced features, you can build robust, secure, and highly customized HTTP clients. When you're ready to explore every class and method in detail, the complete API Reference is your next destination.

---

**Next**: [Dive into the API Reference](./api-reference.md)