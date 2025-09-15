# Overview

![Requests Logo](../../../ext/requests-logo.png)

**Requests** is a simple, yet elegant, HTTP library for Python, designed to make HTTP requests humane and straightforward. It abstracts the complexities of making requests behind a beautiful, simple API so you can focus on interacting with services and consuming data in your application.

Requests is one of the most downloaded Python packages in the world, with over `30 million` weekly downloads. It is trusted by more than `1,000,000` repositories, making it a reliable foundation for building robust and reliable HTTP-speaking applications.

### A Quick Example

See how easy it is to make a `GET` request with authentication and access the response data.

```python Basic GET Request icon=logos:python
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

>>> r.status_code
200

>>> r.headers['content-type']
'application/json; charset=utf8'

>>> r.encoding
'utf-8'

>>> r.text
'{"authenticated": true, ...}'

>>> r.json()
{'authenticated': True, ...}
```

With Requests, you don’t need to manually add query strings to your URLs or form-encode your `POST` data. Just use the intuitive methods and let the library handle the hard work.

### Features & Best Practices

Requests is built with modern web development needs in mind, offering a powerful set of features out of the box.

| Feature                       | Description                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| Keep-Alive & Connection Pooling | Reuses underlying TCP connections for significant performance improvements. |
| International Domains and URLs  | Natively supports non-ASCII domains and URLs.                               |
| Sessions with Cookie Persistence| Persist cookies across all requests made from a Session object.             |
| Browser-style TLS/SSL Verification | Automatically verifies server certificates just like a web browser.           |
| Basic & Digest Authentication   | Built-in, easy-to-use authentication helpers.                               |
| Familiar `dict`–like Cookies    | Manage cookies using a simple dictionary interface.                         |
| Automatic Content Decompression | Automatically decompresses gzip, deflate, and brotli encoded responses.     |
| Multi-part File Uploads         | Simple interface for uploading files.                                       |
| SOCKS Proxy Support           | Route your requests through a SOCKS proxy with an extra dependency.         |
| Connection Timeouts           | Prevent requests from hanging indefinitely by setting timeouts.             |
| Streaming Downloads           | Download large files efficiently by iterating over the response content.    |
| Automatic honoring of `.netrc`  | Uses authentication information from your `.netrc` file if available.       |

### Supported Versions

Requests officially supports Python 3.9+.

---

Ready to get started? Head over to the [Getting Started](./getting-started.md) guide to install the library and make your first request.

<x-card data-title="Full API Reference and User Guide" data-image="../../../ext/ss.png" data-href="https://requests.readthedocs.io" data-cta="Read the Docs" >
For a comprehensive guide to every module, class, and function, explore our complete documentation hosted on Read the Docs.
</x-card>