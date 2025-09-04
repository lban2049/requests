# Overview

![Requests Logo](../../../ext/requests-logo.png)

Requests is an elegant and simple HTTP library for Python, built for human beings. It allows you to send HTTP/1.1 requests with extreme ease, without the need to manually add query strings to your URLs or form-encode your `PUT` & `POST` data.

Today, Requests is one of the most downloaded Python packages, with around `30M downloads / week`, and is a dependency for over `1,000,000` repositories on GitHub. You can put your trust in this code.

Here is a simple example of using Requests to perform a `GET` request with basic authentication:

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

## Features

Requests is ready for the demands of building robust and reliable HTTP-speaking applications. It comes with a wide range of features out of the box.

<x-cards data-columns="3">
  <x-card data-title="Connection Management" data-icon="lucide:network">
    Includes Keep-Alive and connection pooling for efficient network usage.
  </x-card>
  <x-card data-title="International Support" data-icon="lucide:globe">
    Natively handles International Domains and URLs.
  </x-card>
  <x-card data-title="Session Persistence" data-icon="lucide:cookie">
    Use Session objects with cookie persistence across requests.
  </x-card>
  <x-card data-title="SSL Verification" data-icon="lucide:shield-check">
    Features browser-style TLS/SSL verification for secure connections.
  </x-card>
  <x-card data-title="Authentication" data-icon="lucide:key-round">
    Built-in support for Basic & Digest Authentication.
  </x-card>
  <x-card data-title="Cookie Handling" data-icon="lucide:cookie">
    Familiar dict-like interface for managing cookies.
  </x-card>
  <x-card data-title="Content Decompression" data-icon="lucide:file-archive">
    Automatic content decompression and decoding.
  </x-card>
  <x-card data-title="File Uploads" data-icon="lucide:upload">
    Easily handle multi-part file uploads.
  </x-card>
  <x-card data-title="Proxy Support" data-icon="lucide:server">
    SOCKS proxy support for routing requests.
  </x-card>
  <x-card data-title="Timeouts" data-icon="lucide:timer">
    Configure connection timeouts to prevent hanging requests.
  </x-card>
  <x-card data-title="Streaming" data-icon="lucide:download">
    Supports streaming downloads for large files.
  </x-card>
  <x-card data-title="Chunked Requests" data-icon="lucide:box-select">
    Ability to send chunked HTTP requests.
  </x-card>
</x-cards>

## Full Documentation

For a comprehensive guide, including detailed API references and advanced usage patterns, please refer to the official documentation hosted on Read the Docs.

[![Read the Docs](https://raw.githubusercontent.com/psf/requests/main/ext/ss.png)](https://requests.readthedocs.io)

## Next Steps

Ready to get started? Proceed to the [Getting Started](./getting-started.md) guide for installation instructions and to make your first request.