# Error Handling

When working with network requests, things can go wrong. Servers can be unavailable, connections can drop, or you might encounter an invalid URL. Requests is designed to handle these situations by raising exceptions. This allows you to build resilient applications that can gracefully manage failures.

All exceptions raised by Requests inherit from the base exception `requests.exceptions.RequestException`.

## Unsuccessful Status Codes: `HTTPError`

One of the most common checks is to see if a request was successful. If the server returns an error status code (in the 4xx or 5xx range), Requests will not automatically raise an exception. However, you can use the `response.raise_for_status()` method to do so.

If the request was unsuccessful, an `HTTPError` will be raised.

```python
import requests

for status_code in [404, 503]:
    try:
        url = f'https://httpbin.org/status/{status_code}'
        response = requests.get(url)

        # This will raise an HTTPError if the HTTP request returned an unsuccessful status code.
        response.raise_for_status()
    except requests.exceptions.HTTPError as err:
        print(f'HTTP error for status {status_code}: {err}')
    except Exception as err:
        print(f'An unexpected error occurred: {err}')
```

This code will produce output for both the 404 (Client Error) and 503 (Server Error) status codes, demonstrating how `raise_for_status()` helps catch these issues.

## Connection Problems: `ConnectionError`

Network-level errors, such as DNS failures, refused connections, or other connectivity issues, will raise a `ConnectionError`.

```python
import requests

try:
    response = requests.get('https://this-domain-does-not-exist.com')
except requests.exceptions.ConnectionError as err:
    print(f'Connection error occurred: {err}')
```

This exception is a good catch-all for when your application simply can't reach the target server.

## Timeouts

You can configure requests to stop waiting for a response after a given number of seconds. If the timeout is reached, a `Timeout` exception is raised. The `Timeout` exception is a parent class for more specific timeouts:

- `ConnectTimeout`: Raised when the timeout occurs while trying to establish a connection.
- `ReadTimeout`: Raised if the server does not send any data in the allotted time.

```python
import requests

try:
    # This request will time out while trying to connect.
    response = requests.get('https://github.com', timeout=0.001)
except requests.exceptions.ConnectTimeout as err:
    print(f'Connection timed out: {err}')
except requests.exceptions.ReadTimeout as err:
    print(f'Read timed out: {err}')
except requests.exceptions.Timeout as err:
    print(f'A timeout occurred: {err}')
```

## Exceeding Redirects: `TooManyRedirects`

By default, Requests will follow up to 30 redirects. If this limit is exceeded, it will raise a `TooManyRedirects` exception. This prevents your application from getting stuck in an infinite redirect loop.

```python
import requests

try:
    # httpbin.org/redirect/N follows N redirects. The default limit is 30.
    response = requests.get('https://httpbin.org/redirect/31')
except requests.exceptions.TooManyRedirects as err:
    print(f'Too many redirects: {err}')
```

## Invalid URLs

If you provide a URL that is malformed, Requests will raise an exception, typically before a network request is even made. The most common is `MissingSchema`, which occurs if you forget to include `http://` or `https://`.

```python
import requests

try:
    response = requests.get('google.com')
except requests.exceptions.MissingSchema as err:
    print(f'Invalid URL: {err}')
```

## Exception Hierarchy

Understanding the hierarchy of exceptions can help you catch them more effectively. For instance, catching `ConnectionError` will also catch `ProxyError` and `SSLError`.

Here is a diagram of the main exception types:

```d2
direction: down

"IOError" -> "RequestException"

"RequestException" -> "HTTPError"
"RequestException" -> "ConnectionError"
"RequestException" -> "Timeout"
"RequestException" -> "URLRequired"
"RequestException" -> "TooManyRedirects"
"RequestException" -> "InvalidURL"
"RequestException" -> "MissingSchema"

"ConnectionError" -> "ProxyError"
"ConnectionError" -> "SSLError"

"Timeout" -> "ReadTimeout"

"ConnectTimeout"
"ConnectionError" -> "ConnectTimeout"
"Timeout" -> "ConnectTimeout"

"InvalidURL" -> "InvalidProxyURL"
```


## Next Steps

Now that you can gracefully handle errors, you are ready to explore more complex scenarios. Proceed to the [Advanced Usage](./advanced-usage.md) guide to learn about proxies, SSL verification, custom adapters, and more.