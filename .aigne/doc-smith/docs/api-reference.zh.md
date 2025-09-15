# API 参考

欢迎来到 Requests API 参考。本节提供了 Requests 库中所有面向公众的类、方法和对象的详细信息。它适用于需要查找特定参数或详细了解特定函数行为的用户。

有关面向任务的实用示例，请参阅[用户指南](./user-guide.md)。

## 顶级 API

使用 Requests 的最简单方法是调用顶级方法。这些函数是临时 `Session` 对象的包装器，非常适合简单的一次性请求。

### `requests.request(method, url, **kwargs)`

构建并发送一个 `Request`。这是所有其他顶级请求方法调用的基础方法。

**参数**

<x-field data-name="method" data-type="string" data-required="true" data-desc="新 Request 对象的 HTTP 方法：GET、OPTIONS、HEAD、POST、PUT、PATCH 或 DELETE。"></x-field>
<x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
<x-field data-name="params" data-type="dict, list of tuples, or bytes" data-required="false" data-desc="在请求的查询字符串中发送的数据。"></x-field>
<x-field data-name="data" data-type="dict, list of tuples, bytes, or file-like object" data-required="false" data-desc="在请求正文中发送的数据。用于表单编码数据。"></x-field>
<x-field data-name="json" data-type="object" data-required="false" data-desc="在请求正文中发送的可 JSON 序列化的 Python 对象。"></x-field>
<x-field data-name="headers" data-type="dict" data-required="false" data-desc="随请求发送的 HTTP 标头字典。"></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="随请求发送的对象。"></x-field>
<x-field data-name="files" data-type="dict" data-required="false" data-desc="'name': file-like-objects 格式的字典，用于多部分编码上传。"></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="用于启用 Basic/Digest/Custom HTTP Auth 的 Auth 元组或可调用对象。"></x-field>
<x-field data-name="timeout" data-type="float or tuple" data-required="false" data-desc="在放弃之前等待服务器发送数据的秒数。可以是一个浮点数（用于连接和读取超时），也可以是一个 (connect, read) 元组。"></x-field>
<x-field data-name="allow_redirects" data-type="bool" data-default="True" data-required="false" data-desc="启用或禁用重定向。"></x-field>
<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="将协议映射到代理 URL 的字典。"></x-field>
<x-field data-name="verify" data-type="bool or string" data-default="True" data-required="false" data-desc="控制是否验证服务器的 TLS 证书。也可以是 CA 证书包的路径。"></x-field>
<x-field data-name="stream" data-type="bool" data-default="False" data-required="false" data-desc="如果为 False，将立即下载响应内容。"></x-field>
<x-field data-name="cert" data-type="string or tuple" data-required="false" data-desc="SSL 客户端证书文件（.pem）的路径。如果是元组，则为 ('cert', 'key') 对。"></x-field>

**返回**

<x-field data-name="response" data-type="requests.Response" data-desc="一个 Response 对象。"></x-field>

**用法**

```python icon=logos:python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req.status_code)
# 200
```

### 便捷方法

Requests 为每个 HTTP 动词提供了简写方法。

- `requests.get(url, params=None, **kwargs)`：发送 GET 请求。
- `requests.post(url, data=None, json=None, **kwargs)`：发送 POST 请求。
- `requests.put(url, data=None, **kwargs)`：发送 PUT 请求。
- `requests.patch(url, data=None, **kwargs)`：发送 PATCH 请求。
- `requests.delete(url, **kwargs)`：发送 DELETE 请求。
- `requests.head(url, **kwargs)`：发送 HEAD 请求。
- `requests.options(url, **kwargs)`：发送 OPTIONS 请求。

这些方法接受与 `requests.request()` 函数相同的 `**kwargs`，但不包括 `method`。

## Session 对象

对于向同一主机发出多个请求，`Session` 对象允许您在多个请求之间保持某些参数。它还会重用底层的 TCP 连接，这可以显著提高性能。

### `requests.Session()`

一个提供 cookie 持久化、连接池和配置的 Requests 会话。

**基本用法**

```python icon=logos:python
import requests

s = requests.Session()
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# { "cookies": { "sessioncookie": "123456789" } }
```

### Session 属性

`Session` 对象具有与 `Request` 对象相同的属性，可以设置这些属性以应用于该会话发出的所有请求。

<x-field data-name="headers" data-type="dict" data-desc="一个不区分大小写的标头字典，将在每个请求中发送。"></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="附加到每个请求的默认身份验证对象。"></x-field>
<x-field data-name="proxies" data-type="dict" data-desc="用于每个请求的代理字典。"></x-field>
<x-field data-name="hooks" data-type="dict" data-desc="事件处理钩子。"></x-field>
<x-field data-name="params" data-type="dict" data-desc="附加到每个请求的查询字符串数据字典。"></x-field>
<x-field data-name="verify" data-type="bool or string" data-desc="默认 SSL 验证设置。"></x-field>
<x-field data-name="cert" data-type="string or tuple" data-desc="默认 SSL 客户端证书。"></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="一个包含此会话中设置的所有 cookie 的 CookieJar。"></x-field>

### Session 方法

Session 对象具有与顶级 API 相同的方法（`get`、`post`、`request` 等）。

