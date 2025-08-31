# Handling Responses

After making a request, `requests` returns a `Response` object. This object contains the server's response to your HTTP request, including the content, status code, and headers. This guide will walk you through how to access and work with this information.

For details on how to create the initial request, please see the [Making a Request](./user-guide-making-a-request.md) guide.

## Response Content

`requests` simplifies accessing the body of the response in various formats.

### Binary Response Content

You can access the raw bytes of the response body using the `content` attribute. This is useful for non-textual content, such as images, PDFs, or other files. `requests` will automatically decode `gzip` and `deflate` transfer-encodings for you.

**Example: Saving an image**
```python
import requests

r = requests.get('https://httpbin.org/image/png')

# r.content returns the image data in bytes.
with open('example.png', 'wb') as f:
    f.write(r.content)
```
This code fetches a PNG image and saves it to a file named `example.png` in binary write mode.

### Text Response Content

For textual data, the `text` attribute provides the response content as a standard Python string. `requests` attempts to determine the character encoding from the response's HTTP headers. If the server does not specify an encoding, `requests` will use an external library like `chardet` to estimate it.

You can find out which encoding `requests` is using:

```python
import requests

r = requests.get('https://httpbin.org/html')
print(r.encoding)
# Output: utf-8

print(r.text)
# Output: The HTML content as a string
```

If you need to override the detected encoding, you can set the `encoding` attribute manually before accessing `.text`:

```python
r.encoding = 'ISO-8859-1'
```

### JSON Response Content

If the response contains JSON data, you can use the built-in `json()` method. This method parses the content and returns a Python dictionary or list.

```python
import requests
from requests.exceptions import JSONDecodeError

r = requests.get('https://httpbin.org/json')
try:
    data = r.json()
    print(data['slideshow']['title'])
except JSONDecodeError:
    print("Response could not be decoded as JSON.")
except KeyError:
    print("JSON does not contain expected keys.")

```
If the response body does not contain valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`.

### Raw Response Stream

For advanced cases where you need to process a large response without loading it all into memory, you can use a raw stream. To enable this, set `stream=True` in your initial request. This provides access to the raw response which you can iterate over.

```python
import requests

r = requests.get('https://httpbin.org/stream/20', stream=True)

# Set encoding if not provided by the server
if r.encoding is None:
    r.encoding = 'utf-8'

# iter_lines processes the stream line by line
for line in r.iter_lines(decode_unicode=True):
    if line:
        print(line)
```

## Response Status

After receiving a response, the first step is typically to check its status to see if the request was successful.

```d2
direction: down

response: "Receive Response object"
check_status: "Check status (r.ok, r.raise_for_status())"
is_ok: "Successful (2xx)?"
process: "Process content (r.json(), r.text)"
handle_error: "Handle error (4xx/5xx)"
done: "Done"

response -> check_status
check_status -> is_ok
is_ok -> process: Yes
is_ok -> handle_error: No
process -> done
handle_error -> done
```

### Status Codes

The `status_code` attribute provides the HTTP status code as an integer.

```python
import requests

r = requests.get('https://httpbin.org/status/404')
print(r.status_code)
# Output: 404
```

`requests` also includes a status code lookup object, `requests.codes`, which makes your code more readable by using descriptive names instead of numbers.

```python
if r.status_code == requests.codes.not_found:
    print('The requested resource was not found.')
```

### Checking for Errors

Instead of checking the status code manually, you can use the `raise_for_status()` method. It will raise an `HTTPError` if the request returned an unsuccessful status code (a 4xx client error or 5xx server error).

```python
import requests
from requests.exceptions import HTTPError

for status in [200, 404, 500]:
    try:
        url = f'https://httpbin.org/status/{status}'
        r = requests.get(url)
        r.raise_for_status()
    except HTTPError as http_err:
        print(f'HTTP error for status {status}: {http_err}')
    else:
        print(f'Success for status {status}!')
```

The `Response` object also has a boolean `ok` property, which is `True` if the status code is less than 400.

```python
r = requests.get('https://httpbin.org/get')
if r.ok:  # or simply `if r:`
    print('Request was successful.')
else:
    print('Request failed.')
```

## Response Headers

The response headers are available in the `headers` attribute. This attribute is a dictionary-like object where the keys are case-insensitive.

```python
import requests

r = requests.get('https://httpbin.org/get')

print(r.headers)
# Output: A CaseInsensitiveDict object of headers

# Access is case-insensitive
print(r.headers['Content-Type'])
# Output: 'application/json'

print(r.headers.get('content-type'))
# Output: 'application/json'
```

## Cookies

If the server sends any cookies, you can access them through the `cookies` attribute, which is a `RequestsCookieJar` object.

```python
import requests

r = requests.get('https://httpbin.org/cookies/set?name=mycookie&value=12345')

print(r.cookies['mycookie'])
# Output: '12345'
```

## Redirection and History

By default, `requests` automatically follows redirects. The `Response` object you receive is the final response after all redirects have occurred. You can access the history of the requests that led to the final destination via the `history` attribute. It contains a list of the `Response` objects, from the oldest to the most recent.

```python
import requests

r = requests.get('https://httpbin.org/redirect/3')

print(f"Final URL: {r.url}")
print(f"Final Status Code: {r.status_code}")

print("Request History:")
for resp in r.history:
    print(f"  - {resp.status_code} from {resp.url}")

# You can also check if the response was a permanent redirect
if r.is_permanent_redirect:
    print("This was a permanent redirect.")
```

## Next Steps

Now that you understand how to handle responses, you can learn how to persist parameters and cookies across multiple requests for improved performance and state management.

<x-card data-title="Session Objects" data-href="/user-guide/session-objects" data-icon="lucide:book-copy" data-cta="Continue Reading">
  Utilize Session objects to persist parameters, cookies, and headers across multiple requests.
</x-card>

This practice is key to building efficient and stateful applications.