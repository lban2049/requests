# Handling Responses

After you make a request, Requests returns a `Response` object, which contains the server's response. This object holds all the information you need, from the content of the page to metadata like status codes and headers.

```python Make a simple request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

Now that we have the `r` object, let's explore how to inspect its contents.

## Response Content

Requests provides several ways to access the body of the response, depending on the content type.

### Text Content

For text-based responses, such as HTML or plain text, you can use the `text` attribute. Requests automatically decodes the content from the server's response.

```python View text response icon=logos:python
r = requests.get('https://api.github.com/events')
print(r.text)
# '[{"id":"34343116709","type":"PushEvent","actor":{"id":...'
```

Requests makes an educated guess about the encoding based on the HTTP headers. If you need to override this, you can set the `encoding` attribute manually before accessing `.text`:

```python Manually set encoding icon=logos:python
r.encoding = 'utf-8'
print(r.text)
```

### Binary Response Content

For non-text content, like images or PDF files, you can access the raw bytes of the response using the `content` attribute. This is the best approach for downloading files, as it avoids any decoding issues.

```python Getting an image icon=logos:python
r = requests.get('https://httpbin.org/image/png')
print(r.content)
# b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...'

# You could save this to a file
with open('image.png', 'wb') as f:
    f.write(r.content)
```

### JSON Response Content

If you're working with an API that returns JSON, Requests has a built-in JSON decoder. Simply call the `json()` method to parse the response content into a Python dictionary or list.

```python Decoding JSON icon=logos:python
r = requests.get('https://api.github.com/events')
data = r.json()

# Access data like a normal Python object
first_event_type = data[0]['type']
print(first_event_type)
# 'PushEvent'
```

If the response does not contain valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`.

### Streaming Content

For very large responses, you can avoid loading the entire content into memory at once. By setting `stream=True` in your request, you can iterate over the response data as it arrives.

Use the `iter_content()` method to control the chunk size. This is ideal for downloading large files.

```python Saving a large file icon=logos:python
# Download a 100KB file in chunks of 8KB
with requests.get('https://httpbin.org/stream-bytes/102400', stream=True) as r:
    r.raise_for_status() # Ensure the request was successful
    with open('large_file.bin', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

You can also iterate over the response line-by-line using `iter_lines()`.

## Response Status Codes

It's crucial to check the status code of a response to verify if the request was successful.

<x-field data-name="status_code" data-type="integer" data-desc="The HTTP status code of the response (e.g., 200, 404)."></x-field>
<x-field data-name="reason" data-type="string" data-desc="The textual reason for the status (e.g., 'OK', 'Not Found')."></x-field>
<x-field data-name="ok" data-type="boolean" data-desc="Returns True if the status code is less than 400, False otherwise. A simple way to check for success."></x-field>

```python Status Code Example icon=logos:python
r = requests.get('https://httpbin.org/status/404')
print(r.status_code)
# 404

print(r.reason)
# 'NOT FOUND'

if not r.ok:
    print("Request failed!")
```

Requests also provides a convenient lookup object, `requests.codes`, for comparing status codes with human-readable names.

| Code | `requests.codes` Attribute |
|---|---|
| 200 | `ok`, `okay`, `all_ok` |
| 301 | `moved_permanently`, `moved` |
| 302 | `found` |
| 400 | `bad_request`, `bad` |
| 401 | `unauthorized` |
| 403 | `forbidden` |
| 404 | `not_found` |
| 500 | `internal_server_error` |

### Raising an Exception for Bad Responses

Instead of checking `r.ok` manually, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the request returned an unsuccessful status code (4xx client error or 5xx server error).

```python Using raise_for_status icon=logos:python
try:
    r = requests.get('https://httpbin.org/status/404')
    r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"HTTP error occurred: {err}")

# A successful request will not raise an exception
r = requests.get('https://httpbin.org/status/200')
r.raise_for_status()
print("Request was successful!")
```

## Response Headers

The response headers are available as a dictionary-like object at `r.headers`. The header dictionary is special: it's case-insensitive.

```python Accessing Headers icon=logos:python
r = requests.get('https://httpbin.org/get')

# Accessing is case-insensitive
content_type_1 = r.headers['Content-Type']
content_type_2 = r.headers.get('content-type')

print(content_type_1)
# 'application/json'
print(content_type_2)
# 'application/json'
```

## Cookies

If the server sends any cookies, you can access them through the `r.cookies` attribute, which is a `CookieJar` object that acts like a dictionary.

```python Working with Cookies icon=logos:python
r = requests.get('https://httpbin.org/cookies/set/flavor/chocolatechip')
cookie_value = r.cookies['flavor']

print(cookie_value)
# 'chocolatechip'
```

## Redirection and History

Requests automatically handles HTTP redirects. The `history` attribute of the `Response` object contains a list of the older `Response` objects that were part of the redirection chain. The list is sorted from the oldest to the most recent response.

```python Redirection History icon=logos:python
r = requests.get('https://github.com')

print(f"Final URL: {r.url}")
# Final URL: https://github.com/

print(f"Status Code: {r.status_code}")
# Status Code: 200

# Let's try a URL that redirects
r_redirect = requests.get('http://github.com') # Note: http, not https

print(f"Final URL after redirect: {r_redirect.url}")
# Final URL after redirect: https://github.com/

print("Redirect History:")
for resp in r_redirect.history:
    print(f"- {resp.status_code}: {resp.url}")
# Redirect History:
# - 301: http://github.com/
```

---

Now that you can confidently handle responses, let's explore how to manage state and improve performance across multiple requests using [Session Objects](./user-guide-session-objects.md).