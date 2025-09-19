# 请求和响应对象

Requests 库的核心是代表 HTTP 对话双方的对象：你发送的请求和你接收的响应。本参考详细介绍了 `Request`、`PreparedRequest` 和 `Response` 对象，阐述了它们的属性和方法，以便对 HTTP 交互进行高级控制。

## Request 对象

`Request` 对象是一个由用户创建的对象，它捕获了你*打算*发出的请求的所有信息。它是一个数据容器，在实际准备传输之前，可以四处传递。这对于在请求通过网络发送前，以结构化的方式对请求进行排队或修改非常有用。

### 创建 Request

你可以直接实例化一个 `Request` 对象，并提供 HTTP 请求所需的所有详细信息。

```python Request 对象初始化 icon=logos:python
import requests

req = requests.Request(
    'POST',
    'https://httpbin.org/post',
    headers={'X-My-Header': 'true'},
    files={'report.xls': open('report.xls', 'rb')},
    data={'foo': 'bar'}
)
```

### 参数

<x-field-group>
  <x-field data-name="method" data-type="string" data-required="false" data-desc="要使用的 HTTP 方法（例如 'GET'、'POST'、'PUT'）。"></x-field>
  <x-field data-name="url" data-type="string" data-required="false" data-desc="请求发送的目标 URL。"></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="要发送的 HTTP 标头字典。"></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="用于多部分文件上传的 `{'filename': file-like-object}` 字典。"></x-field>
  <x-field data-name="data" data-type="dict, list[tuple], bytes, or file-like" data-required="false" data-desc="要附加到请求中的正文。如果提供字典，它将被表单编码。"></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="要在请求正文中发送的可 JSON 序列化的 Python 对象。"></x-field>
  <x-field data-name="params" data-type="dict or list[tuple]" data-required="false" data-desc="要附加到 URL 查询字符串的 URL 参数。"></x-field>
  <x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="一个身份验证处理器或用于基本身份验证的 `(user, pass)` 元组。"></x-field>
  <x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="要附加到请求中的 cookie 字典或 CookieJar。"></x-field>
  <x-field data-name="hooks" data-type="dict" data-required="false" data-desc="供内部使用的回调钩子字典。"></x-field>
</x-field-group>

### 方法

#### `prepare()`

`Request` 对象的主要方法。它获取请求的数据并准备传输，返回一个 `PreparedRequest` 对象。

```python 准备请求 icon=logos:python
prepared_request = req.prepare()

print(prepared_request.headers)
print(prepared_request.body)
```

## PreparedRequest 对象

`PreparedRequest` 是调用 `request.prepare()` 的结果。该对象经过完全处理，包含了将要发送到服务器的确切字节。通常情况下，你不需要手动创建 `PreparedRequest`。`Session` 对象会在发送请求前在内部创建它。

### 属性

<x-field-group>
  <x-field data-name="method" data-type="string" data-desc="要发送的 HTTP 动词，规范化为大写。"></x-field>
  <x-field data-name="url" data-type="string" data-desc="完全准备好的 URL，包括已编码的参数。"></x-field>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="包含所有请求标头的字典，包括在准备过程中添加的标头（例如 `Content-Type`、`Content-Length`、`Cookie`）。"></x-field>
  <x-field data-name="body" data-type="bytes, string, or file-like" data-desc="请求正文，已编码并准备好传输。"></x-field>
  <x-field data-name="_cookies" data-type="CookieJar" data-desc="用于生成 Cookie 标头的 CookieJar。"></x-field>
</x-field-group>

### 发送 PreparedRequest

一旦有了 `PreparedRequest`，就可以使用 `Session` 对象发送它。这让你能对请求生命周期进行精细控制。

```python 发送 PreparedRequest icon=logos:python
import requests

req = requests.Request('GET', 'https://httpbin.org/get', params={'key': 'value'})
s = requests.Session()

# 准备请求
prepped = s.prepare_request(req) # Session 也可以直接准备它

# prepped 现在是一个 PreparedRequest 对象
print(f"正在发送 {prepped.method} 到 {prepped.url}")

# 发送它
response = s.send(prepped)

print(f"收到响应: {response.status_code}")
```

## Response 对象

任何发送请求的方法（如 `requests.get()` 或 `session.send()`）都会返回一个 `Response` 对象。它包含了服务器对你的 HTTP 请求的响应。

### 属性

