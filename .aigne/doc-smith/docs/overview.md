# Overview

![Requests Logo](../../../ext/requests-logo.svg)

Requests is an elegant and simple HTTP library for Python, built for human beings. It allows you to send HTTP/1.1 requests with extreme ease, abstracting away the complexities of network connections so you can focus on interacting with services and consuming data in your application.

It is one of the most downloaded Python packages, with around 30 million downloads per week, and is a dependency for over 1,000,000 repositories on GitHub. You can put your trust in this code.

```d2
direction: down

"Your Python App": {
  shape: rectangle
}

"Requests Library": {
  shape: package
  "import requests"
}

"Web Server / API": {
  shape: cylinder
  "Remote HTTP Service"
}

"Your Python App" -> "Requests Library": "Makes simple API calls\n(e.g., requests.get())"
"Requests Library" -> "Web Server / API": "Handles complex HTTP details\n(Connection Pooling, SSL, etc.)"
"Web Server / API" -> "Requests Library": "HTTP Response"
"Requests Library" -> "Your Python App": "Returns a simple Response object"
```

## A Simple Request

With Requests, you can perform a complex authenticated GET request in just a few lines of code and interact with the response data using intuitive methods.

```python
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
'{"authenticated": true, ...'
>>> r.json()
{'authenticated': True, ...}
```

## Core Features

Requests is ready to support robust and reliable HTTP-speaking applications with a comprehensive feature set.

<x-cards data-columns="2">
  <x-card data-title="Connection Management" data-icon="lucide:plug-zap">
    Features like Keep-Alive, connection pooling, and connection timeouts ensure robust and efficient network communication.
  </x-card>
  <x-card data-title="Authentication" data-icon="lucide:key-round">
    Built-in support for Basic and Digest authentication schemes.
  </x-card>
  <x-card data-title="Session Persistence" data-icon="lucide:cookies">
    Use Session objects to persist cookies and other parameters across multiple requests to the same host.
  </x-card>
  <x-card data-title="Data Handling" data-icon="lucide:file-code-2">
    Automatic content decompression, multi-part file uploads, and a simple `.json()` method for handling JSON data.
  </x-card>
  <x-card data-title="SSL Verification" data-icon="lucide:shield-check">
    Provides browser-style TLS/SSL verification by default for secure connections.
  </x-card>
  <x-card data-title="Proxy Support" data-icon="lucide:route">
    Easily route your requests through SOCKS proxies.
  </x-card>
</x-cards>

## Next Steps

<x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started" data-cta="Install Requests">
  Ready to begin? Follow the simple, step-by-step instructions to install the library and make your first HTTP request.
</x-card>