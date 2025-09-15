# Overview

**Requests** is a simple, yet elegant, HTTP library for Python, designed to make HTTP requests humane and straightforward. It abstracts the complexities of making requests behind a beautiful, simple API, so you can focus on interacting with services and consuming data in your application.

> Python HTTP for Humans.

Requests is one of the most downloaded Python packages in the world, with over `30 million downloads per week`. It is trusted by more than `1,000,000` repositories on GitHub, making it a reliable and robust choice for your projects.

## Why Use Requests?

Sending HTTP/1.1 requests is incredibly easy with Requests. You no longer need to manually add query strings to your URLs or form-encode your `POST` data. The library handles these tasks seamlessly, allowing for cleaner and more readable code.

Here's a quick look at how simple it is to make a `GET` request with authentication:

```python Basic GET Request icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# Check the status code
print(r.status_code)
# >>> 200

# Access response headers
print(r.headers['content-type'])
# >>> 'application/json; charset=utf8'

# Access the response body as text
print(r.text)
# >>> '{"authenticated": true, ...}'

# Or, decode it as JSON
print(r.json())
# >>> {'authenticated': True, ...}
```

## Core Features

Requests is packed with features that support the demands of modern, robust, and reliable HTTP applications.

| Feature                       | Description                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| Keep-Alive & Connection Pooling | Reuses underlying TCP connections for significant performance improvements.   |
| International Domains & URLs  | Natively supports internationalized domain names for global applications.   |
| Sessions with Cookie Persistence| Persist parameters and cookies across multiple requests with Session objects. |
| Browser-style TLS/SSL Verification | Verifies SSL certificates for HTTPS requests by default, just like a browser. |
| Basic & Digest Authentication | Built-in, easy-to-use authentication support.                               |
| Familiar `dict`-like Cookies  | Manage cookies with a simple dictionary-like interface.                     |
| Automatic Decompression       | Automatically decompresses `gzip`, `deflate`, and `brotli` encoded content.     |
| Multi-part File Uploads       | Streamlined interface for uploading files.                                  |
| Connection Timeouts           | Prevents requests from hanging indefinitely by setting connection timeouts.   |
| Streaming Downloads           | Download large files without loading the entire content into memory at once.|
| Chunked HTTP Requests         | Supports chunked transfer encoding for streaming uploads.                   |

## Installation

Getting started with Requests is as simple as running a single command. The library is available on PyPI and officially supports **Python 3.9+**.

```console
$ python -m pip install requests
```

For a more detailed guide on installation and making your first request, head over to the [Getting Started](./getting-started.md) section.

---

Ready to dive deeper? The complete API reference and user guide are available on [Read the Docs](https://requests.readthedocs.io).

[![Read the Docs](https://raw.githubusercontent.com/psf/requests/main/ext/ss.png)](https://requests.readthedocs.io)