# Getting Started

This guide provides the essential steps to install the Requests library and make your first HTTP request. Follow along to get up and running in just a few minutes.

## Installation

First, you'll need to install the library. Requests is available on the Python Package Index (PyPI) and can be installed with pip.

```console Install with pip icon=logos:python
$ python -m pip install requests
```

Requests officially supports Python 3.9 and newer. Please ensure your environment meets this requirement before you proceed.

## Make Your First Request

With Requests installed, making an HTTP request is straightforward. Let's start by fetching some data from the GitHub Events API.

```python Making a GET request icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')
```

Now, you have a `Response` object called `r`. This object contains all the information from the server's response.

You can easily check if the request was successful by inspecting the status code:

```python Check the status code
>>> r.status_code
200
```

A `200` status code indicates that the request was successful. Other codes, like `404`, would signify that the resource was not found.

Requests also makes it simple to access the response payload. For text-based responses, you can use the `.text` attribute:

```python Access response content as text
>>> r.text
'{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.32.3", \n    "X-Amzn-Trace-Id": "Root=1-66a93555-0123456789abcdef01234567"\n  }, \n  "origin": "127.0.0.1", \n  "url": "https://httpbin.org/get"\n}'
```

For APIs that return JSON, which is very common, you can use the built-in `.json()` method to parse the content directly into a Python dictionary:

```python Decode JSON response
>>> r.json()
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=1-66a93555-0123456789abcdef01234567'}, 'origin': '127.0.0.1', 'url': 'https://httpbin.org/get'}
```

## Next Steps

Congratulations! You've successfully installed Requests and made your first API call. You are now ready to explore more of what the library has to offer.

To dive deeper into features like sending data with POST requests, using Session objects for performance, and handling authentication, proceed to the [User Guide](./user-guide.md).