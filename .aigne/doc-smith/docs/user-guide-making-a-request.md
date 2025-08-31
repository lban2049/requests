# Making a Request

Making an HTTP request with the Requests library is designed to be simple and intuitive. To begin, ensure you have the library imported:

```python
import requests
```

The entire API is centered around seven primary functions, one for each HTTP verb. These functions are straightforward wrappers for the underlying `requests.request()` method, providing a clean and readable way to interact with web services.

The fundamental interaction follows a clear request-response cycle.

```d2
direction: right
shape: sequence_diagram

Client: {
  shape: person
  label: "Your Application"
}

Server: {
  shape: cloud
  label: "Web Server"
}

Client -> Server: "HTTP Request (GET, POST, etc.)\n- URL: /get\n- Headers: {'user-agent': 'my-app'}\n- Body: (optional)" {
    style.animated: true
}

Server -> Client: "HTTP Response\n- Status Code: 200 OK\n- Headers: {'content-type': 'application/json'}\n- Body: {'key': 'value'}" {
    style.animated: true
}
```

Here is a summary of the most common parameters you will use when constructing a request:

| Parameter | Description |
|---|---|
| `url` | The URL for the new `Request` object. |
| `params` | A dictionary, list of tuples, or bytes to be sent in the query string of the request. |
| `data` | A dictionary, list of tuples, bytes, or a file-like object to send in the body of the request (typically for form data). |
| `json` | A JSON-serializable Python object to send in the body of the request. This automatically sets the `Content-Type` header to `application/json`. |
| `headers` | A dictionary of HTTP headers to send with the request. |
| `files` | A dictionary for multipart encoding file uploads. |
| `timeout` | The number of seconds to wait for the server to send data before giving up. It can be a single float or a `(connect, read)` tuple. |

## GET Requests & URL Parameters

To make a `GET` request to retrieve data from a URL, use the `requests.get()` method. This is one of the most common types of requests.

```python
# Make a simple GET request
r = requests.get('https://api.github.com/events')
```

Frequently, you'll need to pass data in the URL's query string. Instead of manually constructing the URL, you can provide a dictionary to the `params` argument, and Requests will build the URL for you.

```python
# Define a dictionary of parameters
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

# Make the request with the parameters
r = requests.get('https://httpbin.org/get', params=payload)

# You can inspect the URL that was constructed
print(r.url)
# Expected Output: https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## POST, PUT, PATCH & Request Bodies

Methods like `POST`, `PUT`, and `PATCH` are used to send data to a server. This data is transmitted in the request body.

### Form-Encoded Data

To send form-encoded data, which is what a browser does when a user submits a form, pass a dictionary to the `data` parameter. Your dictionary of data will be automatically form-encoded when the request is made.

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# You can view the form data the server received
print(r.json()['form'])
# Expected Output: {'key1': 'value1', 'key2': 'value2'}
```

### JSON Data

For many modern APIs, sending JSON-encoded data is required. You can use the `json` parameter, which accepts a Python dictionary or other JSON-serializable object. Requests handles the serialization and automatically sets the correct `Content-Type` header (`application/json`).

```python
payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# View the JSON data received by the server
print(r.json()['json'])
# Expected Output: {'some': 'data'}
```

### Other Methods

The `PUT` and `PATCH` methods function identically to `POST` when it comes to sending body data.

```python
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
```

## DELETE, HEAD, and OPTIONS

Other HTTP methods are available through a similarly simple and consistent API, though they typically do not involve sending a request body.

```python
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## Custom Headers

To add or modify HTTP headers for a request, pass a dictionary to the `headers` parameter. This is useful for setting custom `User-Agent` strings, authentication tokens, or other request metadata.

```python
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

## Multipart File Uploads

Requests simplifies the process of uploading files using multipart-encoded data. Provide a dictionary of file-like objects (opened in binary mode) to the `files` parameter.

```python
url = 'https://httpbin.org/post'

# Create a dummy file for the example
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

# Open the file in binary read mode and pass it to the files parameter
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# The server will receive the file in the 'files' field
print(r.json()['files'])
# Expected Output: {'file': 'This is a test report.'}
```

For more control over the uploaded file, you can provide a tuple for the dictionary value. This allows you to specify a custom filename, content type, and additional headers for that file.

```python
# The tuple format is ('filename', file_object, 'content_type', custom_headers)
with open('report.csv', 'w') as f:
    f.write('col1,col2\nval1,val2')

with open('report.csv', 'rb') as f:
    files = {'file': ('custom_report_name.csv', f, 'text/csv', {'Expires': '0'})}
    r = requests.post(url, files=files)

# Inspect the headers and filename as received by the server
print(r.json()['files'])
```

## Timeouts

To prevent your program from waiting indefinitely for a response from a slow or unresponsive server, you should always specify a timeout. The `timeout` parameter accepts a float value representing the number of seconds to wait for the server to send a response.

```python
# Wait a maximum of 5 seconds for a response
try:
    r = requests.get('https://httpbin.org/delay/10', timeout=5)
except requests.exceptions.Timeout:
    print('The request timed out.')
```

You can also specify different timeouts for connecting to the server and for reading the response by passing a tuple.

```python
# Wait 3.05 seconds to establish a connection, and then 10 seconds to receive the response
r = requests.get('https://httpbin.org/get', timeout=(3.05, 10))
```

---

Now that you are familiar with how to construct and send requests, the next step is to process the data that the server sends back. To learn more, proceed to the next section on [Handling Responses](./user-guide-handling-responses.md).
