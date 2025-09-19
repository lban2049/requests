# POSTing Data

Often, you'll want to send data in the body of a request, particularly for `POST`, `PUT`, and `PATCH` methods. The Requests library makes this incredibly straightforward by handling the encoding for you.

## Sending Form-Encoded Data

The most common way to send data is as a simple key-value pair, similar to an HTML form. To do this, you can pass a dictionary to the `data` argument of the `post` method. Your dictionary will be automatically form-encoded before being sent.

```python Sending Form-Encoded Data icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post("https://httpbin.org/post", data=payload)

print(r.json())
```

The `form` field in the response from `httpbin.org` confirms that the data was correctly received and decoded on the server side.

```json Response Body icon=mdi:code-json
{
  ...
  "form": {
    "key1": "value1", 
    "key2": "value2"
  }, 
  ...
}
```

You can also pass a list of tuples to the `data` parameter if you need to send multiple values for the same key.

## Sending JSON Data

Modern APIs often prefer JSON-encoded data. Instead of encoding the dictionary to a string yourself, you can pass it directly to the `json` parameter. Requests will automatically handle the serialization and set the `Content-Type` header to `application/json`.

```python Sending a JSON Payload icon=logos:python
import requests

url = 'https://httpbin.org/post'
payload = {'some': 'data'}

r = requests.post(url, json=payload)

print(r.json())
```

As you can see in the response, the `json` field contains your original payload, and the `Content-Type` header is correctly set.

```json Response Body icon=mdi:code-json
{
  ...
  "json": {
    "some": "data"
  }, 
  "headers": {
    "Content-Type": "application/json", 
    ...
  },
  ...
}
```

## Multipart-Encoded File Uploads

Requests supports multipart-encoded file uploads, making it easy to send files to a server. Simply provide a file-like object to the `files` parameter.

```python Uploading a File icon=logos:python
import requests

url = 'https://httpbin.org/post'
# Assume 'report.txt' is a file in the same directory
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

print(r.json()['files'])
```

For more control, you can explicitly set the filename, content type, and custom headers by passing a tuple instead of just the file object. The tuple can be structured in several ways:

-   `('filename', file_object)`
-   `('filename', file_object, 'content_type')`
-   `('filename', file_object, 'content_type', custom_headers)`

```python Customizing File Upload icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {
    'file': ('report.csv', 'col1,col2\ndata1,data2\n', 'text/csv', {'Expires': '0'})
}

r = requests.post(url, files=files)

print(r.text)
```

You can also send other form data alongside the file upload by providing the `data` argument as usual.

```python Uploading a File with Form Data icon=logos:python
import requests

url = 'https://httpbin.org/post'
with open('report.txt', 'rb') as f:
    files = {'file': f}
    form_data = {'author': 'john_doe', 'year': '2024'}
    
    r = requests.post(url, files=files, data=form_data)

response_data = r.json()
print("Files received:", response_data['files'])
print("Form data received:", response_data['form'])
```

## Raw Request Body

For methods like `PUT` or `PATCH`, or if you need to send a non-form-encoded payload, you can pass a `string` or `bytes` directly to the `data` argument. This data will be sent as-is.

```python Sending Raw Data with PUT icon=logos:python
import requests
import json

url = 'https://httpbin.org/put'
payload = {'message': 'hello world'}

r = requests.put(url, data=json.dumps(payload))

print(r.json()['data'])
# Output: '{"message": "hello world"}'
```

This covers the primary ways to send data in the body of your requests, from simple forms to complex file uploads.