<x-field-group>
  <x-field data-name="status_code" data-type="int" data-desc="整数类型的 HTTP 状态码（例如 200、404）。"></x-field>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="不区分大小写的响应标头字典。"></x-field>
  <x-field data-name="encoding" data-type="string" data-desc="用于解码 `r.text` 的编码。可以手动设置。"></x-field>
  <x-field data-name="url" data-type="string" data-desc="响应的最终 URL 地址，在任何重定向之后。"></x-field>
  <x-field data-name="history" data-type="list[Response]" data-desc="请求历史（重定向）中的 `Response` 对象列表。"></x-field>
  <x-field data-name="reason" data-type="string" data-desc="HTTP 状态的文本原因（例如 'OK'、'Not Found'）。"></x-field>
  <x-field data-name="cookies" data-type="CookieJar" data-desc="服务器返回的 cookie 的 CookieJar。"></x-field>
  <x-field data-name="elapsed" data-type="timedelta" data-desc="从发送请求到响应标头到达所经过的时间。"></x-field>
  <x-field data-name="request" data-type="PreparedRequest" data-desc="此响应所对应的 `PreparedRequest` 对象。"></x-field>
  <x-field data-name="raw" data-type="urllib3.response.HTTPResponse" data-desc="来自 urllib3 的底层原始响应对象。需要在请求中设置 `stream=True`。"></x-field>
</x-field-group>

### 内容访问

<x-cards>
  <x-card data-title=".content" data-icon="lucide:file-text">
    以字节形式返回响应正文。适用于图像或文件等非文本内容。
  </x-card>
  <x-card data-title=".text" data-icon="lucide:text">
    以字符串形式返回响应正文，使用确定的编码进行解码。
  </x-card>
  <x-card data-title=".json(**kwargs)" data-icon="lucide:braces">
    将 JSON 格式的响应正文反序列化为 Python 对象。如果正文不是有效的 JSON，则会引发异常。
  </x-card>
</x-cards>

```python 访问响应内容 icon=logos:python
import requests

r = requests.get('https://api.github.com/events')

# 以字节形式访问
byte_content = r.content

# 以字符串形式访问
text_content = r.text

# 以 JSON 形式访问
json_content = r.json()

print(f"第一个事件类型: {json_content[0]['type']}")
```

### 状态和错误处理

<x-cards>
  <x-card data-title=".ok" data-icon="lucide:check-circle">
    一个布尔属性，如果状态码小于 400，则为 `True`，否则为 `False`。
  </x-card>
  <x-card data-title=".raise_for_status()" data-icon="lucide:shield-alert">
    一个方法，如果请求返回了不成功的状态码（4xx 或 5xx），则会引发 `HTTPError`。
  </x-card>
</x-cards>

```python 检查响应状态 icon=logos:python
import requests

r = requests.get('https://httpbin.org/status/404')

if r.ok:
    print("请求成功！")
else:
    print(f"请求失败，状态码: {r.status_code}")

try:
    r.raise_for_status()
except requests.exceptions.HTTPError as err:
    print(f"发生了一个 HTTP 错误: {err}")

# 输出:
# 请求失败，状态码: 404
# 发生了一个 HTTP 错误: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

### 流式内容

对于大型响应，可以流式传输内容以避免一次性将其全部加载到内存中。

- **`iter_content(chunk_size=1, decode_unicode=False)`**：以块的形式迭代响应数据。
- **`iter_lines(chunk_size=512, decode_unicode=False)`**：逐行迭代响应数据。

```python 流式传输大文件 icon=logos:python
import requests

# 使用 stream=True 启用流式传输
with requests.get('https://httpbin.org/stream/10', stream=True) as r:
    r.raise_for_status()
    with open('streamed_data.txt', 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
```

## 状态码查询

Requests 提供了一个方便的查询对象，用于通过通用名称引用 HTTP 状态码，这在检查 `response.status_code` 属性时非常有用。

```python 使用状态码常量 icon=logos:python
import requests

r = requests.get('https://httpbin.org/get')

if r.status_code == requests.codes.ok: # 与 r.status_code == 200 相同
    print("请求成功！")
```

以下是一些在 `requests.codes` 下可用的常见代码：

| 代码 | 常量 |
| :--- | :--- |
| 200 | `ok`, `okay`, `all_ok` |
| 201 | `created` |
| 301 | `moved_permanently`, `moved` |
| 302 | `found` |
| 400 | `bad_request`, `bad` |
| 401 | `unauthorized` |
| 403 | `forbidden` |
| 404 | `not_found` |
| 500 | `internal_server_error`, `server_error` |
| 503 | `service_unavailable`, `unavailable` |
