# 处理响应

当你发出请求时，Requests 会返回一个 `Response` 对象。该对象包含了服务器的响应，包括内容、状态码、标头、Cookie 等。本指南将介绍如何访问和处理这些数据。

```python
import requests

r = requests.get('https://api.github.com/events')
```

## 响应内容

Requests 可以处理不同类型的响应内容，并在可能的情况下自动为你解码。

### 文本内容

对于基于文本的响应（例如 HTML 或纯文本），你可以使用 `.text` 属性以字符串形式访问其内容。Requests 会根据服务器的响应自动对内容进行解码。

```python
>>> r.text
'[{"id":"1234567890","type":"PushEvent","actor":{...}}]'
```

Requests 会根据 HTTP 标头猜测字符编码。如果需要覆盖此设置，可以在访问 `.text` 之前手动设置 `.encoding` 属性。

```python
>>> r.encoding
'utf-8'
>>> r.encoding = 'ISO-8859-1'
```

### 二进制内容

对于图片或 PDF 文件等非文本内容，你可以使用 `.content` 属性以字节形式访问响应正文。这会返回响应的原始字节数据，以便你将其保存到文件中。

以下是保存图片的示例：

```python
img_response = requests.get('https://raw.githubusercontent.com/psf/requests/main/docs/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(img_response.content)
```

![requests-logo](../../../ext/requests-logo.png)

### JSON 内容

许多 Web API 以 JSON 格式返回数据。Requests 内置了 JSON 解码器 `.json()`，它会解析响应内容并返回一个 Python 字典或列表。

```python
import requests

r = requests.get('https://api.github.com/events')
json_data = r.json()

# Access a value from the parsed JSON
print(json_data[0]['type'])
```

如果响应不包含有效的 JSON，调用 `.json()` 将会引发 `requests.exceptions.JSONDecodeError` 异常。

### 流式内容

对于大型响应，你可以在请求中设置 `stream=True` 并使用 `iter_content()` 方法，从而避免一次性将全部内容加载到内存中。这对于下载大文件非常有用。

```python
# 必须设置 stream=True 才能生效
r = requests.get('https://httpbin.org/stream/20', stream=True)

for chunk in r.iter_content(chunk_size=128):
    # 在接收到每个数据块时进行处理
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

另外，你还可以使用 `raise_for_status()` 方法。如果请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法会引发一个 `HTTPError`。

```python
try:
    bad_r = requests.get('https://httpbin.org/status/404')
    bad_r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(err)
```

这是一种确保程序在请求失败时能处理错误的便捷方法。

## 响应标头

响应标头可通过 `r.headers` 访问，它是一个不区分大小写的类字典对象。

```python
>>> r.headers
{'Content-Type': 'application/json; charset=utf-8', 'Server': 'gunicorn/19.9.0', ...}

# 访问标头时不区分大小写
>>> r.headers['Content-Type']
'application/json; charset=utf-8'

>>> r.headers.get('content-type')
'application/json; charset=utf-8'
```

## Cookie

如果服务器发送了任何 Cookie，你可以通过 `r.cookies` 对象（一个 `RequestsCookieJar`）来访问它们。

```python
>>> url = 'https://httpbin.org/cookies/set/sessioncookie/123456789'
>>> r = requests.get(url)

>>> r.cookies['sessioncookie']
'123456789'
```

## 重定向与历史记录

Requests 会自动处理 HTTP 重定向。你收到的 `Response` 对象是跟踪所有重定向后的最终响应。

要查看导致最终响应的请求历史记录，可以使用 `.history` 属性。它包含一个从最旧到最新的旧 `Response` 对象列表。

```python
>>> r = requests.get('http://github.com') # 注意：是 http，不是 https

>>> r.url
'https://github.com/'

>>> r.status_code
200

>>> r.history
(<Response [301]>,)
```

在本例中，对 `http://github.com` 的原始请求导致了一个 301 Moved Permanently（永久移动）重定向。下图说明了此流程。

```d2
shape: sequence_diagram

Client: "你的应用程序"
Server: "GitHub 服务器"

Client -> Server: "GET http://github.com"
Server -> Client: "301 Moved Permanently\nLocation: https://github.com"
Client -> Server: "GET https://github.com"
Server -> Client: "200 OK"

note over Client, Server: "'301' 响应存储在 `r.history` 属性中。"
```

---

既然你已经了解了如何检查和处理响应，接下来可以通过在多个请求之间持久化参数来提高效率。

<x-card data-title="下一步：会话对象" data-icon="lucide:book-copy" data-href="/user-guide/session-objects" data-cta="阅读更多">
  学习如何使用会话对象在多个请求之间持久化参数和 Cookie，以提高性能。
</x-card>