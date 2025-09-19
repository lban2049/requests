# Request and Response Objects

At the core of the Requests library are the objects that represent the two sides of an HTTP conversation: the request you send and the response you receive. This reference provides a detailed look at the `Request`, `PreparedRequest`, and `Response` objects, detailing their attributes and methods for advanced control over the HTTP interaction.

## The Request Object

The `Request` object is a user-created object that captures all the information for a request you *intend* to make. It's a data container that you can pass around before it's actually prepared for transmission. This is useful for queuing requests or modifying them in a structured way before they are sent over the network.

### Creating a Request

You can instantiate a `Request` object directly, providing all the necessary details for your HTTP request.

```python Request Object Initialization icon=logos:python
import requests

req = requests.Request(
    'POST',
    'https://httpbin.org/post',
    headers={'X-My-Header': 'true'},
    files={'report.xls': open('report.xls', 'rb')},
    data={'foo': 'bar'}
)
```

### Parameters

<x-field-group>
  <x-field data-name="method" data-type="string" data-required="false" data-desc="The HTTP method to use (e.g., 'GET', 'POST', 'PUT')."></x-field>
  <x-field data-name="url" data-type="string" data-required="false" data-desc="The URL to send the request to."></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="A dictionary of HTTP headers to send."></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="A dictionary of {'filename': file-like-object} for multipart file uploads."></x-field>
  <x-field data-name="data" data-type="dict, list[tuple], bytes, or file-like" data-required="false" data-desc="The body to attach to the request. If a dictionary is provided, it will be form-encoded."></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="A JSON-serializable Python object to be sent in the request body."></x-field>
  <x-field data-name="params" data-type="dict or list[tuple]" data-required="false" data-desc="URL parameters to be appended to the URL's query string."></x-field>
  <x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="An authentication handler or a (user, pass) tuple for Basic Auth."></x-field>
  <x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="A dictionary or CookieJar of cookies to attach to the request."></x-field>
  <x-field data-name="hooks" data-type="dict" data-required="false" data-desc="A dictionary of callback hooks for internal usage."></x-field>
</x-field-group>

### Methods

#### `prepare()`

The primary method of the `Request` object. It takes the request's data and prepares it for transmission, returning a `PreparedRequest` object.

```python Preparing a Request icon=logos:python
prepared_request = req.prepare()

print(prepared_request.headers)
print(prepared_request.body)
```

## The PreparedRequest Object

The `PreparedRequest` is the result of calling `request.prepare()`. This object is fully processed and contains the exact bytes that will be sent to the server. You typically don't create a `PreparedRequest` manually. A `Session` object creates one internally before sending it.

### Attributes

<x-field-group>
  <x-field data-name="method" data-type="string" data-desc="The HTTP verb to be sent, normalized to uppercase."></x-field>
  <x-field data-name="url" data-type="string" data-desc="The fully prepared URL, including encoded parameters."></x-field>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A dictionary of all request headers, including those added during preparation (e.g., `Content-Type`, `Content-Length`, `Cookie`)."></x-field>
  <x-field data-name="body" data-type="bytes, string, or file-like" data-desc="The request body, encoded and ready for transmission."></x-field>
  <x-field data-name="_cookies" data-type="CookieJar" data-desc="The CookieJar used to generate the Cookie header."></x-field>
</x-field-group>

### Sending a PreparedRequest

Once you have a `PreparedRequest`, you can send it using a `Session` object. This gives you fine-grained control over the request lifecycle.

```python Sending a PreparedRequest icon=logos:python
import requests

req = requests.Request('GET', 'https://httpbin.org/get', params={'key': 'value'})
s = requests.Session()

# Prepare the request
prepped = s.prepare_request(req) # Session can also prepare it directly

# prepped is now a PreparedRequest object
print(f"Sending {prepped.method} to {prepped.url}")

# Send it
response = s.send(prepped)

print(f"Received response: {response.status_code}")
```

## The Response Object

A `Response` object is returned from any request-sending method like `requests.get()` or `session.send()`. It contains the server's response to your HTTP request.

### Attributes