#### `mount(prefix, adapter)`

将连接适配器注册到前缀。这允许您为某些服务定义特殊的传输行为。有关更多详细信息，请参阅[高级用法](./advanced-usage-adapters-and-hooks.md)。

#### `close()`

关闭所有适配器，从而关闭会话，释放任何池化的连接。

## Response 对象

当您发出请求时，Requests 会返回一个 `Response` 对象，其中包含服务器的响应。

### `requests.Response` 属性

<x-field data-name="status_code" data-type="int" data-desc="响应的整数状态码（例如 200、404）。"></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="一个不区分大小写的响应标头字典。"></x-field>
<x-field data-name="encoding" data-type="string" data-desc="用于解码响应文本的编码。如果为 None，将进行猜测。"></x-field>
<x-field data-name="url" data-type="string" data-desc="经过任何重定向后响应的最终 URL 位置。"></x-field>
<x-field data-name="history" data-type="list" data-desc="请求历史（重定向）中的 Response 对象列表。"></x-field>
<x-field data-name="reason" data-type="string" data-desc="状态码的文本原因（例如 'OK'、'Not Found'）。"></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="服务器返回的 cookie 的 CookieJar。"></x-field>
<x-field data-name="elapsed" data-type="datetime.timedelta" data-desc="从发送请求到响应到达所经过的时间。"></x-field>
<x-field data-name="request" data-type="PreparedRequest" data-desc="此响应对应的 PreparedRequest 对象。"></x-field>
<x-field data-name="ok" data-type="bool" data-desc="如果 status_code 小于 400，则返回 True，否则返回 False。"></x-field>
<x-field data-name="is_redirect" data-type="bool" data-desc="如果此响应是格式正确的 HTTP 重定向，则返回 True。"></x-field>
<x-field data-name="content" data-type="bytes" data-desc="响应的内容，以字节为单位。"></x-field>
<x-field data-name="text" data-type="string" data-desc="响应的内容，以 Unicode 编码。"></x-field>
<x-field data-name="links" data-type="dict" data-desc="返回响应中已解析的标头链接（如果有）。"></x-field>

### `requests.Response` 方法

#### `json(**kwargs)`

将 JSON 响应正文解码为 Python 对象。任何关键字参数都将传递给 `json.loads`。

#### `raise_for_status()`

如果 HTTP 请求返回不成功的状态码（4xx 或 5xx），则引发 `HTTPError`。

#### `iter_content(chunk_size=1, decode_unicode=False)`

迭代响应数据。当请求中的 `stream=True` 时，这可以避免一次性将内容读入内存。

#### `iter_lines(chunk_size=512, decode_unicode=False, delimiter=None)`

迭代响应数据，一次一行。

#### `close()`

将连接释放回连接池。通常不需要显式调用此方法。

## 身份验证

Requests 提供了几种内置的身份验证处理程序。

### `requests.auth.HTTPBasicAuth(username, password)`

将 HTTP 基本身份验证附加到给定的 Request 对象。它可以传递给 `auth` 参数。

```python icon=logos:python
from requests.auth import HTTPBasicAuth
requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
```

一个简便的方法是传递一个元组：

```python icon=logos:python
requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
```

### `requests.auth.HTTPDigestAuth(username, password)`

将 HTTP 摘要式身份验证附加到给定的 Request 对象。

```python icon=logos:python
from requests.auth import HTTPDigestAuth
url = 'https://httpbin.org/digest-auth/qop/user/pass'
requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
```

## 异常

Requests 会针对各种错误引发异常。您的代码应该预见到这些异常。

| Exception | Description |
|---|---|
| `requests.exceptions.RequestException` | Requests 中所有异常的基异常类。 |
| `requests.exceptions.ConnectionError` | 因网络相关错误（例如 DNS 解析失败、连接被拒绝）而引发。 |
| `requests.exceptions.HTTPError` | 由 `raise_for_status()` 针对不成功的状态码（4xx 或 5xx）引发。 |
| `requests.exceptions.Timeout` | 请求超时。这是更具体的超时异常的基类。 |
| `requests.exceptions.ConnectTimeout` | 尝试连接到远程服务器时请求超时。 |
| `requests.exceptions.ReadTimeout` | 服务器在指定时间内未发送任何数据。 |
| `requests.exceptions.TooManyRedirects` | 请求超出了配置的最大重定向次数。 |
| `requests.exceptions.URLRequired` | 发出请求需要一个有效的 URL。 |
| `requests.exceptions.MissingSchema` | URL 方案（例如 `http` 或 `https`）缺失。 |
| `requests.exceptions.InvalidURL` | 提供的 URL 无效。 |
| `requests.exceptions.JSONDecodeError` | 当 `response.json()` 无法解码响应内容时引发。 |
| `requests.exceptions.SSLError` | 发生 SSL 错误。 |

## 状态码查询

Requests 提供了一个方便的对象，用于按名称查找状态码。

### `requests.codes`

这是一个 `LookupDict` 对象，允许不区分大小写地访问 HTTP 状态码。

**用法**

```python icon=logos:python
import requests

print(requests.codes.ok)          # 200
print(requests.codes.not_found)   # 404
print(requests.codes['teapot'])   # 418
print(requests.codes.teapot)      # 418
```