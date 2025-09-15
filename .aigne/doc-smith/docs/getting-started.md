# Getting Started

This guide provides a straightforward path to installing the Requests library and making your first HTTP request. You'll be fetching data from the web in just a few minutes.

## Installation

Before you begin, ensure you have a supported version of Python installed. Requests officially supports Python 3.9 and newer.

To install Requests, open your terminal or command prompt and use `pip`, the Python package installer:

```console Installing Requests icon=logos:python
$ python -m pip install requests
```

This command will download and install the latest version of Requests, along with its essential dependencies like `urllib3`, `idna`, `charset_normalizer`, and `certifi`, so you have everything you need to get started.

## Make Your First Request

With Requests installed, making a web request is incredibly simple. Let's start with a basic `GET` request to retrieve some data from a test service.

The following example demonstrates how to make a request, inspect the response, and access its content.

```python Your First Request icon=logos:python
import requests

# Make a GET request to a simple test endpoint
r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# 1. Check the HTTP status code
# A 200 OK status means the request was successful
print(f"Status Code: {r.status_code}")

# 2. Access Response Headers
# Headers are returned in a dictionary-like object
print(f"Content-Type: {r.headers['content-type']}")

# 3. Access the Response Body as text
# The .text attribute holds the raw string content
print(f"Response Text: {r.text}")

# 4. Access the Response Body as JSON
# The .json() method decodes a JSON response into a Python dictionary
json_data = r.json()
print(f"JSON Data: {json_data}")
```

Let's break down what's happening here:

1.  **`import requests`**: We begin by importing the library.
2.  **`requests.get(...)`**: This is the core of the library. It constructs and sends an HTTP `GET` request to the specified URL. In this case, we also pass an `auth` tuple to handle Basic Authentication effortlessly.
3.  **`r.status_code`**: The `r` object is an instance of the `Response` class, containing the server's response. The `status_code` attribute lets you check if the request was successful. A value of `200` indicates success.
4.  **`r.headers`**: This dictionary-like object gives you access to all the HTTP response headers.
5.  **`r.text`**: This attribute provides the response payload as a plain string.
6.  **`r.json()`**: When you're working with APIs that return JSON, this built-in method is a lifesaver. It automatically decodes the response text into a Python dictionary or list, making the data immediately accessible.

## What's Next?

Congratulations! You've successfully installed Requests and fetched data from the web. You now know the basics of making a request and handling the response.

To dive deeper into the library's features, the User Guide is the perfect next step. Learn how to send data, customize headers, manage sessions, and more.

<x-card data-title="User Guide: Making a Request" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request">
Explore different HTTP methods like POST and PUT, pass URL parameters, and handle various types of request bodies.
</x-card>