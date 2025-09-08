# 处理响应

发出请求后，Requests 会返回一个包含服务器响应的 `Response` 对象。该对象持有您需要的所有信息，从状态码、标头到响应体本身。本指南将向您介绍检查和处理此响应数据的最常用方法。

有关发送请求的更多详情，请参阅上一节 [发送请求](./user-guide-making-a-request.md)。

## 检查状态码

收到响应后的第一步通常是检查请求是否成功。`status_code` 属性以整数形式提供 HTTP 状态码。

```python 检查状态码 icon=logos:python
r = requests.get('https://httpbin.org/status/200')
print(r.status_code)
# 200

if r.status_code == 200:
    print('Success!')
elif r.status_code == 404:
    print('Not Found.')
```

为了方便起见，Requests 提供了一个用于常见状态码的查找对象，这能让您的代码更具可读性。

```python 使用 codes 对象 icon=logos:python
import requests

r = requests.get('https://httpbin.org/status/200')
if r.status_code == requests.codes.ok: # .ok 是 200 的别名
    print('Request was successful.')
```

### 为错误的响应抛出异常

您可以使用 `raise_for_status()` 方法来代替手动检查状态码。如果 HTTP 请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法将抛出 `HTTPError` 异常。

```python 使用 raise_for_status() icon=logos:python
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

如果请求成功（状态码在 2xx 范围内），`raise_for_status()` 将不执行任何操作。`ok` 属性也为检查成功与否提供了一个简单的布尔值判断。

```python 使用 .ok 属性 icon=logos:python
r = requests.get('https://httpbin.org/status/200')
if r.ok:
    print("Request was successful!")
```

## 访问响应标头

响应标头通过 `headers` 属性以一个类字典对象的形式提供。其一个关键特性是标头键不区分大小写。

```python 访问标头 icon=logos:python
r = requests.get('https://httpbin.org/get')

# 访问标头
print(r.headers['Content-Type'])
# 'application/json'

# 不区分大小写
print(r.headers.get('content-type'))
# 'application/json'
```

## 访问响应体

根据响应的 `Content-Type`，您可以通过多种方式访问响应体。

### 原始二进制内容

对于非文本响应（如图片或 PDF 文件），您可以使用 `content` 属性访问响应体的原始字节。

以下是一个从 URL 保存图片的示例：

```python 保存二进制内容 icon=logos:python
import requests

# 此 URL 指向 Requests 库的徽标
r = requests.get('https://raw.githubusercontent.com/psf/requests/main/ext/requests-logo.png')

with open('requests_logo.png', 'wb') as f:
    f.write(r.content)

# 这会将 'requests_logo.png' 保存在您的当前目录中。
```
![Requests 库徽标](../../../ext/requests-logo.png)

### 文本内容

对于文本数据，`text` 属性以字符串形式提供响应体。Requests 会根据响应标头中指定的字符编码自动解码内容。如果未指定编码，它会尽力猜测。

```python 访问文本内容 icon=logos:python
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

```python 手动设置编码 icon=logos:python
r.encoding = 'utf-8'
print(r.text)
```

### JSON 内容

如果响应包含 JSON 数据，您可以使用内置的 `json()` 方法将其解析为 Python 字典或列表。

```python 解析 JSON 内容 icon=logos:python
r = requests.get('https://httpbin.org/json')

data = r.json()

# 从解析后的 JSON 中访问数据
slideshow_title = data['slideshow']['title']
print(f'Slideshow Title: {slideshow_title}')

# 控制台输出：
# Slideshow Title: Sample Slide Show
```

如果响应体不包含有效的 JSON，调用 `.json()` 将会抛出 `requests.exceptions.JSONDecodeError` 异常。

## 流式传输内容

对于非常大的响应，您可以在请求中设置 `stream=True`，以避免一次性将全部内容加载到内存中。然后，您可以使用 `iter_content()` 或 `iter_lines()` 来迭代响应内容。

### 分块流式传输

`iter_content()` 允许您按指定大小的块来迭代响应数据。这对于下载大文件非常有效，可以避免消耗过多内存。

```python 分块下载大文件 icon=logos:python
# 使用 iter_content 下载大文件
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status() # 确保请求成功
    with open('large_file.bin', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            # chunk 将是一个最大为 8192 字节的 bytes 对象
            f.write(chunk)
```

### 按行流式传输

`iter_lines()` 对于处理基于文本的流式 API 非常有用，因为它会逐行迭代响应内容。

```python 逐行处理文本流 icon=logos:python
with requests.get('https://httpbin.org/stream/5', stream=True) as r:
    for line in r.iter_lines():
        if line:
            # line 将是一个 bytes 对象，解码后才能打印
            decoded_line = line.decode('utf-8')
            print(decoded_line)
```
这种方法可以高效地处理大文件下载或数据流，而不会占用大量内存。

---

既然您已经了解了如何处理响应，就可以通过使用 [会话对象](./user-guide-session-objects.md) 来提升请求的效率和状态管理。