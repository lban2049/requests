# 处理响应

当你发出请求时，Requests 会返回一个 `Response` 对象。该对象包含服务器的响应，包括内容、状态码、标头、Cookie 等。本指南将介绍如何访问和使用这些数据。

```python
import requests

r = requests.get('https://api.github.com/events')
```

## 响应内容

Requests 可以处理不同类型的响应内容，并在可能的情况下自动为你解码。

### 文本内容

对于基于文本的响应，例如 HTML 或纯文本，你可以使用 `.text` 属性以字符串形式访问其内容。Requests 会自动根据服务器的响应对内容进行解码。

```python
>>> r.text
'[{"id":"1234567890","type":"PushEvent","actor":{...}}]'
```

Requests 会根据 HTTP 标头猜测字符编码。如果你需要覆盖此设置，可以在访问 `.text` 之前手动设置 `.encoding` 属性。

```python
>>> r.encoding
'utf-8'
>>> r.encoding = 'ISO-8859-1'
```

### 二进制内容

对于非文本内容，例如图片或 PDF 文件，你可以使用 `.content` 属性以字节形式访问响应正文。这将为你提供响应的原始字节，然后你可以将其保存到文件中。

以下是保存图片的示例：

```python
img_response = requests.get('https://raw.githubusercontent.com/psf/requests/main/docs/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(img_response.content)
```

![requests-logo](../../../ext/requests-logo.png)

### JSON 内容

许多 Web API 以 JSON 格式返回数据。Requests 内置了一个 JSON 解码器 `.json()`，它会解析响应内容并返回一个 Python 字典或列表。

```python
import requests

r = requests.get('https://api.github.com/events')
json_data = r.json()

# Access a value from the parsed JSON
print(json_data[0]['type'])
```

如果响应不包含有效的 JSON，调用 `.json()` 将引发 `requests.exceptions.JSONDecodeError`。

### 流式内容

对于大型响应，你可以在请求中使用带有 `stream=True` 的 `iter_content()` 方法，以避免一次性将整个内容加载到内存中。这对于下载大文件非常有用。

```python
# stream=True is required for this to work
r = requests.get('https://httpbin.org/stream/20', stream=True)

for chunk in r.iter_content(chunk_size=128):
    # Process each chunk as it's received
    print(chunk)
```

## 响应状态码

你可以检查响应的 HTTP 状态码，以了解请求是否成功。

```python
>>> r.status_code
200
```

Requests 提供了一个 `codes` 对象，用于将状态码与通用名称进行比较，这可以使你的代码更具可读性。

```python
>>> r.status_code == requests.codes.ok
True
```

### 检查错误

虽然你可以手动检查 `r.status_code`，但 Requests 提供了一种更简单的方法来检查请求是否成功。如果状态码小于 400，`Response` 对象的 `ok` 属性为 `True`，否则为 `False`。

```python
if r.ok:
    print('Request was successful')
else:
    print('Request failed')
```

或者，你也可以使用 `raise_for_status()` 方法。如果请求返回不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法将引发 `HTTPError`。

```python
try:
    bad_r = requests.get('https://httpbin.org/status/404')
    bad_r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(err)
```

这是一种便捷的方法，可以确保你的程序在请求失败时能够处理错误。

## 响应标头

响应标头可通过 `r.headers` 访问，它是一个不区分大小写的类字典对象。

```python
>>> r.headers
{'Content-Type': 'application/json; charset=utf-8', 'Server': 'gunicorn/19.9.0', ...}

# Accessing headers is case-insensitive
>>> r.headers['Content-Type']
'application/json; charset=utf-8'

>>> r.headers.get('content-type')
'application/json; charset=utf-8'
```

## Cookie

如果服务器发送了任何 Cookie，你可以通过 `r.cookies` 对象访问它们，该对象是一个 `RequestsCookieJar`。

```python
>>> url = 'https://httpbin.org/cookies/set/sessioncookie/123456789'
>>> r = requests.get(url)

>>> r.cookies['sessioncookie']
'123456789'
```

## 重定向和历史记录

Requests 会自动处理 HTTP 重定向。你收到的 `Response` 对象是跟踪所有重定向后的最终响应。

要查看导致最终响应的请求历史记录，你可以使用 `.history` 属性。它包含一个旧的 `Response` 对象列表，按从旧到新的顺序排列。

```python
>>> r = requests.get('http://github.com') # Note: http, not https

>>> r.url
'https://github.com/'

>>> r.status_code
200

>>> r.history
(<Response [301]>,)
```

在本例中，对 `http://github.com` 的原始请求导致了 301 Moved Permanently 重定向，该重定向存储在 `.history` 元组中。

---

既然你已经了解了如何检查和处理响应，接下来你可以通过使用[会话对象](./user-guide-session-objects.md)在多个请求之间持久化参数来提高效率。