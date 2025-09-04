# 处理响应

发出请求后，Requests 会返回一个包含服务器响应的 `Response` 对象。该对象持有您需要的所有信息，从状态码、响应头到响应体本身。本指南将引导您了解检查和处理此响应数据的最常用方法。

有关发送请求的更多详细信息，请参阅上一节 [发送请求](./user-guide-making-a-request.md)。

## 检查状态码

收到响应后的第一步通常是检查请求是否成功。`status_code` 属性以整数形式提供 HTTP 状态码。

```python
import requests

r = requests.get('https://httpbin.org/status/200')
print(r.status_code)
# 200

if r.status_code == 200:
    print('Success!')
elif r.status_code == 404:
    print('Not Found.')
```

为方便起见，Requests 提供了一个用于常见状态码的查找对象，使您的代码更具可读性。

```python
if r.status_code == requests.codes.ok: # .ok 是 200 的别名
    print('Request was successful.')
```

### 针对错误响应抛出异常

除了手动检查状态码，您还可以使用 `raise_for_status()` 方法。如果 HTTP 请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法将抛出 `HTTPError` 异常。

```python
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

# 控制台输出：
# HTTP error occurred: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

如果请求成功（状态码在 2xx 范围内），`raise_for_status()` 将不会执行任何操作。

## 访问响应头

响应头可通过 `headers` 属性以一个类字典对象的形式访问。其一个主要特点是，响应头的键名不区分大小写。

```python
r = requests.get('https://httpbin.org/get')

# 访问响应头
print(r.headers['Content-Type'])
# 'application/json'

# 不区分大小写
print(r.headers.get('content-type'))
# 'application/json'
```

## 访问响应体

根据响应的 `Content-Type`，您可以通过多种方式访问响应体。

### 原始二进制内容

对于图片或 PDF 文件等非文本响应，您可以使用 `content` 属性访问响应体的原始字节。

以下是一个从 URL 保存图片的示例：

```python
import requests

r = requests.get('https://raw.githubusercontent.com/psf/requests/main/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(r.content)

# 这将在您当前目录下保存 'requests_logo.png'。
```

### 文本内容

对于文本数据，`text` 属性以字符串形式提供响应体。Requests 会根据响应头中指定的字符编码自动解码内容。如果未指定编码，它会尽力猜测。

```python
r = requests.get('https://httpbin.org/html')
print(r.text)
```

**响应示例**
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

如果您发现编码检测不正确，可以在访问 `.text` 之前手动设置 `encoding` 属性：

```python
r.encoding = 'utf-8'
print(r.text)
```

### JSON 内容

如果响应包含 JSON 数据，您可以使用内置的 `json()` 方法将其解析为 Python 字典或列表。

```python
r = requests.get('https://httpbin.org/json')

data = r.json()

# 从解析后的 JSON 中访问数据
slideshow_title = data['slideshow']['title']
print(f'Slideshow Title: {slideshow_title}')
```

**`r.json()` 的响应示例**
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

如果响应体不包含有效的 JSON，调用 `.json()` 将会抛出 `requests.exceptions.JSONDecodeError` 异常。

## 流式内容

对于非常大的响应，您可以在请求中使用 `stream=True` 来避免一次性将全部内容加载到内存中。然后，您可以使用 `iter_content()` 或 `iter_lines()` 遍历响应内容。

```python
# 使用 iter_content 下载大文件
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status() # 确保请求成功
    with open('large_file.txt', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            # chunk 将是一个字节对象
            f.write(chunk)
```
这种方法在处理大文件下载或数据流时效率很高，且不会占用大量内存。

---

既然您已经了解如何处理响应，就可以通过使用 [会话对象](./user-guide-session-objects.md) 来提高请求的效率和状态管理。