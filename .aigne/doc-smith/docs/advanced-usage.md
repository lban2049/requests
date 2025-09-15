# Advanced Usage

While Requests is renowned for its 'HTTP for Humans' simplicity, it also provides a robust set of advanced features for handling complex and demanding scenarios. Once you've mastered the basics covered in the [User Guide](./user-guide.md), you can dive deeper to gain fine-grained control over network behavior, security, and extensibility.

This section provides a high-level overview and links to detailed guides on these advanced topics. You'll learn how to build more resilient, secure, and customized HTTP clients.

<x-cards data-columns="3">
  <x-card data-title="Timeouts, Retries, and Proxies" data-icon="lucide:timer" data-href="/advanced-usage/timeouts-retries-proxies">
    Protect your application from unreliable network conditions by setting request timeouts, configuring automatic retries, and routing requests through proxy servers.
  </x-card>
  <x-card data-title="SSL Certificate Verification" data-icon="lucide:shield-check" data-href="/advanced-usage/ssl-cert-verification">
    Take full control of your application's security by managing SSL/TLS verification, using custom CA bundles, and providing client-side certificates.
  </x-card>
  <x-card data-title="Custom Adapters and Hooks" data-icon="lucide:puzzle" data-href="/advanced-usage/adapters-and-hooks">
    Extend the core functionality of Requests by creating custom Transport Adapters for different transport protocols and using the event hook system to modify the request cycle.
  </x-card>
</x-cards>

### Example: Adapter with Custom Retries

A common advanced use case is to configure a `Session` to automatically retry requests that fail due to transient network issues. This is accomplished by creating an `HTTPAdapter` with a custom retry strategy.

```python title="Mounting an Adapter with Retries" icon=logos:python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()

# Define a retry strategy for specific HTTP status codes and methods
retry_strategy = Retry(
    total=3,
    status_forcelist=[429, 500, 502, 503, 504],
    allowed_methods=["HEAD", "GET", "OPTIONS"]
)

# Create an adapter with this retry strategy and mount it to the session
adapter = HTTPAdapter(max_retries=retry_strategy)
session.mount("https://", adapter)
session.mount("http://", adapter)

try:
    # Any request made with this session will now use the retry strategy
    response = session.get("https://api.example.com/data")
    print("Request successful!")
except requests.exceptions.RequestException as e:
    print(f"Request failed after multiple retries: {e}")
```

This example only scratches the surface of what's possible. Explore the detailed guides to fully leverage the power of Requests.

### Next Steps

After exploring these topics, you may want to consult the complete [API Reference](./api-reference.md) for a comprehensive look at all available classes and methods.