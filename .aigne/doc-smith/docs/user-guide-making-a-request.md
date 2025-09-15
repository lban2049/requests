# Making a Request

Making HTTP requests is the core feature of the Requests library. It's designed to be simple and intuitive, allowing you to focus on interacting with services rather than managing connections and request formats. This guide covers the most common ways to make requests, including different HTTP methods and how to send data.

## Making a GET Request

One of the most common HTTP methods is GET, which is used to retrieve data from a specified resource. To make a GET request, simply use the `requests.get()` function.

```python Making a GET Request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.status_code)
```

This code sends a GET request to the GitHub Events API. After the request is made, we get a `Response` object, which we've named `r`. We can use this object to see details about the response, such as the status code. A status code of `200` means the request was successful.


## Passing Parameters in URLs

Often, you need to send some data in the URL's query string (e.g., `?key=value`). Instead of manually building the URL, you can provide these parameters as a dictionary using the `params` keyword argument. Requests will correctly format and encode them for you.

```python Passing URL Parameters icon=logos:python
import requests

# Define the parameters as a dictionary
payload = {'key1': 'value1', 'key2': 'value2'}

# Make the request with the params
r = requests.get('https://httpbin.org/get', params=payload)

# You can see the URL that was constructed
print(r.url)
```

**Output:**
```text
https://httpbin.org/get?key1=value1&key2=value2
```

You can also pass a list of tuples as parameters if you need to provide multiple values for the same key:

```python Passing a List of Tuples icon=logos:python
import requests

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

**Output:**
```text
https://httpbin.org/get?key1=value1&key1=value2
```

## Other HTTP Methods

Requests provides simple functions for all common HTTP methods. They are all as easy to use as `get()`.

```python Common HTTP Methods icon=logos:python
import requests

r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')

print(r.status_code)
```

Each function returns a `Response` object with the server's reply.

## Sending Data in the Request Body

For methods like POST, PUT, and PATCH, you often need to send data in the body of the request. Requests makes this straightforward.

### Form-Encoded Data

To send data as `application/x-www-form-urlencoded`, which is a common format for HTML forms, you can pass a dictionary to the `data` parameter. Your dictionary of data will be automatically form-encoded when the request is made.

```python POSTing Form Data icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# The response body from httpbin.org will show the form data
print(r.json()['form'])
```

### JSON-Encoded Data

Instead of manually encoding a dictionary to JSON, you can use the `json` parameter. Requests will automatically serialize your Python object to a JSON string and add the correct `Content-Type: application/json` header.

```python POSTing a JSON Body icon=logos:python
import requests

payload = {'some': 'data'}
r = requests.post('https://httpbin.org/post', json=payload)

# The response from httpbin.org will reflect the JSON payload
print(r.json()['json'])
```

### Multipart-Encoded File Uploads

To upload a file, you can pass a file-like object to the `files` parameter. Requests will automatically handle the multipart encoding.

```python Uploading a File icon=logos:python
import requests

# You need to open the file in binary mode
files = {'file': open('report.xls', 'rb')}

r = requests.post('https://httpbin.org/post', files=files)

# The response will contain information about the uploaded file
print(r.json()['files'])
```

You can also set the filename, content type, and custom headers explicitly by passing a tuple to the `files` dictionary.

```python Customizing File Uploads icon=logos:python
import requests

files = {
    'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})
}

r = requests.post('https://httpbin.org/post', files=files)
print(r.json()['files'])
```

## Custom Headers

If you need to add or modify HTTP headers, you can pass a dictionary to the `headers` parameter. For example, you might need to set a custom `User-Agent`.

```python Setting Custom Headers icon=logos:python
import requests

headers = {'user-agent': 'my-cool-app/1.0.0'}
r = requests.get('https://httpbin.org/headers', headers=headers)

print(r.json()['headers']['User-Agent'])
```

---

Now that you know how to create and customize requests, the next step is to understand what you can do with the server's response. Learn more in the next section, [Handling Responses](./user-guide-handling-responses.md).
