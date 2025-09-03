# Handling Responses

After you've made a request using a method like `requests.get()`, you receive a `Response` object. This object contains all the information returned by the server, and learning how to handle it is the next logical step after [Making a Request](./user-guide-making-a-request.md).

Let's start by making a simple request to the GitHub API:

```python
import requests

r = requests.get('https://api.github.com/events')
```

Now, we have a `Response` object called `r`. We can start inspecting its attributes and methods to get the data we need.

## Accessing Response Content

Requests provides several ways to access the body of the response, depending on the content type.

### Binary Response Content

You can access the raw bytes of the response using the `r.content` attribute. This is useful for non-textual content like images, audio, or other files.

```python
# This will print the raw bytes of the response
print(r.content)
# b'[{"id":"35003554473","type":"PushEvent","actor":{"id":...}]'
```

Requests automatically decompresses `gzip` and `deflate` transfer-encodings for you. You can see this in action when downloading a compressed file.

### Text Response Content

For text-based responses, the `r.text` attribute provides the content as a string. Requests automatically decodes `r.content` to create `r.text`.

How does it determine the encoding?
1.  It checks for an encoding in the HTTP headers (in the `Content-Type` header).
2.  If no encoding is specified in the headers, it falls back to guessing the encoding using libraries like `chardet`.

```python
print(r.text)
# '[{"id":"35003554473","type":"PushEvent","actor":{"id":...}]'
```

You can view which encoding Requests is using and even change it before accessing `r.text`:

```python
print(r.encoding)  # utf-8

r.encoding = 'ISO-8859-1'
# The text will now be decoded using the new encoding
print(r.text)
```

### JSON Response Content

If you're dealing with a JSON API, you can use the built-in `r.json()` method. This method parses the response text as JSON and returns a Python dictionary or list.

```python
r = requests.get('https://api.github.com/events')
json_response = r.json()

# Now you can work with it like a regular Python object
print(json_response[0]['type']) # e.g., 'PushEvent'
```

If the response does not contain valid JSON, calling `r.json()` will raise a `requests.exceptions.JSONDecodeError`.

```python
import requests

r_html = requests.get('https://github.com')
try:
    r_html.json()
except requests.exceptions.JSONDecodeError:
    print("Response could not be decoded as JSON.")
```

## Inspecting Response Metadata

Beyond the content, the `Response` object contains useful information about the server's reply.

### Status Code

You can check the HTTP status code of the response with the `status_code` attribute.

```python
print(r.status_code) # 200
```

For better readability, Requests provides a lookup object, `requests.codes`, which contains common status codes by name.

```python
if r.status_code == requests.codes.ok: # .ok is an alias for 200
    print("Request was successful!")
else:
    print(f"Request failed with status code {r.status_code}")
```

### Response Headers

The response headers are available as a Python dictionary via `r.headers`. The header keys are case-insensitive.

```python
print(r.headers)
# {'content-type': 'application/json; charset=utf-8', 'cache-control': 'public, max-age=60, s-maxage=60', ...}

print(r.headers['Content-Type'])
# 'application/json; charset=utf-8'

# Access is case-insensitive
print(r.headers.get('content-type'))
# 'application/json; charset=utf-8'
```

### Cookies

If a response contains any cookies, you can access them using `r.cookies`.

```python
r_with_cookies = requests.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
print(r_with_cookies.cookies['sessioncookie'])
# '123456789'
```

## Checking for Errors

Requests simplifies the process of checking if a request was successful.

### The `ok` Property

The `ok` property returns `True` if the `status_code` is less than 400 (i.e., not a client or server error). This provides a simple boolean check for success.

```python
r_success = requests.get('https://httpbin.org/status/200')
if r_success.ok:
    print("Success!") # This will print

r_fail = requests.get('https://httpbin.org/status/404')
if not r_fail.ok:
    print("Failure!") # This will print
```

### The `raise_for_status()` Method

For a more direct approach, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the HTTP request returned an unsuccessful status code (4xx client error or 5xx server error).

```python
from requests.exceptions import HTTPError

bad_r = requests.get('https://httpbin.org/status/404')

try:
    bad_r.raise_for_status()
except HTTPError as http_err:
    print(f'HTTP error occurred: {http_err}') # HTTP error occurred: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

A successful request will not raise an exception:
```python
good_r = requests.get('https://httpbin.org/status/200')
good_r.raise_for_status() # Does nothing
```

This method is a convenient way to assert that a request succeeded within your application's control flow.

## Redirection and History

By default, Requests will automatically perform redirection for all verbs except HEAD. You can inspect the history of redirects that led to the final response.

The `history` property contains a list of the `Response` objects that were created in order to complete the request. The list is sorted from the oldest to the most recent response.

```python
r = requests.get('http://github.com') # This will redirect to https://github.com

print(r.url)
# 'https://github.com/'

print(r.status_code)
# 200

print(r.history)
# [<Response [301]>]
```

The original `Response` for the 301 redirect is stored in the `history` list.

## Streaming Content

For large responses, it's often better to avoid reading the entire content into memory at once. You can achieve this by setting `stream=True` in your request.

### Iterating Over Content

When `stream=True`, you can use the `iter_content()` method to iterate over the response data. This is ideal for downloading large files.

```python
# In this example, we download a large file and save it to disk chunk by chunk
url = 'https://files.pythonhosted.org/packages/source/r/requests/requests-2.31.0.tar.gz'
with requests.get(url, stream=True) as r:
    r.raise_for_status()
    with open('requests.tar.gz', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

The `chunk_size` parameter controls how many bytes are read into memory at a time.

### Iterating Over Lines

For streaming APIs that return line-delimited data (like text or JSON lines), you can use the `iter_lines()` method.

```python
r = requests.get('https://httpbin.org/stream/20', stream=True)

for line in r.iter_lines():
    if line:
        decoded_line = line.decode('utf-8')
        print(decoded_line)
```

This method handles line endings and decodes the content for you, making it easy to process line-by-line.

---

Now that you understand how to inspect and handle responses, you can build more robust applications. The next step is to learn how to persist state across multiple requests. For that, continue to [Session Objects](./user-guide-session-objects.md).
