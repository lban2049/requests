# Handling Response Content

Once you've made a request, your `Response` object holds the server's reply. The first step in handling a response is often to inspect its body. Requests provides several convenient ways to access the response body, tailored to different types of content.

This guide covers how to read the response as raw bytes, decoded text, and structured JSON. For a primer on making the initial request, see [Making a Request](./core-usage-making-a-request.md).

## Binary Response Content

You can access the server's response as a sequence of bytes using the `response.content` attribute. This is the raw payload, perfect for non-textual content like images, PDFs, or other binary files.

```python Downloading an Image icon=logos:python
import requests

r = requests.get('https://httpbin.org/image/png')

# The content is in bytes
print(r.content[:20])
# Expected output might start with: b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...'

# You can save these bytes directly to a file
with open('image.png', 'wb') as f:
    f.write(r.content)
```

The `b''` prefix in the output indicates that you are working with a `bytes` object. Writing this content to a file in binary write mode (`'wb'`) ensures the file is saved correctly without any text encoding interference.

## Text Response Content

For textual data, such as HTML or plain text, the `response.text` attribute is more convenient. It automatically decodes the raw bytes from `response.content` into a Python string.

```python Reading Text Content icon=logos:python
import requests

r = requests.get('https://httpbin.org/html')

# The content is a unicode string
print(r.text)
# Expected output:
# <!DOCTYPE html>
# <html>
#   <head>
#   </head>
#   <body>
#       <h1>Herman Melville - Moby-Dick</h1>
# ...
```

Requests intelligently determines the encoding. It first checks the `Content-Type` HTTP header for a `charset`. If none is specified, it uses a library like `chardet` to guess the encoding from the content itself. You can inspect the chosen encoding and, if necessary, override it before accessing `.text`:

```python Overriding Encoding icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

# Check the auto-detected encoding
print(f"Auto-detected encoding: {r.encoding}")

# Override with a different encoding if needed
r.encoding = 'ISO-8859-1'

print(f"Manually set encoding: {r.encoding}")
print(r.text)
```

## JSON Response Content

Many web APIs return data in JSON format. Requests includes a built-in JSON decoder, the `response.json()` method, which parses the response body into a Python object (typically a `dict` or `list`).

```python Parsing a JSON Response icon=logos:python
import requests
from requests.exceptions import JSONDecodeError

r = requests.get('https://httpbin.org/json')

try:
    # The .json() method returns a Python dictionary
    data = r.json()
    slideshow_title = data['slideshow']['title']
    print(f"Slideshow Title: {slideshow_title}")
except JSONDecodeError:
    print("Response could not be decoded as JSON.")
except KeyError:
    print("JSON structure is not as expected.")

# Expected output:
# Slideshow Title: Showcase
```

If the server returns a response that isn't valid JSON, calling `.json()` will raise a `requests.exceptions.JSONDecodeError`. It's good practice to wrap the call in a `try...except` block to handle this case gracefully.

## Streaming Large Responses

By default, when you access `.content` or `.text`, the entire response body is downloaded and loaded into memory at once. This can be problematic for very large files. To avoid this, you can stream the response by setting `stream=True` in your request.

When streaming, the response content is downloaded only as you iterate over it.

```python Streaming and Saving a File icon=logos:python
import requests

# Use stream=True to defer downloading the response body
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status() # Check that the request was successful
    with open('streamed_data.jsonl', 'wb') as f:
        # Iterate over the response in chunks of 8192 bytes
        for chunk in r.iter_content(chunk_size=8192):
            # chunk will be a bytes object
            f.write(chunk)
```

Using a `with` statement ensures that the connection is properly closed even if errors occur. The `iter_content()` method allows you to process the payload in manageable pieces. For line-based text streams, you can also use the convenient `iter_lines()` method.

---

You've now seen the primary ways to handle response bodies, from simple byte and text access to robust JSON parsing and efficient streaming. With this foundation, you can confidently process data from any API.

To learn how to send data *to* a server, continue to the next section: [POSTing Data](./core-usage-posting-data.md).