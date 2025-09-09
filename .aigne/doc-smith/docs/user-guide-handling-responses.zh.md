# 处理响应

发出请求后，Requests 库会返回一个 `Response` 对象。该对象包含服务器的响应，包括内容、状态码、标头等。接下来我们探讨如何高效地使用此对象。

首先，我们发出一个请求，以便在示例中使用：

```python Making a Request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

## 响应内容

你可以根据内容类型，通过多种方式访问响应正文。

### 二进制响应内容

对于非文本请求，你可以使用 `content` 属性以字节形式访问响应正文。Requests 会自动为你解码 `gzip` 和 `deflate` 传输编码。

这对于处理图像或其他文件等数据非常有用。

```python Getting Binary Content icon=logos:python
# r.content 返回字节
print(r.content[:100]) # 打印前 100 个字节

# 示例：保存图像
# from PIL import Image
# from io import BytesIO
# image_response = requests.get('https://via.placeholder.com/150')
# try:
#     i = Image.open(BytesIO(image_response.content))
#     i.save('placeholder.png')
#     print("图像已保存为 placeholder.png")
# except Exception as e:
#     print(f"无法处理图像：{e}")
```

### 文本响应内容

对于基于文本的响应，`text` 属性以字符串形式提供内容。Requests 会自动根据服务器的响应解码内容。

Requests 会根据 HTTP 标头对编码进行智能猜测。如果在 `Content-Type` 标头中找不到 `charset`，它将尝试使用 `chardet` 库来猜测编码。你可以通过以下方式查看 Requests 正在使用的编码：

```python Checking the Encoding icon=logos:python
print(f"Detected encoding: {r.encoding}")
# 输出: Detected encoding: utf-8
```

如果需要覆盖检测到的编码，可以在访问 `.text` 之前手动设置 `encoding` 属性：

```python Setting the Encoding icon=logos:python
r.encoding = 'ISO-8859-1'
print(r.text)
```

### JSON 响应内容

如果响应包含 JSON 数据，你可以使用内置的 `json()` 方法将其解析为 Python 字典或列表。这在处理 API 时非常方便。

```python Parsing JSON icon=logos:python
json_response = r.json()
print(type(json_response)) # <class 'list'>
print(json_response[0]['type']) # 像普通 Python 对象一样访问数据
```

如果响应不包含有效的 JSON，调用 `.json()` 将会引发 `requests.exceptions.JSONDecodeError` 异常。

### 流式内容

对于非常大的响应，可以通过使用 `iter_content` 避免一次性将全部内容加载到内存中。这需要通过在初始请求中设置 `stream=True` 来实现。

```python Streaming Large Files icon=logos:python
with requests.get('https://httpbin.org/stream/20', stream=True) as r:
    for chunk in r.iter_content(chunk_size=128):
        if chunk:
            print(chunk)
```

你也可以使用 `iter_lines` 逐行遍历响应。

## 检查响应

除了内容之外，`Response` 对象还提供了用于检查的实用属性。

### 状态码

你可以使用 `status_code` 属性检查响应的 HTTP 状态码。

```python Checking the Status Code icon=logos:python
print(r.status_code)
# 输出: 200
```

为了提高可读性，Requests 为常用状态码提供了一个查找对象：

```python Using the Codes Object icon=logos:python
if r.status_code == requests.codes.ok:
    print("Request was successful!")
else:
    print(f"Request failed with status code: {r.status_code}")
```

### 响应标头

响应标头以一个不区分大小写的类字典对象的形式提供。

```python Accessing Headers icon=logos:python
print(r.headers)
# 访问特定的标头
print(f"Content-Type: {r.headers['Content-Type']}")
# 不区分大小写的实际应用
print(f"content-type: {r.headers.get('content-type')}")
```

### Cookie

如果响应包含任何 Cookie，你可以通过 `cookies` 属性访问它们，该属性会返回一个 `CookieJar` 对象。

```python Working with Cookies icon=logos:python
cookie_r = requests.get('https://httpbin.org/cookies/set?my_cookie=12345')
print(cookie_r.cookies['my_cookie'])
# 输出: 12345
```

## 错误处理

Requests 可以轻松地检查请求是否成功，或在失败时引发异常。

### `ok` 属性

检查请求是否成功的一个简单方法是使用布尔属性 `ok`。如果 `status_code` 小于 400（即，不是客户端或服务器错误），则该属性返回 `True`。

```python Using the ok Property icon=logos:python
if r.ok:
    print("Request is OK")
else:
    print("Request failed")
```

### 针对错误引发异常

要获得更明确的失败信号，可以使用 `raise_for_status()` 方法。如果请求导致客户端错误（4xx 状态码）或服务器错误（5xx 状态码），该方法将引发 `HTTPError`。

```python Raising Exceptions icon=logos:python
error_r = requests.get('https://httpbin.org/status/404')
print(f"Status Code: {error_r.status_code}")

try:
    error_r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"HTTP Error Occurred: {err}")
```

如果请求成功，`raise_for_status()` 不会执行任何操作。

## 重定向与历史记录

默认情况下，Requests 会自动执行重定向。`Response` 对象的 `history` 属性包含一个 `Response` 对象列表，这些对象是在完成请求过程中创建的。该列表按从最旧到最新的响应顺序排列。

例如，GitHub 会将所有 HTTP 请求重定向到 HTTPS：

```python Inspecting Redirect History icon=logos:python
redir_r = requests.get('http://github.com')

print(f"Final URL: {redir_r.url}")
print(f"Final Status Code: {redir_r.status_code}")

# 检查历史记录
print(f"History: {redir_r.history}")

# 历史记录中的第一个响应是最初的 301 重定向
if redir_r.history:
    original_response = redir_r.history[0]
    print(f"Original Status Code: {original_response.status_code}")
    print(f"Original URL: {original_response.url}")
```

既然你已经熟悉了如何处理响应，下一步就是管理跨多个请求的状态。通过 [Session Objects](./user-guide-session-objects.md) 学习如何持久化 Cookie 和标头。