# Handling Responses

Once you've made a request, the Requests library returns a `Response` object. This object contains the server's response, including the content, status code, headers, and more. Let's explore how to work with this object effectively.

First, let's make a request to use in our examples:

```python Making a Request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

## Response Content

You can access the body of the response in several ways, depending on the content type.

### Binary Response Content

For non-text requests, you can access the response body in bytes using the `content` attribute. Requests automatically decodes `gzip` and `deflate` transfer-encodings for you.

This is useful for data like images or other files.

```python Getting Binary Content icon=logos:python
# r.content returns bytes
print(r.content[:100]) # Print the first 100 bytes

# Example: Saving an image
# from PIL import Image
# from io import BytesIO
# image_response = requests.get('https://via.placeholder.com/150')
# try:
#     i = Image.open(BytesIO(image_response.content))
#     i.save('placeholder.png')
#     print("Image saved as placeholder.png")
# except Exception as e:
#     print(f"Could not process image: {e}")
```

### Text Response Content

For text-based responses, the `text` attribute provides the content as a string. Requests automatically decodes the content from the server's response.

Requests makes an educated guess about the encoding based on the HTTP headers. If it can't find a `charset` in the `Content-Type` header, it will attempt to guess the encoding using the `chardet` library. You can find out what encoding Requests is using:

```python Checking the Encoding icon=logos:python
print(f"Detected encoding: {r.encoding}")
# Output: Detected encoding: utf-8
```

If you need to override the detected encoding, you can set the `encoding` attribute manually before accessing `.text`:

```python Setting the Encoding icon=logos:python
r.encoding = 'ISO-8859-1'
print(r.text)
```

### JSON Response Content

If the response contains JSON data, you can use the built-in `json()` method to parse it into a Python dictionary or list. This is incredibly convenient for working with APIs.

```python Parsing JSON icon=logos:python
json_response = r.json()
print(type(json_response)) # <class 'list'>
print(json_response[0]['type']) # Access data like a normal Python object
```

If the response does not contain valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`.

### Streaming Content

For very large responses, you can avoid loading the entire content into memory at once by using `iter_content`. This is done by setting `stream=True` in your initial request.

```python Streaming Large Files icon=logos:python
with requests.get('https://httpbin.org/stream/20', stream=True) as r:
    for chunk in r.iter_content(chunk_size=128):
        if chunk:
            print(chunk)
```

You can also iterate over the response line-by-line using `iter_lines`.

## Inspecting the Response

Beyond the content, the `Response` object provides useful attributes for inspection.

### Status Codes

You can check the HTTP status code of the response with the `status_code` attribute.

```python Checking the Status Code icon=logos:python
print(r.status_code)
# Output: 200
```

For better readability, Requests provides a lookup object for common status codes:

```python Using the Codes Object icon=logos:python
if r.status_code == requests.codes.ok:
    print("Request was successful!")
else:
    print(f"Request failed with status code: {r.status_code}")
```

### Response Headers

The response headers are available as a dictionary-like object that is case-insensitive.

```python Accessing Headers icon=logos:python
print(r.headers)
# Access a specific header
print(f"Content-Type: {r.headers['Content-Type']}")
# Case-insensitivity in action
print(f"content-type: {r.headers.get('content-type')}")
```

### Cookies

If the response contains any cookies, you can access them through the `cookies` attribute, which returns a `CookieJar` object.

```python Working with Cookies icon=logos:python
cookie_r = requests.get('https://httpbin.org/cookies/set?my_cookie=12345')
print(cookie_r.cookies['my_cookie'])
# Output: 12345
```

## Error Handling

Requests makes it easy to check if a request was successful or to raise an exception on failure.

### The `ok` Property

A simple way to check for success is the `ok` boolean property. It returns `True` if the `status_code` is less than 400 (i.e., not a client or server error).

```python Using the ok Property icon=logos:python
if r.ok:
    print("Request is OK")
else:
    print("Request failed")
```

### Raising an Exception for Errors

For a more explicit failure signal, you can use the `raise_for_status()` method. If the request resulted in a client error (4xx status code) or a server error (5xx status code), it will raise an `HTTPError`.

```python Raising Exceptions icon=logos:python
error_r = requests.get('https://httpbin.org/status/404')
print(f"Status Code: {error_r.status_code}")

try:
    error_r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"HTTP Error Occurred: {err}")
```

If the request was successful, `raise_for_status()` does nothing.

## Redirection and History

By default, Requests will automatically perform location redirection. The `history` attribute of the `Response` object contains a list of the `Response` objects that were created in order to complete the request. The list is sorted from the oldest to the most recent response.

For example, GitHub redirects all HTTP requests to HTTPS:

```python Inspecting Redirect History icon=logos:python
redir_r = requests.get('http://github.com')

print(f"Final URL: {redir_r.url}")
print(f"Final Status Code: {redir_r.status_code}")

# Check the history
print(f"History: {redir_r.history}")

# The first response in history is the original 301 redirect
if redir_r.history:
    original_response = redir_r.history[0]
    print(f"Original Status Code: {original_response.status_code}")
    print(f"Original URL: {original_response.url}")
```

Now that you're comfortable handling responses, the next step is to manage state across multiple requests. Learn how to persist cookies and headers with [Session Objects](./user-guide-session-objects.md).