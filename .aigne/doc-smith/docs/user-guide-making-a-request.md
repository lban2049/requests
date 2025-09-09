# Making a Request

Making an HTTP request with the Requests library is simple and intuitive. This guide will walk you through the most common HTTP methods and show you how to customize your requests with parameters, headers, and different types of request bodies.

## Making a GET Request

To make a `GET` request, use the `requests.get()` function. This is one of the most common methods for retrieving data from a URL.

```python Making a simple GET request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
# The response object 'r' now contains the server's response.
```

### Passing Parameters in URLs

Often, you need to pass data in the URL's query string (e.g., `?key=value`). Instead of manually building the URL, you can provide the `params` argument with a dictionary or a list of tuples. Requests will correctly encode the parameters for you.

```python Passing URL parameters icon=logos:python
import requests

# Using a dictionary for parameters
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

# You can verify the URL that was constructed
print(r.url)
# Output: https://httpbin.org/get?key1=value1&key2=value2
```

If you need to pass multiple values for the same key, you can use a list of tuples:

```python icon=logos:python
# Using a list of tuples for multiple values
payload_tuples = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload_tuples)
print(r.url)
# Output: https://httpbin.org/get?key1=value1&key1=value2
```

## Other HTTP Methods

Requests provides simple functions for all other standard HTTP methods: `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, and `OPTIONS`. They are all as straightforward as `GET`.

```python Using various HTTP methods icon=logos:python
r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## Passing Data in the Request Body

For methods like `POST`, `PUT`, and `PATCH`, you often need to send data in the request body. Requests makes this easy with the `data` and `json` parameters.

### Sending Form-Encoded Data

To send data as if it were from an HTML form, you can pass a dictionary to the `data` parameter. Your dictionary of data will be automatically form-encoded when the request is made.

```python POSTing form data icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# The form data is available in the response's 'form' field
print(r.json()['form'])
# Output: {'key1': 'value1', 'key2': 'value2'}
```

### Sending JSON Data

For modern APIs, it's common to send data in JSON format. Instead of encoding the data yourself, you can use the `json` parameter. Requests will automatically serialize your Python object to a JSON string and set the `Content-Type` header to `application/json`.

```python POSTing JSON data icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
payload = {'some': 'data'}

r = requests.post(url, json=payload)
# The Content-Type header is automatically set to 'application/json'
```

### Uploading Files (Multipart-Encoded)

Requests also supports multipart-encoded file uploads. You can pass a dictionary of file-like objects to the `files` parameter.

```python Uploading a file icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}

r = requests.post(url, files=files)
print(r.text)
```

You can also explicitly set the filename, content type, and custom headers by passing a tuple to the `files` dictionary value.

```python Customizing file uploads icon=logos:python
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## Custom Headers

If you need to add custom HTTP headers to a request, you can pass a dictionary to the `headers` parameter.

```python Adding custom headers icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

---

Now that you know how to create and customize requests, the next step is to understand the response you get back from the server. For that, let's proceed to the next section.

<x-card data-title="Handling Responses" data-icon="lucide:arrow-right-circle" data-href="/user-guide/handling-responses" data-cta="Next Step">
  Learn how to access response content, status codes, and headers.
</x-card>