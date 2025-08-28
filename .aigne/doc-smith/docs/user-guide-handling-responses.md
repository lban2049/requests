# Handling Responses

When you make a request, Requests returns a `Response` object. This object contains the server's response, including the content, status code, headers, cookies, and more. This guide covers how to access and work with this data.

```python
import requests

r = requests.get('https://api.github.com/events')
```

## Response Content

Requests can handle different types of response content and will automatically decode it for you when possible.

### Text Content

For text-based responses, such as HTML or plain text, you can access the content as a string using the `.text` attribute. Requests automatically decodes the content from the server's response.

```python
>>> r.text
'[{"id":"1234567890","type":"PushEvent","actor":{...}}]'
```

Requests makes a guess about the character encoding based on the HTTP headers. If you need to override this, you can set the `.encoding` attribute manually before accessing `.text`.

```python
>>> r.encoding
'utf-8'
>>> r.encoding = 'ISO-8859-1'
```

### Binary Content

For non-textual content, such as images or PDF files, you can access the response body as bytes using the `.content` attribute. This gives you the raw bytes of the response, which you can then save to a file.

Here's an example of saving an image:

```python
img_response = requests.get('https://raw.githubusercontent.com/psf/requests/main/docs/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(img_response.content)
```

![requests-logo](../../../ext/requests-logo.png)

### JSON Content

Many web APIs return data in JSON format. Requests has a built-in JSON decoder, `.json()`, which will parse the response content and return a Python dictionary or list.

```python
import requests

r = requests.get('https://api.github.com/events')
json_data = r.json()

# Access a value from the parsed JSON
print(json_data[0]['type'])
```

If the response does not contain valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`.

### Streaming Content

For large responses, you can avoid loading the entire content into memory at once by using the `iter_content()` method with `stream=True` in your request. This is useful for downloading large files.

```python
# stream=True is required for this to work
r = requests.get('https://httpbin.org/stream/20', stream=True)

for chunk in r.iter_content(chunk_size=128):
    # Process each chunk as it's received
    print(chunk)
```

## Response Status Codes

You can check the HTTP status code of the response to understand if your request was successful.

```python
>>> r.status_code
200
```

Requests provides a `codes` object for comparing status codes with common names, which can make your code more readable.

```python
>>> r.status_code == requests.codes.ok
True
```

### Checking for Errors

While you can check `r.status_code` manually, Requests offers a simpler way to check for success. The `ok` attribute of a `Response` object is `True` if the status code is less than 400, and `False` otherwise.

```python
if r.ok:
    print('Request was successful')
else:
    print('Request failed')
```

Alternatively, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the request returned an unsuccessful status code (a 4xx client error or 5xx server error).

```python
try:
    bad_r = requests.get('https://httpbin.org/status/404')
    bad_r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(err)
```

This is a convenient way to ensure your program handles errors when a request fails.

## Response Headers

The response headers are available as a case-insensitive, dictionary-like object at `r.headers`.

```python
>>> r.headers
{'Content-Type': 'application/json; charset=utf-8', 'Server': 'gunicorn/19.9.0', ...}

# Accessing headers is case-insensitive
>>> r.headers['Content-Type']
'application/json; charset=utf-8'

>>> r.headers.get('content-type')
'application/json; charset=utf-8'
```

## Cookies

If the server sends any cookies, you can access them through the `r.cookies` object, which is a `RequestsCookieJar`.

```python
>>> url = 'https://httpbin.org/cookies/set/sessioncookie/123456789'
>>> r = requests.get(url)

>>> r.cookies['sessioncookie']
'123456789'
```

## Redirection and History

Requests automatically handles HTTP redirects. The `Response` object you receive is the final response after following any redirects.

To see the history of requests that led to the final response, you can use the `.history` property. It contains a list of the older `Response` objects, from oldest to most recent.

```python
>>> r = requests.get('http://github.com') # Note: http, not https

>>> r.url
'https://github.com/'

>>> r.status_code
200

>>> r.history
(<Response [301]>,)
```

In this case, the original request to `http://github.com` resulted in a 301 Moved Permanently redirect, which is stored in the `.history` tuple.

---

Now that you know how to inspect and handle responses, you can improve efficiency by persisting parameters across multiple requests using [Session Objects](./user-guide-session-objects.md).
