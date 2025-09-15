# Handling Responses

After you've made a request using Requests, the server's response is available in a `Response` object. This object contains a wealth of information, from the response body to headers, cookies, and status codes. This guide will walk you through how to access and work with this data.

If you haven't made a request yet, you might want to review the [Making a Request](./user-guide-making-a-request.md) guide first.

## Reading Response Content

Requests can handle various types of content returned by the server. Let's explore the most common ones.

### Text Content

For text-based responses, such as HTML or plain text, you can use the `.text` attribute. Requests will automatically decode the response content into a Unicode string.

```python example.py icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.text)
```

Requests makes an educated guess about the response encoding based on the HTTP headers. If you find that the encoding is incorrect, you can manually set it before accessing `.text`.

```python set_encoding.py icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
r.encoding = 'utf-8' # Manually set the encoding
print(r.text)
```

### Binary Content

For non-text content like images, PDFs, or other files, you should use the `.content` attribute. This provides the raw bytes of the response body.

Here's an example of how you can download an image and save it to a file:

```python download_image.py icon=logos:python
import requests

r = requests.get('https://raw.githubusercontent.com/psf/requests/main/ext/requests-logo.png')

with open('requests-logo.png', 'wb') as f:
    f.write(r.content)
```
This code will save a file named `requests-logo.png` in your current working directory.

![requests-logo.png](../../../ext/requests-logo.png)

### JSON Response Content

Many modern APIs return data in JSON format. Requests has a built-in JSON decoder, `r.json()`, which will parse the response content and return a Python dictionary or list.

```python json_response.py icon=logos:python
import requests

r = requests.get('https://httpbin.org/json')
json_data = r.json()

print(json_data['slideshow']['title'])
```

**Example Response**
```json
{
  "slideshow": {
    "author": "Yours Truly", 
    "date": "date of publication", 
    "slides": [
      {
        "title": "Wake up to WonderWidgets!", 
        "type": "all"
      }, 
      {
        "items": [
          "Why <em>WonderWidgets</em> are great", 
          "Who <em>buys</em> WonderWidgets"
        ], 
        "title": "Overview", 
        "type": "all"
      }
    ], 
    "title": "Sample Slide Show"
  }
}
```

If the response does not contain valid JSON, calling `r.json()` will raise a `requests.exceptions.JSONDecodeError`. It's good practice to check the response status or headers before attempting to parse it as JSON.

### Streaming Large Responses

For large downloads, it's inefficient to load the entire response into memory at once. You can handle this by setting `stream=True` in your request. This allows you to iterate over the content in chunks.

`iter_content()` allows you to iterate over the response data. You can specify a `chunk_size` in bytes.

```python stream_download.py icon=logos:python
import requests

# A large file example URL
url = 'https://speed.hetzner.de/100MB.bin'

with requests.get(url, stream=True) as r:
    r.raise_for_status() # Ensure the request was successful
    with open('large_file.bin', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            # The chunk_size is the number of bytes it should read into memory.
            # This is not necessarily the length of each item returned as decoding can take place.
            f.write(chunk)
```

You can also iterate over the response line by line using `iter_lines()`.

```python stream_lines.py icon=logos:python
import requests

url = 'https://httpbin.org/stream/20' # An endpoint that streams lines

with requests.get(url, stream=True) as r:
    for line in r.iter_lines():
        if line:
            # filter out keep-alive new lines
            decoded_line = line.decode('utf-8')
            print(decoded_line)
```

## Response Status and Headers

Beyond the body, the status code and headers provide crucial information about the response.

### Status Code

You can check the HTTP status code of the response using the `status_code` attribute.

<x-field data-name="status_code" data-type="number" data-desc="The integer representation of the HTTP status code (e.g., 200, 404)."></x-field>

Requests also provides a convenient lookup object, `requests.codes`, to access status codes by name.

```python status_check.py icon=logos:python
import requests

r = requests.get('https://httpbin.org/status/404')

if r.status_code == 200:
    print('Success!')
elif r.status_code == requests.codes.not_found: # Same as 404
    print('Resource not found.')
else:
    print(f'Request failed with status code: {r.status_code}')
```

### Checking for Errors

Instead of checking the status code manually, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the request returned an unsuccessful status code (4xx client error or 5xx server error).

```python raise_for_status.py icon=logos:python
import requests
from requests.exceptions import HTTPError

urls = ['https://httpbin.org/get', 'https://httpbin.org/status/500']

for url in urls:
    try:
        r = requests.get(url)
        r.raise_for_status() # Raises an exception for bad status codes
        print(f'{url}: Success!')
    except HTTPError as http_err:
        print(f'HTTP error occurred: {http_err}')
    except Exception as err:
        print(f'Other error occurred: {err}')
```

### Response Headers

The `headers` attribute provides a dictionary-like object containing the response headers. The keys are case-insensitive.

<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of response headers."></x-field>

```python get_headers.py icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

print(r.headers)
# Access a specific header
print(f"Content-Type: {r.headers['Content-Type']}")
# Case-insensitive access
print(f"content-type: {r.headers['content-type']}")
```

## Cookies

If the response contains any cookies, you can access them through the `cookies` attribute, which returns a `RequestsCookieJar` object.

```python get_cookies.py icon=logos:python
import requests

# This endpoint sets a cookie
r = requests.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

# Access the cookie
cookie_value = r.cookies['sessioncookie']
print(f'Session Cookie Value: {cookie_value}')
```

## Redirection and History

By default, Requests automatically performs redirection for status codes like 301 and 302. The `history` attribute of the `Response` object stores a list of the older `Response` objects that were part of the redirection chain. The list is sorted from the oldest to the most recent response.

<x-field data-name="history" data-type="list[Response]" data-desc="A list of Response objects from the history of the request, in case of redirects."></x-field>
<x-field data-name="url" data-type="string" data-desc="The final URL location of the Response."></x-field>

```python check_history.py icon=logos:python
import requests

r = requests.get('https://github.com')

print(f'Final URL: {r.url}')
print(f'Status Code: {r.status_code}')

if r.history:
    print('Request was redirected.')
    for resp in r.history:
        print(f'  - Redirect from {resp.url} (Status: {resp.status_code})')
else:
    print('Request was not redirected.')
```

## Next Steps

You now have a solid understanding of how to inspect and handle the data returned from a server. To learn how to persist information like cookies and headers across multiple requests, proceed to the next section on [Session Objects](./user-guide-session-objects.md).