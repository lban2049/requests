# Getting Started

This guide provides the essential steps to get the Requests library up and running. You'll learn how to install it and make your first simple HTTP request.

## Installation

Before you start, ensure you have a supported version of Python installed. Requests officially supports Python 3.9 and newer.

Requests is available on the Python Package Index (PyPI) and can be easily installed using `pip`.

```console Install Requests icon=logos:python
$ python -m pip install requests
```

Once the command completes, you'll have Requests installed in your environment, ready to use.

## Make Your First Request

With Requests installed, making an HTTP request is straightforward. Let's start with a basic `GET` request to retrieve some data from a test endpoint.

```python Make a GET request icon=logos:python
import requests

# Send a GET request to the httpbin.org test endpoint
r = requests.get('https://httpbin.org/get')

# Check the HTTP status code for a successful request (200 OK)
print(f"Status Code: {r.status_code}")

# The response content can be decoded as JSON
print("Response JSON:")
print(r.json())
```

Let's break down what's happening in this code:

1.  **`import requests`**: First, we import the `requests` library.
2.  **`r = requests.get(...)`**: We call the `get()` function to send an HTTP GET request to the specified URL. This function returns a `Response` object containing the server's response.
3.  **`r.status_code`**: This attribute gives you the HTTP status code. A value of `200` indicates that the request was successful.
4.  **`r.json()`**: If the response content is in JSON format, you can use this convenient method to parse it directly into a Python dictionary.

## Next Steps

You've successfully installed Requests and made your first API call. To learn how to send data, handle different HTTP methods, and manage headers, continue to the User Guide.

<x-card data-title="User Guide: Making a Request" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request">
  Learn how to use various HTTP methods like GET, POST, PUT, and how to pass URL parameters, headers, and request bodies.
</x-card>