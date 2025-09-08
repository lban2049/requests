# Handling Responses

After making a request, Requests returns a `Response` object which contains the server's response. This object holds all the information you need, from the status code and headers to the response body itself. This guide will walk you through the most common ways to inspect and handle this response data.

For more details on sending requests, see the previous section on [Making a Request](./user-guide-making-a-request.md).

## Checking the Status Code

The first step after receiving a response is often to check if the request was successful. The `status_code` attribute provides the HTTP status code as an integer.

```python Checking the status code icon=logos:python
r = requests.get('https://httpbin.org/status/200')
print(r.status_code)
# 200

if r.status_code == 200:
    print('Success!')
elif r.status_code == 404:
    print('Not Found.')
```

For convenience, Requests provides a lookup object for common status codes, making your code more readable.

```python Using the codes object icon=logos:python
import requests

r = requests.get('https://httpbin.org/status/200')
if r.status_code == requests.codes.ok: # .ok is an alias for 200
    print('Request was successful.')
```

### Raising an Exception for Bad Responses

Instead of checking the status code manually, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the HTTP request returned an unsuccessful status code (a 4xx client error or 5xx server error).

```python Using raise_for_status() icon=logos:python
import requests
from requests.exceptions import HTTPError

bad_r = requests.get('https://httpbin.org/status/404')

try:
    bad_r.raise_for_status()
except HTTPError as http_err:
    print(f'HTTP error occurred: {http_err}')
except Exception as err:
    print(f'Other error occurred: {err}')
else:
    print('Success!')

# Console Output:
# HTTP error occurred: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

If the request is successful (a status code in the 2xx range), `raise_for_status()` will do nothing. The `ok` property also provides a simple boolean check for success.

```python Using the .ok property icon=logos:python
r = requests.get('https://httpbin.org/status/200')
if r.ok:
    print("Request was successful!")
```

## Accessing Response Headers

The response headers are available as a dictionary-like object through the `headers` attribute. A key feature is that the header keys are case-insensitive.

```python Accessing headers icon=logos:python
r = requests.get('https://httpbin.org/get')

# Accessing headers
print(r.headers['Content-Type'])
# 'application/json'

# Case-insensitivity
print(r.headers.get('content-type'))
# 'application/json'
```

## Accessing the Response Body

Depending on the `Content-Type` of the response, you can access the body in several ways.

### Raw Binary Content

For non-textual responses, like images or PDF files, you can access the raw bytes of the response body using the `content` attribute.

Here is an example of saving an image from a URL:

```python Saving binary content icon=logos:python
import requests

# This URL points to the Requests library logo
r = requests.get('https://raw.githubusercontent.com/psf/requests/main/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(r.content)

# This will save 'requests_logo.png' in your current directory.
```
![Requests Library Logo](../../../ext/requests-logo.png)

### Text Content

For textual data, the `text` attribute provides the response body as a string. Requests automatically decodes the content based on the character encoding specified in the response headers. If no encoding is specified, it will make a best guess.

```python Accessing text content icon=logos:python
r = requests.get('https://httpbin.org/html')
print(r.text)
```

**Example Response**
```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
      <h1>Herman Melville - Moby-Dick</h1>
  </body>
</html>
```

If you find that the encoding detection is incorrect, you can manually set the `encoding` attribute before accessing `.text`:

```python Manually setting encoding icon=logos:python
r.encoding = 'utf-8'
print(r.text)
```

### JSON Content

If the response contains JSON data, you can use the built-in `json()` method to parse it into a Python dictionary or list.

```python Parsing JSON content icon=logos:python
r = requests.get('https://httpbin.org/json')

data = r.json()

# Access data from the parsed JSON
slideshow_title = data['slideshow']['title']
print(f'Slideshow Title: {slideshow_title}')

# Console Output:
# Slideshow Title: Sample Slide Show
```

If the response body does not contain valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`.

## Streaming Content

For very large responses, you can avoid loading the entire content into memory at once by using `stream=True` in your request. You can then iterate over the content using `iter_content()` or `iter_lines()`.

### Streaming Chunks

`iter_content()` allows you to iterate over the response data in chunks of a specified size. This is highly effective for downloading large files without consuming excessive memory.

```python Downloading a large file in chunks icon=logos:python
# Using iter_content to download a large file
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status() # Ensure the request was successful
    with open('large_file.bin', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            # chunk will be a bytes object of size at most 8192
            f.write(chunk)
```

### Streaming Lines

`iter_lines()` is useful for processing text-based streaming APIs, as it iterates over the response content one line at a time.

```python Processing a text stream line by line icon=logos:python
with requests.get('https://httpbin.org/stream/5', stream=True) as r:
    for line in r.iter_lines():
        if line:
            # The line will be a bytes object, decode it for printing
            decoded_line = line.decode('utf-8')
            print(decoded_line)
```
This approach is efficient for handling large downloads or processing data streams without high memory usage.

---

Now that you know how to handle responses, you can improve the efficiency and state management of your requests by using [Session Objects](./user-guide-session-objects.md).
