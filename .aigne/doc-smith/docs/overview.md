# Overview

![Requests Logo](../../../ext/requests-logo.svg)

Requests is an elegant and simple HTTP library for Python, designed for human beings. It allows you to send HTTP/1.1 requests with ease, handling the complexities of query strings, form encoding, and connection management. As one of the most downloaded Python packages—with approximately 30 million downloads per week—and a dependency for over 1,000,000 repositories on GitHub, Requests provides reliable code you can trust.

### Quick Example

Making a web request is straightforward. Here’s how to fetch a web page and check the response:

```python
import requests

r = requests.get('https://www.python.org')

>>> r.status_code
200

>>> b'Python is a programming language' in r.content
True
```

This example sends a `GET` request. The library manages the connection and returns a `Response` object containing the server's data and metadata.

### The Request-Response Cycle

The library simplifies the interaction between your application and a web server. The flow involves creating a request, receiving a response, and processing the returned data.

```d2
direction: down

App: "Your Application"
Lib: "Requests Library"
Server: "Web Server"
Process: "Process Data"

App -> Lib: "calls requests.get(...)"
Lib -> Server: "Sends HTTP Request"
Server -> Lib: "Returns HTTP Response"
Lib -> App: "Creates Response Object"
App -> Process: "Accesses r.status_code, r.content"
```

### Core Features

Requests is built to support the development of reliable HTTP applications and includes a wide range of features.

<x-cards data-columns="3">
  <x-card data-title="Connection Pooling" data-icon="lucide:network">
    Reuses underlying TCP connections with Keep-Alive for improved performance.
  </x-card>
  <x-card data-title="Session Objects" data-icon="lucide:cookie">
    Persist parameters, cookies, and headers across multiple requests for stateful interactions.
  </x-card>
  <x-card data-title="SSL Verification" data-icon="lucide:shield-check">
    Automatically verifies server certificates for HTTPS requests, similar to a web browser.
  </x-card>
  <x-card data-title="Automatic Decompression" data-icon="lucide:file-archive">
    Natively decodes `gzip` and `deflate` compressed content.
  </x-card>
  <x-card data-title="File Uploads" data-icon="lucide:upload">
    Simplifies sending multi-part file uploads with an intuitive interface.
  </x-card>
  <x-card data-title="Built-in Authentication" data-icon="lucide:key-round">
    Includes simple helpers for Basic and Digest authentication schemes.
  </x-card>
</x-cards>

---

This overview covers the purpose and main features of the Requests library. To install it and make your first request, continue to the next section.

➡️ **Next: [Getting Started](./getting-started.md)**
