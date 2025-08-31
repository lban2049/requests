# Getting Started

This page provides a straightforward guide to installing the Requests library and making your first HTTP request. You'll be up and running in a few minutes.

## Prerequisites

Before installing Requests, ensure you have a compatible Python version. As noted in the project's setup files, Requests officially supports Python 3.9 and newer.

## Installation

The recommended way to install Requests is with `pip`, Python's package installer. Open your terminal and run the following command:

```console
$ python -m pip install requests
```

This command fetches the latest version of Requests from the Python Package Index (PyPI) and installs it. It also installs the library's required dependencies, such as `urllib3`, `charset_normalizer`, `idna`, and `certifi`, which handle the low-level details of making HTTP requests securely and reliably.

## Making Your First Request

With Requests installed, you can begin making web requests. The fundamental process involves sending a request to a URL and then processing the response that comes back from the server.

The following diagram illustrates this basic request-response cycle:

```d2
shape: sequence_diagram
direction: right

"Your Python Script"
"Requests Library"
"Web Server (httpbin.org)"

"Your Python Script" -> "Requests Library": calls `requests.get(...)`
"Requests Library" -> "Web Server (httpbin.org)": "Sends HTTP GET Request" {
  style.animated: true
}
"Web Server (httpbin.org)" -> "Requests Library": "Returns HTTP Response" {
  style.animated: true
  style.stroke: green
}
"Requests Library" -> "Your Python Script": "Returns Response Object" {
  style.stroke: green
}
```

Here’s how to implement this in code. The example below shows how to send a simple `GET` request and inspect what the server returns.

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
Content-Type: application/json
Response JSON:
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate, br', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=...'}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

This demonstrates the basic workflow: use a function like `requests.get()` to make a request, and then use the returned `Response` object (`r`) to access the details of the server's response, including the status code, headers, and the JSON body.

## Next Steps

Now that you have successfully installed Requests and made a basic request, you are ready to explore its other features.

<x-card data-title="User Guide" data-icon="lucide:book-open" data-href="/user-guide" data-cta="Explore the Guide">
  Dive into the User Guide to learn about making different kinds of requests, handling various response types, using Session objects for performance, and more.
</x-card>

The guide provides comprehensive examples for most of the library's core features.
