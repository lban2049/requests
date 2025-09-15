# Making a Request

Making HTTP requests is a fundamental part of most modern applications, and the Requests library makes this process incredibly straightforward. This guide covers the common methods for sending data and interacting with web services.

Let's start with a simple GET request to fetch some data.

```python Making a simple GET request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.status_code)
# 200
```

It's that easy! Now, let's explore how to customize these requests to suit your needs.

## Passing URL Parameters

Often, you need to send data in the URL's query string. Instead of manually building the URL, you can provide the `params` argument with a dictionary of key-value pairs. Requests will correctly format the URL for you.

```python Passing URL parameters icon=logos:python
import requests

# Define the parameters to be sent
payload = {'key1': 'value1', 'key2': 'value2'}

# Make the GET request
r = requests.get('https://httpbin.org/get', params=payload)

# Print the final URL that was requested
print(r.url)
# https://httpbin.org/get?key1=value1&key2=value2
```

You can also pass a list of items as a value:

```python Passing a list of items icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
# https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## Sending Data in the Request Body

For HTTP methods like `POST`, `PUT`, and `PATCH`, you typically send data in the body of the request.

### Form-Encoded Data

The most common way to send data is as form-encoded data, which is what your browser does when you submit a simple form. You can pass a dictionary to the `data` argument.

```python Sending form-encoded data icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}

r = requests.post('https://httpbin.org/post', data=payload)

# httpbin.org will echo the form data back in the response
print(r.json()['form'])
# {'key1': 'value1', 'key2': 'value2'}
```

### JSON-Encoded Data

Many modern APIs prefer JSON-encoded data. Instead of encoding the dictionary to JSON yourself, you can simply use the `json` parameter. Requests will handle the encoding and set the `Content-Type` header to `application/json` automatically.

```python Sending JSON data icon=logos:python
import requests

payload = {'some': 'data'}

r = requests.post('https://httpbin.org/post', json=payload)

# httpbin.org will echo the JSON data and headers
response_json = r.json()
print(response_json['json'])
# {'some': 'data'}
print(response_json['headers']['Content-Type'])
# application/json
```

### Multipart File Uploads

Requests makes it simple to upload multipart-encoded files. Just provide a file-like object to the `files` argument.

```python Uploading a file icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}

r = requests.post(url, files=files)

print(r.json()['files'])
# {'file': '... contents of report.xls ...'}
```

You can also explicitly set the filename, content type, and custom headers if needed by passing a tuple.

```python Customizing a file upload icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}

r = requests.post(url, files=files)

print(r.json()['files'])
# {'file': 'some,data,to,send\n'}
```

## Other HTTP Methods

Requests provides a convenience method for every major HTTP verb. The usage is consistent across all methods.

```python Using other HTTP methods icon=logos:python
import requests

r = requests.put('https://httpbin.org/put', data={'key': 'value'})
print(f"PUT status: {r.status_code}")

r = requests.delete('https://httpbin.org/delete')
print(f"DELETE status: {r.status_code}")

r = requests.head('https://httpbin.org/get')
print(f"HEAD status: {r.status_code}")

r = requests.options('https://httpbin.org/get')
print(f"OPTIONS status: {r.status_code}")
```

## Custom Headers

To add or modify HTTP headers, pass a dictionary to the `headers` parameter. A common use case is to set a custom `User-Agent` string.

```python Sending custom headers icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

---

Now that you've learned how to create and customize requests, the next step is to process the server's response. Continue to the next section to learn about [Handling Responses](./user-guide-handling-responses.md).
