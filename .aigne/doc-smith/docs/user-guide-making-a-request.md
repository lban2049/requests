# Making a Request

The Requests library simplifies sending HTTP requests. To get started, you'll use one of the functions corresponding to the desired HTTP method. All of these functions are wrappers around the main `requests.request()` function.

## HTTP Methods

Requests provides a function for each of the most common HTTP methods:

*   `requests.get()`: Retrieves data from a specified URL.
*   `requests.post()`: Submits data to be processed to a specified resource.
*   `requests.put()`: Updates a resource or creates a new one if it does not exist.
*   `requests.patch()`: Applies partial modifications to a resource.
*   `requests.delete()`: Deletes the specified resource.
*   `requests.head()`: Requests the headers of a resource without the body.
*   `requests.options()`: Describes the communication options for the target resource.

Here are simple examples for each method:

```python
import requests

r = requests.get('https://httpbin.org/get')
print(r)

r = requests.post('https://httpbin.org/post', data={'key': 'value'})
print(r)

r = requests.put('https://httpbin.org/put', data={'key': 'value'})
print(r)

r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
print(r)

r = requests.delete('https://httpbin.org/delete')
print(r)

r = requests.head('https://httpbin.org/get')
print(r)

r = requests.options('https://httpbin.org/get')
print(r)
```

Response:
```
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
<Response [200]>
```

## Passing Parameters in URLs

Often, you need to send data in the URL's query string. Instead of manually building the URL, you can provide these parameters as a dictionary or a list of tuples using the `params` keyword argument. Requests will correctly URL-encode them for you.

For example, to pass `key1=value1` and `key2=value2` to `httpbin.org/get`:

```python
import requests

# Using a dictionary
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

# The URL constructed by Requests
print(r.url)
```

Response:
```
https://httpbin.org/get?key1=value1&key2=value2
```

You can also pass a list of tuples if you need to provide multiple values for a single key:

```python
import requests

# Using a list of tuples
payload_list = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload_list)

print(r.url)
```

Response:
```
https://httpbin.org/get?key1=value1&key1=value2
```

## Request Body

For methods like `POST`, `PUT`, and `PATCH`, you typically send data in the request body.

### Form-Encoded Data

To send form-encoded data, similar to what an HTML form would submit, pass a dictionary to the `data` parameter. Your dictionary of data will be automatically form-encoded when the request is made.

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# Print the form data echoed by httpbin
print(r.json()['form'])
```

Response:
```json
{
  "key1": "value1",
  "key2": "value2"
}
```

### JSON Encoded Data

Instead of form-encoding the data, you can send it as a JSON-serialized string. Use the `json` parameter, which accepts a Python object (like a dictionary or list). Requests will automatically encode it to JSON and set the `Content-Type` header to `application/json`.

```python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# Print the JSON data echoed by httpbin
print(r.json()['json'])
```

Response:
```json
{
  "some": "data"
}
```

### Multipart-Encoded File Upload

To upload a multipart-encoded file, use the `files` parameter. You can pass a dictionary where the key is the field name and the value is a file-like object.

```python
import requests

url = 'https://httpbin.org/post'
# You need a file named 'report.txt' in the same directory
# with some content for this to run.
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# httpbin will echo back the uploaded file's contents
print(r.json()['files'])
```

Response:
```json
{
  "file": "This is a test report."
}
```

You can also explicitly set the filename, content type, and custom headers by providing a tuple for the file value:

```python
files = {'file': ('report.csv', 'some,data,to,send\n', 'text/csv', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## Custom Headers

To add or modify HTTP headers for a request, pass a dictionary of headers to the `headers` parameter.

```python
import requests

url = 'https://httpbin.org/get'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)

# httpbin will echo the headers back
print(r.json()['headers']['User-Agent'])
```

Response:
```
my-app/0.0.1
```

---

Now that you know how to construct and send requests, the next step is to understand what you get back from the server. For more details, proceed to the [Handling Responses](./user-guide-handling-responses.md) guide.