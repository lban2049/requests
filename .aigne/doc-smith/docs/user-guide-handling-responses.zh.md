# 处理响应

在您使用 `requests.get()` 等方法发出请求后，会收到一个 `Response` 对象。该对象包含了服务器返回的所有信息，学习如何处理它是继[发出请求](./user-guide-making-a-request.md)之后的合理步骤。

让我们先向 GitHub API 发出一个简单的请求：

```python
import requests

r = requests.get('https://api.github.com/events')
```

现在，我们有了一个名为 `r` 的 `Response` 对象。我们可以开始检查它的属性和方法，以获取所需的数据。

## 访问响应内容

Requests 提供了多种访问响应体的方式，具体取决于内容类型。

### 二进制响应内容

您可以使用 `r.content` 属性访问响应的原始字节。这对于图片、音频或其他文件等非文本内容非常有用。

```python
# 这将打印响应的原始字节
print(r.content)
# b'[{"id":"35003554473","type":"PushEvent","actor":{"id":...}]'
```

Requests 会自动为您解压 `gzip` 和 `deflate` 传输编码。在下载压缩文件时，您可以看到这一过程。

### 文本响应内容

对于基于文本的响应，`r.text` 属性以字符串形式提供内容。Requests 会自动解码 `r.content` 来创建 `r.text`。

它是如何确定编码的？
1.  它会检查 HTTP 标头中是否有编码（在 `Content-Type` 标头中）。
2.  如果标头中未指定编码，它会回退到使用 `chardet` 等库来猜测编码。

```python
print(r.text)
# '[{"id":"35003554473","type":"PushEvent","actor":{"id":...}]'
```

您可以查看 Requests 正在使用的编码，甚至可以在访问 `r.text` 之前更改它：

```python
print(r.encoding)  # utf-8

r.encoding = 'ISO-8859-1'
# 现在将使用新的编码对文本进行解码
print(r.text)
```

### JSON 响应内容

如果您正在处理 JSON API，可以使用内置的 `r.json()` 方法。该方法会将响应文本解析为 JSON，并返回一个 Python 字典或列表。

```python
r = requests.get('https://api.github.com/events')
json_response = r.json()

# 现在您可以像处理常规 Python 对象一样处理它
print(json_response[0]['type']) # 例如 'PushEvent'
```

如果响应不包含有效的 JSON，调用 `r.json()` 将会引发 `requests.exceptions.JSONDecodeError`。

```python
import requests

r_html = requests.get('https://github.com')
try:
    r_html.json()
except requests.exceptions.JSONDecodeError:
    print("响应无法解码为 JSON。")
```

## 检查响应元数据

除了内容之外，`Response` 对象还包含有关服务器回复的有用信息。

### 状态码

您可以使用 `status_code` 属性检查响应的 HTTP 状态码。

```python
print(r.status_code) # 200
```

为了提高可读性，Requests 提供了一个查找对象 `requests.codes`，其中按名称包含了常见的状态码。

```python
if r.status_code == requests.codes.ok: # .ok 是 200 的别名
    print("请求成功！")
else:
    print(f"请求失败，状态码为 {r.status_code}")
```

### 响应标头

响应标头可通过 `r.headers` 以 Python 字典的形式获取。标头键不区分大小写。

```python
print(r.headers)
# {'content-type': 'application/json; charset=utf-8', 'cache-control': 'public, max-age=60, s-maxage=60', ...}

print(r.headers['Content-Type'])
# 'application/json; charset=utf-8'

# 访问不区分大小写
print(r.headers.get('content-type'))
# 'application/json; charset=utf-8'
```

### Cookies

如果响应包含任何 Cookies，您可以使用 `r.cookies` 访问它们。

```python
r_with_cookies = requests.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
print(r_with_cookies.cookies['sessioncookie'])
# '123456789'
```

## 检查错误

Requests 简化了检查请求是否成功的流程。

### ok 属性

如果 `status_code` 小于 400（即不是客户端或服务器错误），`ok` 属性将返回 `True`。这提供了一个简单的布尔值来检查请求是否成功。

```python
r_success = requests.get('https://httpbin.org/status/200')
if r_success.ok:
    print("成功！") # 将会打印

r_fail = requests.get('https://httpbin.org/status/404')
if not r_fail.ok:
    print("失败！") # 将会打印
```

### raise_for_status() 方法

如果需要更直接的方式，您可以使用 `raise_for_status()` 方法。如果 HTTP 请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法将引发 `HTTPError`。

```python
from requests.exceptions import HTTPError

bad_r = requests.get('https://httpbin.org/status/404')

try:
    bad_r.raise_for_status()
except HTTPError as http_err:
    print(f'发生 HTTP 错误: {http_err}') # 发生 HTTP 错误: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

成功的请求不会引发异常：
```python
good_r = requests.get('https://httpbin.org/status/200')
good_r.raise_for_status() # 不执行任何操作
```

在应用程序的控制流中，此方法是断言请求成功的一种便捷方式。

## 重定向与历史记录

默认情况下，除了 HEAD 请求外，Requests 会自动对所有请求动词执行重定向。您可以检查导致最终响应的重定向历史记录。

`history` 属性包含一个 `Response` 对象列表，这些对象是为了完成请求而创建的。该列表按从最旧到最新的响应排序。

```python
r = requests.get('http://github.com') # 这将重定向到 https://github.com

print(r.url)
# 'https://github.com/'

print(r.status_code)
# 200

print(r.history)
# [<Response [301]>]
```

301 重定向的原始 `Response` 对象存储在 `history` 列表中。

## 流式内容

对于大型响应，最好避免一次性将全部内容读入内存。您可以通过在请求中设置 `stream=True` 来实现这一点。

### 迭代内容

当 `stream=True` 时，您可以使用 `iter_content()` 方法来迭代响应数据。这对于下载大文件非常理想。

```python
# 在此示例中，我们下载一个大文件并分块保存到磁盘
url = 'https://files.pythonhosted.org/packages/source/r/requests/requests-2.31.0.tar.gz'
with requests.get(url, stream=True) as r:
    r.raise_for_status()
    with open('requests.tar.gz', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

`chunk_size` 参数控制每次读入内存的字节数。

### 逐行迭代

对于返回行分隔数据（如文本或 JSON 行）的流式 API，您可以使用 `iter_lines()` 方法。

```python
r = requests.get('https://httpbin.org/stream/20', stream=True)

for line in r.iter_lines():
    if line:
        decoded_line = line.decode('utf-8')
        print(decoded_line)
```

该方法会为您处理行尾符并解码内容，使得逐行处理变得容易。

---

现在您已经了解了如何检查和处理响应，可以构建更稳健的应用程序。下一步是学习如何在多个请求之间持久化状态。为此，请继续阅读[会话对象](./user-guide-session-objects.md)。