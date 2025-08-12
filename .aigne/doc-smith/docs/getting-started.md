# Getting Started

Requests simplifies sending HTTP/1.1 requests. This section will guide you through installing the Requests library and executing your very first HTTP request.

## Installation

Requests is available on PyPI, the Python Package Index. You can install it using pip, Python's package installer.

To install Requests, open your terminal or command prompt and run the following command:

```console
$ python -m pip install requests
```

Requests officially supports Python 3.9 and newer versions. If you are using an older Python version, you will need to upgrade to a supported version or pin to an older Requests release (before 2.32.0).

Requests is a widely adopted Python package, downloaded approximately 30 million times per week and depended upon by over 1,000,000 repositories, according to GitHub.

[![Downloads](https://static.pepy.tech/badge/requests/month)](https://pepy.tech/project/requests)
[![Supported Versions](https://img.shields.io/pypi/pyversions/requests.svg)](https://pypi.org/project/requests)
[![Contributors](https://img.shields.io/github/contributors/psf/requests.svg)](https://github.com/psf/requests/graphs/contributors)

## Making Your First Request

Once installed, you can start using Requests to interact with web services. Here is a basic example of how to make a GET request and inspect the response:

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

This example demonstrates a GET request to `https://httpbin.org/basic-auth/user/pass` with basic authentication. Let's break down the response:

-   `r.status_code`: This retrieves the HTTP status code of the response. A `200` indicates a successful request.
-   `r.headers['content-type']`: This accesses the `Content-Type` header, which specifies the media type of the resource. Here, it's `application/json; charset=utf8`.
-   `r.encoding`: This shows the detected character encoding for the response content, which is `utf-8`.
-   `r.text`: This provides the content of the response in Unicode, making it easy to read as a string.
-   `r.json()`: If the response contains JSON data, this method automatically parses it into a Python dictionary or list, providing convenient access to structured data.

Requests abstracts away the complexities of manually adding query strings or form-encoding data, allowing you to send requests with minimal effort.

---

Now that you have successfully installed Requests and made your first HTTP request, you are ready to explore its capabilities further. Proceed to the [Core Concepts](./core-concepts.md) section to understand the fundamental building blocks of the Requests library.