# Overview

**Requests** is an elegant and simple HTTP library for Python, designed for human beings. It allows you to send HTTP/1.1 requests with extreme ease, abstracting away the complexities of manual query strings, form encoding, and connection management.

As one of the most downloaded Python packages, with around `30 million downloads per week`, Requests is a dependency for over `1,000,000` public repositories on GitHub. You can put your trust in this code.

### Quick Start

Here's a quick example of using Requests to perform a GET request with basic authentication and inspect the response:

```python Basic GET Request icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# Check the status code
print(r.status_code)
# >>> 200

# Inspect response headers
print(r.headers['content-type'])
# >>> 'application/json; charset=utf8'

# Access the response body as text
print(r.text)
# >>> '{"authenticated": true, ...}'

# Or automatically decode it as JSON
print(r.json())
# >>> {'authenticated': True, ...}
```

### Core Features

Requests is ready for the demands of building robust and reliable HTTP–speaking applications. It comes packed with features out of the box.

<x-cards data-columns="2">
  <x-card data-title="Keep-Alive & Connection Pooling" data-icon="lucide:network">
    Reuses underlying TCP connections for performance gains.
  </x-card>
  <x-card data-title="International Domains and URLs" data-icon="lucide:globe">
    Natively supports internationalized domain names for global applications.
  </x-card>
  <x-card data-title="Sessions with Cookie Persistence" data-icon="lucide:cookie">
    Session objects persist parameters and cookies across all requests.
  </x-card>
  <x-card data-title="Browser-style SSL Verification" data-icon="lucide:shield-check">
    Automatically verifies SSL certificates for hosts, just like a web browser.
  </x-card>
  <x-card data-title="Built-in Authentication" data-icon="lucide:key-round">
    Provides elegant, built-in support for Basic and Digest Authentication.
  </x-card>
  <x-card data-title="Automatic Decompression" data-icon="lucide:folder-up">
    Automatically decompresses and decodes response content (e.g., gzip).
  </x-card>
  <x-card data-title="File Uploads" data-icon="lucide:upload">
    Easily upload files using multi-part encoding.
  </x-card>
  <x-card data-title="Connection Timeouts" data-icon="lucide:timer-off">
    Set timeouts to prevent requests from hanging indefinitely.
  </x-card>
</x-cards>

### Full Documentation

A comprehensive API reference and user guide is available on [Read the Docs](https://requests.readthedocs.io), providing in-depth explanations for all features.

![Requests Documentation on Read the Docs](../../../ext/ss.png)

### Next Steps

Ready to integrate Requests into your project? Let's get it installed.

<x-card data-title="Installation" data-icon="lucide:package-plus" data-href="/installation" data-cta="Install Requests">
  Guides you on how to install the library and details its supported Python versions.
</x-card>