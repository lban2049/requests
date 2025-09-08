# Getting Started

This guide provides the essential steps to install the Requests library and make your first HTTP request. It's designed to get you up and running quickly so you can start building.

## Installation

Before you begin, ensure you have a supported version of Python (3.9+). Requests is available on the Python Package Index (PyPI) and can be installed using `pip`.

```console Installing Requests icon=logos:python
$ python -m pip install requests
```

This single command downloads and installs Requests along with its necessary dependencies, including `urllib3`, `certifi`, `charset_normalizer`, and `idna`.

## Make Your First Request

With Requests installed, you can start making HTTP requests. The process is straightforward and mirrors the simplicity of the web. Here is how you can send a `GET` request to a test endpoint.

```python Make a GET request icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')
```

After executing this code, you will have a `Response` object named `r`. This object contains all the information returned by the server, such as the content, status code, and headers.

## Inspect the Response

The `Response` object provides easy access to the details of the server's reply. Here are some of the most common attributes you will use.

### Status Code

You can check the HTTP status code to verify if the request was successful. A status code of `200` indicates success.

```python Check the status code
>>> r.status_code
200
```

### Response Headers

The server's response headers are available in a dictionary-like object. You can access any header by its key.

```python Access response headers
>>> r.headers['content-type']
'application/json; charset=utf8'
```

### Response Body

For text-based responses, you can access the content as a string using the `.text` attribute.

```python Get the response body as text
>>> r.text
'{"authenticated": true, ...'
```

If the endpoint returns JSON, as is common with many APIs, Requests has a convenient built-in JSON decoder that will parse the content into a Python dictionary.

```python Decode the JSON response
>>> r.json()
{'authenticated': True, ...}
```

## Next Steps

You have now successfully installed Requests and performed a basic `GET` request. To dive deeper into the library's capabilities, the next step is to explore the various ways you can construct requests and handle different types of data.

<x-card data-title="Making a Request" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request" data-cta="Continue to User Guide">
  Learn how to use various HTTP methods like GET, POST, and PUT, and how to pass URL parameters, headers, and request bodies.
</x-card>