<x-field-group>
  <x-field data-name="status_code" data-type="int" data-desc="The integer HTTP status code (e.g., 200, 404)."></x-field>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of the response headers."></x-field>
  <x-field data-name="encoding" data-type="string" data-desc="The encoding used to decode `r.text`. Can be set manually."></x-field>
  <x-field data-name="url" data-type="string" data-desc="The final URL location of the response, after any redirects."></x-field>
  <x-field data-name="history" data-type="list[Response]" data-desc="A list of Response objects from the history of the request (redirects)."></x-field>
  <x-field data-name="reason" data-type="string" data-desc="The textual reason for the HTTP status (e.g., 'OK', 'Not Found')."></x-field>
  <x-field data-name="cookies" data-type="CookieJar" data-desc="A CookieJar of cookies the server sent back."></x-field>
  <x-field data-name="elapsed" data-type="timedelta" data-desc="The time elapsed between sending the request and the arrival of the response headers."></x-field>
  <x-field data-name="request" data-type="PreparedRequest" data-desc="The PreparedRequest object to which this is a response."></x-field>
  <x-field data-name="raw" data-type="urllib3.response.HTTPResponse" data-desc="The underlying raw response object from urllib3. Requires `stream=True` on the request."></x-field>
</x-field-group>

### Content Access

<x-cards>
  <x-card data-title=".content" data-icon="lucide:file-text">
    Returns the response body in bytes. Ideal for non-text content like images or files.
  </x-card>
  <x-card data-title=".text" data-icon="lucide:text">
    Returns the response body as a string, decoded using the determined encoding.
  </x-card>
  <x-card data-title=".json(**kwargs)" data-icon="lucide:braces">
    Deserializes the response body from JSON into a Python object. Raises an exception if the body is not valid JSON.
  </x-card>
</x-cards>

```python Accessing Response Content icon=logos:python
import requests

r = requests.get('https://api.github.com/events')

# Access as bytes
byte_content = r.content

# Access as string
text_content = r.text

# Access as JSON
json_content = r.json()

print(f"First event type: {json_content[0]['type']}")
```

### Status and Error Handling

<x-cards>
  <x-card data-title=".ok" data-icon="lucide:check-circle">
    A boolean property that is `True` if the status code is less than 400, `False` otherwise.
  </x-card>
  <x-card data-title=".raise_for_status()" data-icon="lucide:shield-alert">
    A method that raises an `HTTPError` if the request returned an unsuccessful status code (4xx or 5xx).
  </x-card>
</x-cards>

```python Checking Response Status icon=logos:python
import requests

r = requests.get('https://httpbin.org/status/404')

if r.ok:
    print("Request was successful!")
else:
    print(f"Request failed with status code: {r.status_code}")

try:
    r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"An HTTP error occurred: {err}")

# Output:
# Request failed with status code: 404
# An HTTP error occurred: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

### Streaming Content

For large responses, you can stream the content to avoid loading it all into memory at once.

- **`iter_content(chunk_size=1, decode_unicode=False)`**: Iterates over the response data in chunks.
- **`iter_lines(chunk_size=512, decode_unicode=False)`**: Iterates over the response data, one line at a time.

```python Streaming a Large File icon=logos:python
import requests

# Use stream=True to enable streaming
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status()
    with open('streamed_data.txt', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

## Status Code Lookups

Requests provides a convenient lookup object for referencing HTTP status codes by their common names, which is useful when checking the `response.status_code` attribute.

```python Using Status Code Constants icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

if r.status_code == requests.codes.ok: # Same as r.status_code == 200
    print("Request was OK!")
```

Here are some common codes available under `requests.codes`:

| Code | Constant(s) |
| :--- | :--- |
| 200 | `ok`, `okay`, `all_ok` |
| 201 | `created` |
| 301 | `moved_permanently`, `moved` |
| 302 | `found` |
| 400 | `bad_request`, `bad` |
| 401 | `unauthorized` |
| 403 | `forbidden` |
| 404 | `not_found` |
| 500 | `internal_server_error`, `server_error` |
| 503 | `service_unavailable`, `unavailable` |
