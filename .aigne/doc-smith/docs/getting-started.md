# Getting Started

This page provides a straightforward guide to installing the Requests library and making your first HTTP request. You'll be up and running in a few minutes.

## Prerequisites

Before installing Requests, ensure you have a compatible Python version. Requests officially supports Python 3.9 and newer.

## Installation

The recommended way to install Requests is with pip. Open your terminal and run the following command:

```console
$ python -m pip install requests
```

This command fetches the latest version of Requests from the Python Package Index (PyPI) and installs it, along with its required dependencies such as `urllib3`, `charset_normalizer`, `idna`, and `certifi`.

## Make Your First Request

With Requests installed, you can begin making web requests. The example below shows how to send a simple `GET` request and inspect what comes back.

```python
import requests

# Send a GET request to a public test API
r = requests.get('https://httpbin.org/get')

# Check the HTTP status code (200 indicates success)
print(f"Status Code: {r.status_code}")

# Access response headers
print(f"Content-Type: {r.headers['content-type']}")

# Get the response body as a Python dictionary
print("Response JSON:")
print(r.json())
```

Running this script will produce an output similar to this:

```text
Status Code: 200
Content-Type: application/json; charset=utf8
Response JSON:
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=...'}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

This demonstrates the basic workflow: use a function like `requests.get()` to make a request and then use the returned `Response` object to access the details of the server's response.

## Next Steps

Now that you have successfully installed Requests and made a basic request, you are ready to explore its other features.

<x-card data-title="User Guide" data-icon="lucide:book-open" data-href="/user-guide" data-cta="Explore the Guide">
  Dive into the User Guide to learn about making different kinds of requests, handling various response types, using Session objects for performance, and more.
</x-card>

The guide provides comprehensive examples for most of the library's core features.