# Making a Request

Sending an HTTP request with Requests is straightforward. This guide covers how to use various HTTP methods, pass URL parameters, customize headers, and send different types of request bodies.

## Basic GET Request

To make a simple `GET` request, use the `requests.get()` function. This is often the first step in interacting with a web service or API.

```python
import requests

r = requests.get('https://api.github.com/events')
# The response object 'r' now contains the server's response.
```

## Passing Parameters in URLs

To add URL query parameters, you can provide them as a dictionary to the `params` argument. Requests will correctly construct the URL for you.

```python
import requests

# Define the parameters
payload = {'key1': 'value1', 'key2': 'value2'}

# Make the request
r = requests.get('https://httpbin.org/get', params=payload)

# Print the URL that was constructed
print(r.url)
```

Running the code above will output the following URL, with the parameters properly encoded:

```
https://httpbin.org/get?key1=value1&key2=value2
```

If you need to provide multiple values for a single key, you can use a list of tuples:

```python

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)
print(r.url)
# Output: https://httpbin.org/get?key1=value1&key1=value2
```

## Other HTTP Methods

Requests provides simple functions for all common HTTP methods. They all work similarly to `requests.get()`.

<x-cards data-columns="3">
  <x-card data-title="POST" data-icon="lucide:send">
    Sends data to a server to create a resource.
  </x-card>
  <x-card data-title="PUT" data-icon="lucide:upload-cloud">
    Sends data to update an existing resource completely.
  </x-card>
  <x-card data-title="PATCH" data-icon="lucide:pencil">
    Applies partial modifications to a resource.
  </x-card>
  <x-card data-title="DELETE" data-icon="lucide:trash-2">
    Deletes a specified resource.
  </x-card>
  <x-card data-title="HEAD" data-icon="lucide:file-question">
    Requests the headers for a resource without the body.
  </x-card>
  <x-card data-title="OPTIONS" data-icon="lucide:settings-2">
    Describes the communication options for the target resource.
  </x-card>
</x-cards>

Here's how you might use them:

```python
r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.patch('https://httpbin.org/patch', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## Sending Data in the Request Body

For methods like `POST`, `PUT`, and `PATCH`, you often need to send data in the request body.

### Form-Encoded Data

To send data as `application/x-www-form-urlencoded` (the default for HTML forms), pass a dictionary to the `data` parameter.

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

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

For modern APIs, sending data as JSON is common. Instead of manually encoding a dictionary with `json.dumps()`, you can use the `json` parameter. Requests will automatically encode the data and set the `Content-Type` header to `application/json`.

```python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

print(r.json()['json'])
```
Response:
```json
{
  "some": "data"
}
```

### Multipart-Encoded File Uploads

To upload a file, you can pass a file-like object to the `files` parameter. The file should be opened in binary mode.

```python
import requests

url = 'https://httpbin.org/post'

# Create a dummy file for the example
with open('report.txt', 'w') as f:
    f.write('This is a test report.')

# Open the file in binary mode and send the request
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

# httpbin.org will return the contents of the uploaded file.
print(r.json()['files'])
```

Response:
```json
{
  "file": "This is a test report."
}
```

You can also explicitly set the filename, content type, and headers by passing a tuple to the dictionary value:

```python
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## Custom Headers

To add or modify HTTP headers, pass a dictionary to the `headers` parameter. For example, you might need to set a custom `User-Agent`.

```python
import requests

url = 'https://httpbin.org/headers'
headers = {'user-agent': 'my-custom-app/0.0.1'}

r = requests.get(url, headers=headers)

print(r.json()['headers']['User-Agent'])
```

Response:
```
my-custom-app/0.0.1
```

Now that you know how to construct and send a request, the next step is to understand the server's response. Proceed to the next section to learn more.

➡️ Next: [Handling Responses](./user-guide-handling-responses.md)
