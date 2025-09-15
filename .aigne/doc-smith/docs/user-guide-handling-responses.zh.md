# 处理响应

使用 Requests 发出请求后，服务器的响应会存储在一个 `Response` 对象中。该对象包含了丰富的信息，从响应体到标头、Cookie 和状态码。本指南将引导你如何访问和使用这些数据。

如果你还没有发出过请求，建议先查阅 [发出请求](./user-guide-making-a-request.md) 指南。

## 读取响应内容

Requests 可以处理服务器返回的各种类型的内容。让我们来探讨最常见的几种。

### 文本内容

对于基于文本的响应，例如 HTML 或纯文本，你可以使用 `.text` 属性。Requests 会自动将响应内容解码为 Unicode 字符串。

```python example.py icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.text)
```

Requests 会根据 HTTP 标头对响应的编码进行智能猜测。如果你发现编码不正确，可以在访问 `.text` 之前手动设置它。

```python set_encoding.py icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
r.encoding = 'utf-8' # Manually set the encoding
print(r.text)
```

### 二进制内容

对于非文本内容，如图像、PDF 或其他文件，你应该使用 `.content` 属性。它提供响应体的原始字节。

下面是一个如何下载图像并将其保存到文件的示例：

```python download_image.py icon=logos:python
import requests

r = requests.get('https://raw.githubusercontent.com/psf/requests/main/ext/requests-logo.png')

with open('requests-logo.png', 'wb') as f:
    f.write(r.content)
```
这段代码将在你当前的工作目录下保存一个名为 `requests-logo.png` 的文件。

![requests-logo.png](../../../ext/requests-logo.png)

### JSON 响应内容

许多现代 API 以 JSON 格式返回数据。Requests 内置了 JSON 解码器 `r.json()`，它会解析响应内容并返回一个 Python 字典或列表。

```python json_response.py icon=logos:python
import requests

r = requests.get('https://httpbin.org/json')
json_data = r.json()

print(json_data['slideshow']['title'])
```

**响应示例**
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

如果响应不包含有效的 JSON，调用 `r.json()` 将会引发 `requests.exceptions.JSONDecodeError` 异常。在尝试将其解析为 JSON 之前，最好检查响应状态或标头。

### 流式传输大响应

对于大文件下载，一次性将整个响应加载到内存中效率很低。你可以通过在请求中设置 `stream=True` 来处理这种情况。这允许你按块迭代内容。

`iter_content()` 允许你遍历响应数据。你可以指定一个以字节为单位的 `chunk_size`。

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

你也可以使用 `iter_lines()` 逐行遍历响应。

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

## 响应状态和标头

除了响应体，状态码和标头也提供了关于响应的关键信息。

### 状态码

你可以使用 `status_code` 属性来检查响应的 HTTP 状态码。

<x-field data-name="status_code" data-type="number" data-desc="HTTP 状态码的整数表示形式（例如，200, 404）。"></x-field>

Requests 还提供了一个方便的查找对象 `requests.codes`，用于通过名称访问状态码。

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

### 检查错误

除了手动检查状态码，你还可以使用 `raise_for_status()` 方法。如果请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），它将引发一个 `HTTPError`。

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

### 响应标头

`headers` 属性提供了一个包含响应标头的类字典对象。其键名不区分大小写。

<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="一个不区分大小写的响应标头字典。"></x-field>

```python get_headers.py icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

print(r.headers)
# Access a specific header
print(f"Content-Type: {r.headers['Content-Type']}")
# Case-insensitive access
print(f"content-type: {r.headers['content-type']}")
```

## Cookie

如果响应包含任何 Cookie，你可以通过 `cookies` 属性访问它们，该属性返回一个 `RequestsCookieJar` 对象。

```python get_cookies.py icon=logos:python
import requests

# This endpoint sets a cookie
r = requests.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

# Access the cookie
cookie_value = r.cookies['sessioncookie']
print(f'Session Cookie Value: {cookie_value}')
```

## 重定向与历史记录

默认情况下，Requests 会自动对 301 和 302 等状态码执行重定向。`Response` 对象的 `history` 属性存储了重定向链中较早的 `Response` 对象的列表。该列表按从最早到最近的响应排序。

<x-field data-name="history" data-type="list[Response]" data-desc="一个包含请求历史记录中 Response 对象的列表，用于处理重定向。"></x-field>
<x-field data-name="url" data-type="string" data-desc="响应的最终 URL 位置。"></x-field>

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

## 后续步骤

现在你已经对如何检查和处理从服务器返回的数据有了扎实的了解。要了解如何在多个请求之间持久化 Cookie 和标头等信息，请继续阅读下一节 [会话对象](./user-guide-session-objects.md)。