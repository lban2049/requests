# Getting Started

This guide provides the essential steps to install the Requests library and make your first HTTP request. It's designed to get you up and running in just a few minutes.

## Installation

Before you begin, ensure you have a supported version of Python installed. Requests officially supports Python 3.9 and newer.

Requests is available on the Python Package Index (PyPI) and can be installed with pip:

```console
$ python -m pip install requests
```

This command will download and install Requests along with its required dependencies, such as `urllib3`, `charset_normalizer`, `idna`, and `certifi`.

## Making Your First Request

With Requests installed, making a web request is simple. Let's start with a basic `GET` request to a test endpoint.

```python
import requests

r = requests.get('https://httpbin.org/get')
```

That's it! You've just sent an HTTP GET request. The `r` variable now holds a `Response` object, which contains all the information returned by the server, including the content and status code.

### Inspecting the Response

You can easily inspect the response to see if your request was successful and view the data it contains.

**Check the Status Code**

A `200 OK` status is the standard response for a successful HTTP request. You can check it with the `status_code` attribute:

```python
>>> r.status_code
200
```

**Access Response Content**

Requests can automatically decode JSON responses into a Python dictionary. Use the `.json()` method to access the data:

```python
>>> r.json()
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', ...}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

If the response is not JSON, you can access the raw text content using `.text`:

```python
>>> r.text
'{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", ...
```

## Next Steps

You have successfully installed Requests and made your first API call. To learn about making different types of requests (like `POST` or `PUT`), passing parameters, and handling various response types, continue to the User Guide.

<x-card data-title="Making a Request" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request" data-cta="Continue to User Guide">
Learn how to use various HTTP methods, pass URL parameters, set headers, and send data in the request body.
</x-card>