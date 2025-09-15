# 处理响应

发出请求后，Requests 会返回一个 `Response` 对象，其中包含服务器的响应。该对象包含你需要的所有信息，从页面内容到状态码和标头等元数据。

```python 发出一个简单的请求 icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

现在我们有了 `r` 对象，接下来我们来研究如何检查其内容。

## 响应内容

Requests 提供了多种访问响应正文的方式，具体取决于内容类型。

### 文本内容

对于基于文本的响应（例如 HTML 或纯文本），你可以使用 `text` 属性。Requests 会自动解码来自服务器响应的内容。

```python 查看文本响应 icon=logos:python
r = requests.get('https://api.github.com/events')
print(r.text)
# '[{"id":"34343116709","type":"PushEvent","actor":{"id":...'
```

Requests 会根据 HTTP 标头对编码进行智能猜测。如果需要覆盖此设置，可以在访问 `.text` 之前手动设置 `encoding` 属性：

```python 手动设置编码 icon=logos:python
r.encoding = 'utf-8'
print(r.text)
```

### 二进制响应内容

对于非文本内容（例如图片或 PDF 文件），你可以使用 `content` 属性访问响应的原始字节。这是下载文件的最佳方法，因为它可以避免任何解码问题。

```python 获取图片 icon=logos:python
r = requests.get('https://httpbin.org/image/png')
print(r.content)
# b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...'

# 你可以将其保存到文件中
with open('image.png', 'wb') as f:
    f.write(r.content)
```

### JSON 响应内容

如果你正在使用的 API 返回 JSON，Requests 有一个内置的 JSON 解码器。只需调用 `json()` 方法即可将响应内容解析为 Python 字典或列表。

```python 解码 JSON icon=logos:python
r = requests.get('https://api.github.com/events')
data = r.json()

# 像访问普通 Python 对象一样访问数据
first_event_type = data[0]['type']
print(first_event_type)
# 'PushEvent'
```

如果响应不包含有效的 JSON，调用 `.json()` 将会引发 `requests.exceptions.JSONDecodeError`。

### 流式内容

对于非常大的响应，你可以避免一次性将全部内容加载到内存中。通过在请求中设置 `stream=True`，你可以迭代处理到达的响应数据。

使用 `iter_content()` 方法来控制块大小。这对于下载大文件非常理想。

```python 保存大文件 icon=logos:python
# 以 8KB 的块大小下载一个 100KB 的文件
with requests.get('https://httpbin.org/stream-bytes/102400', stream=True) as r:
    r.raise_for_status() # 确保请求成功
    with open('large_file.bin', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

你还可以使用 `iter_lines()` 逐行迭代响应。

## 响应状态码

检查响应的状态码以验证请求是否成功至关重要。

<x-field data-name="status_code" data-type="integer" data-desc="响应的 HTTP 状态码（例如，200、404）。"></x-field>
<x-field data-name="reason" data-type="string" data-desc="状态的文本原因（例如，'OK'、'Not Found'）。"></x-field>
<x-field data-name="ok" data-type="boolean" data-desc="如果状态码小于 400 则返回 True，否则返回 False。这是检查成功与否的简单方法。"></x-field>

```python 状态码示例 icon=logos:python
r = requests.get('https://httpbin.org/status/404')
print(r.status_code)
# 404

print(r.reason)
# 'NOT FOUND'

if not r.ok:
    print("Request failed!")
```

Requests 还提供了一个方便的查找对象 `requests.codes`，用于将状态码与人类可读的名称进行比较。

| 代码 | `requests.codes` 属性 |
|---|---|
| 200 | `ok`, `okay`, `all_ok` |
| 301 | `moved_permanently`, `moved` |
| 302 | `found` |
| 400 | `bad_request`, `bad` |
| 401 | `unauthorized` |
| 403 | `forbidden` |
| 404 | `not_found` |
| 500 | `internal_server_error` |

### 对错误的响应抛出异常

除了手动检查 `r.ok`，你还可以使用 `raise_for_status()` 方法。如果请求返回不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法将引发 `HTTPError`。

```python 使用 raise_for_status icon=logos:python
try:
    r = requests.get('https://httpbin.org/status/404')
    r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"HTTP error occurred: {err}")

# 成功的请求不会引发异常
r = requests.get('https://httpbin.org/status/200')
r.raise_for_status()
print("Request was successful!")
```

## 响应标头

响应标头可通过 `r.headers` 以类字典对象的形式获取。该标头字典很特殊：它是大小写不敏感的。

```python 访问标头 icon=logos:python
r = requests.get('https://httpbin.org/get')

# 访问时大小写不敏感
content_type_1 = r.headers['Content-Type']
content_type_2 = r.headers.get('content-type')

print(content_type_1)
# 'application/json'
print(content_type_2)
# 'application/json'
```

## Cookies

如果服务器发送了任何 cookie，你可以通过 `r.cookies` 属性访问它们，该属性是一个行为类似字典的 `CookieJar` 对象。

```python 使用 Cookies icon=logos:python
r = requests.get('https://httpbin.org/cookies/set/flavor/chocolatechip')
cookie_value = r.cookies['flavor']

print(cookie_value)
# 'chocolatechip'
```

## 重定向和历史

Requests 会自动处理 HTTP 重定向。`Response` 对象的 `history` 属性包含一个旧的 `Response` 对象列表，这些对象是重定向链的一部分。该列表按从最旧到最新的响应排序。

```python 重定向历史 icon=logos:python
r = requests.get('https://github.com')

print(f"Final URL: {r.url}")
# 最终 URL: https://github.com/

print(f"Status Code: {r.status_code}")
# 状态码: 200

# 让我们尝试一个会重定向的 URL
r_redirect = requests.get('http://github.com') # 注意：是 http，不是 https

print(f"Final URL after redirect: {r_redirect.url}")
# 重定向后的最终 URL: https://github.com/

print("Redirect History:")
for resp in r_redirect.history:
    print(f"- {resp.status_code}: {resp.url}")
# 重定向历史：
# - 301: http://github.com/
```

---

现在你已经可以自信地处理响应了，接下来我们来探讨如何使用 [Session 对象](./user-guide-session-objects.md) 来管理状态并提高跨多个请求的性能。
