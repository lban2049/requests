# Making a Request

Making an HTTP request with Requests is straightforward. All examples begin with importing the library:

```python
import requests
```

At its core, all HTTP request functionality is built around the `requests.request()` function. The simpler methods like `get()` and `post()` are convenient wrappers around this central function. For a quick overview, here are the most commonly used parameters:

| Parameter | Description |
|---|---|
| `method` | The HTTP method for the request: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `OPTIONS`, `HEAD`. |
| `url` | The URL for the new `Request` object. |
| `params` | A dictionary, list of tuples, or bytes to be sent in the query string of the request. |
| `data` | A dictionary, list of tuples, bytes, or a file-like object to send in the body of the request (typically for form data). |
| `json` | A JSON-serializable Python object to send in the body of the request. Automatically sets the `Content-Type` header to `application/json`. |
| `headers` | A dictionary of HTTP headers to send with the request. |
| `files` | A dictionary for multipart encoding file uploads. |
| `timeout` | The number of seconds to wait for the server to send data before giving up. Can be a single float or a `(connect, read)` tuple. |

## GET Requests & URL Parameters

To make a `GET` request to retrieve data from a URL, use the `requests.get()` method.

```python
# Make a simple GET request
r = requests.get('https://api.github.com/events')
```

Often, you need to pass data in the URL's query string. Instead of manually building the URL, you can provide a dictionary to the `params` argument.

```python
# Pass URL parameters
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}
r = requests.get('https://httpbin.org/get', params=payload)

# You can inspect the URL that was built
print(r.url)
# Output: https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## POST, PUT, PATCH & Request Bodies

Methods like `POST`, `PUT`, and `PATCH` are used to send data to a server. This data is passed in the request body.

### Form-Encoded Data

To send form-encoded data, as a browser does when submitting a form, pass a dictionary to the `data` parameter. The data will be automatically encoded.

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

print(r.json()['form'])
# Output: {'key1': 'value1', 'key2': 'value2'}
```

### JSON Data

For modern APIs, it's common to send JSON-encoded data. You can use the `json` parameter, which accepts a Python dictionary. Requests will handle the serialization and set the appropriate `Content-Type` header for you.

```python
payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

print(r.json()['json'])
# Output: {'some': 'data'}
```

### Other Methods

The `PUT` and `PATCH` methods function similarly to `POST` when it comes to sending body data.

```python
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
```

## DELETE, HEAD, and OPTIONS

Other HTTP methods are also available with a simple, consistent API:

```python
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## Custom Headers

To add or modify HTTP headers, pass a dictionary to the `headers` parameter. This is useful for setting custom `User-Agent` strings, authentication tokens, or other metadata.

```python
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

## Multipart File Uploads

Requests makes it easy to upload files using multipart-encoded data. Provide a dictionary of file-like objects to the `files` parameter.

```python
url = 'https://httpbin.org/post'
files = {'file': open('report.txt', 'rb')}

r = requests.post(url, files=files)
```

For more control, you can provide a tuple for the file value to specify a custom filename, content type, and additional headers.

```python
# The tuple format is ('filename', file_object, 'content_type', custom_headers)
files = {'file': ('report.csv', open('report.csv', 'rb'), 'text/csv', {'Expires': '0'})}

r = requests.post(url, files=files)
```

## Timeouts

To prevent your program from hanging indefinitely on a slow or unresponsive network, you should always specify a timeout. The `timeout` parameter takes a float value representing the number of seconds to wait.

```python
# Wait a maximum of 5 seconds for a response
try:
    r = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('The request timed out.')
```

You can also specify different timeouts for connecting and reading from the server by passing a tuple.

```python
# 3.05 seconds to connect, 10 seconds to read the response
r = requests.get('https://httpbin.org/get', timeout=(3.05, 10))
```

---

Now that you know how to construct and send requests, the next step is to process the data that the server sends back. To learn more, proceed to the next section on [Handling Responses](./user-guide-handling-responses.md).
