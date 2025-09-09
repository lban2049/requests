# Overview

Requests is a simple, yet elegant, HTTP library for Python, designed to be used by humans. It allows you to send HTTP/1.1 requests with extreme ease, abstracting away the complexities of manual query string creation and form-encoding.

As one of the most downloaded Python packages, with around **30 million downloads per week**, Requests is trusted by over **1,000,000 repositories** on GitHub. You can certainly put your trust in this code.

Here’s a quick example of what it looks like in action:

```python A Simple GET Request icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# Check the status code
>>> r.status_code
200

# Inspect response headers
>>> r.headers['content-type']
'application/json; charset=utf8'

# Access the response body as text
>>> r.text
'{"authenticated": true, ...'

# Or, decode it as JSON
>>> r.json()
{'authenticated': True, ...}
```

## Key Features

Requests is ready for the demands of building robust and reliable HTTP applications and comes with a wide range of features out of the box:

*   Keep-Alive & Connection Pooling
*   International Domains and URLs
*   Sessions with Cookie Persistence
*   Browser-style TLS/SSL Verification
*   Basic & Digest Authentication
*   Familiar `dict`–like Cookies
*   Automatic Content Decompression and Decoding
*   Multi-part File Uploads
*   SOCKS Proxy Support
*   Connection Timeouts
*   Streaming Downloads
*   Automatic honoring of `.netrc`
*   Chunked HTTP Requests

## Where to Go Next?

Ready to dive in? Here are the next steps to get you started on your journey with Requests.

<x-cards>
  <x-card data-title="Getting Started" data-icon="lucide:rocket" data-href="/getting-started">
    Simple, step-by-step instructions for installing the library and making your first HTTP request.
  </x-card>
  <x-card data-title="User Guide" data-icon="lucide:book-open" data-href="/user-guide">
    Explore the core functionalities of Requests through practical, code-first examples covering common use cases.
  </x-card>
</x-cards>