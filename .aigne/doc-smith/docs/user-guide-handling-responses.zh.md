# 处理响应

发出请求后，`requests` 会返回一个 `Response` 对象。该对象包含服务器对 HTTP 请求的响应，包括内容、状态码和标头。本指南将引导你如何访问和使用这些信息。

有关如何创建初始请求的详细信息，请参阅[发出请求](./user-guide-making-a-request.md)指南。

## 响应内容

`requests` 简化了以各种格式访问响应正文的过程。

### 二进制响应内容

你可以使用 `content` 属性访问响应正文的原始字节。这对于非文本内容非常有用，例如图片、PDF 或其他文件。`requests` 会自动为你解码 `gzip` 和 `deflate` 传输编码。

**示例：保存图片**
```python
import requests

r = requests.get('https://httpbin.org/image/png')

# r.content returns the image data in bytes.
with open('example.png', 'wb') as f:
    f.write(r.content)
```
这段代码获取一个 PNG 图片，并以二进制写入模式将其保存到名为 `example.png` 的文件中。

### 文本响应内容

对于文本数据，`text` 属性以标准 Python 字符串的形式提供响应内容。`requests` 会尝试从响应的 HTTP 标头中确定字符编码。如果服务器未指定编码，`requests` 会使用像 `chardet` 这样的外部库来估算。

你可以找出 `requests` 正在使用哪种编码：

```python
import requests

r = requests.get('https://httpbin.org/html')
print(r.encoding)
# Output: utf-8

print(r.text)
# Output: The HTML content as a string
```

如果你需要覆盖检测到的编码，可以在访问 `.text` 之前手动设置 `encoding` 属性：

```python
r.encoding = 'ISO-8859-1'
```

### JSON 响应内容

如果响应包含 JSON 数据，你可以使用内置的 `json()` 方法。该方法会解析内容并返回一个 Python 字典或列表。

```python
import requests
from requests.exceptions import JSONDecodeError

r = requests.get('https://httpbin.org/json')
try:
    data = r.json()
    print(data['slideshow']['title'])
except JSONDecodeError:
    print("Response could not be decoded as JSON.")
except KeyError:
    print("JSON does not contain expected keys.")

```
如果响应正文不包含有效的 JSON，调用 `.json()` 将会引发 `requests.exceptions.JSONDecodeError`。

### 原始响应流

对于需要处理大型响应而又不想将其全部加载到内存的高级场景，你可以使用原始流。要启用此功能，请在初始请求中设置 `stream=True`。这样你就可以访问原始响应并对其进行迭代。

```python
import requests

r = requests.get('https://httpbin.org/stream/20', stream=True)

# Set encoding if not provided by the server
if r.encoding is None:
    r.encoding = 'utf-8'

# iter_lines processes the stream line by line
for line in r.iter_lines(decode_unicode=True):
    if line:
        print(line)
```

## 响应状态

收到响应后，第一步通常是检查其状态，看请求是否成功。

```d2
direction: down

response: "Receive Response object"
check_status: "Check status (r.ok, r.raise_for_status())"
is_ok: "Successful (2xx)?"
process: "Process content (r.json(), r.text)"
handle_error: "Handle error (4xx/5xx)"
done: "Done"

response -> check_status
check_status -> is_ok
is_ok -> process: Yes
is_ok -> handle_error: No
process -> done
handle_error -> done
```

### 状态码

`status_code` 属性以整数形式提供 HTTP 状态码。

```python
import requests

r = requests.get('https://httpbin.org/status/404')
print(r.status_code)
# Output: 404
```

`requests` 还包含一个状态码查询对象 `requests.codes`，通过使用描述性名称而非数字，可以使你的代码更具可读性。

```python
if r.status_code == requests.codes.not_found:
    print('The requested resource was not found.')
```

### 检查错误

除了手动检查状态码，你还可以使用 `raise_for_status()` 方法。如果请求返回了不成功的状态码（4xx 客户端错误或 5xx 服务器错误），该方法会引发 `HTTPError`。

```python
import requests
from requests.exceptions import HTTPError

for status in [200, 404, 500]:
    try:
        url = f'https://httpbin.org/status/{status}'
        r = requests.get(url)
        r.raise_for_status()
    except HTTPError as http_err:
        print(f'HTTP error for status {status}: {http_err}')
    else:
        print(f'Success for status {status}!')
```

`Response` 对象还有一个布尔值属性 `ok`，如果状态码小于 400，该属性为 `True`。

```python
r = requests.get('https://httpbin.org/get')
if r.ok:  # or simply `if r:`
    print('Request was successful.')
else:
    print('Request failed.')
```

## 响应标头

响应标头可通过 `headers` 属性获取。该属性是一个类似字典的对象，其键不区分大小写。

```python
import requests

r = requests.get('https://httpbin.org/get')

print(r.headers)
# Output: A CaseInsensitiveDict object of headers

# Access is case-insensitive
print(r.headers['Content-Type'])
# Output: 'application/json'

print(r.headers.get('content-type'))
# Output: 'application/json'
```

## Cookie

如果服务器发送了任何 Cookie，你可以通过 `cookies` 属性访问它们，该属性是一个 `RequestsCookieJar` 对象。

```python
import requests

r = requests.get('https://httpbin.org/cookies/set?name=mycookie&value=12345')

print(r.cookies['mycookie'])
# Output: '12345'
```

## 重定向与历史记录

默认情况下，`requests` 会自动处理重定向。你收到的 `Response` 对象是所有重定向发生后的最终响应。你可以通过 `history` 属性访问导致最终目标的请求历史记录。它包含一个 `Response` 对象列表，按从旧到新的顺序排列。

```python
import requests

r = requests.get('https://httpbin.org/redirect/3')

print(f"Final URL: {r.url}")
print(f"Final Status Code: {r.status_code}")

print("Request History:")
for resp in r.history:
    print(f"  - {resp.status_code} from {resp.url}")

# You can also check if the response was a permanent redirect
if r.is_permanent_redirect:
    print("This was a permanent redirect.")
```

## 后续步骤

现在你已经了解如何处理响应，可以学习如何在多个请求之间持久化参数和 Cookie，以提高性能和进行状态管理。

<x-card data-title="Session Objects" data-href="/user-guide/session-objects" data-icon="lucide:book-copy" data-cta="Continue Reading">
  利用 Session 对象在多个请求之间持久化参数、Cookie 和标头。
</x-card>

这种实践是构建高效且有状态的应用程序的关键。