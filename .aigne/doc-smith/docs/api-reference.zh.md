# API 参考

本文档为 Requests 库中的公共类、方法和函数提供了详细的参考。它旨在帮助需要全面了解该库功能的开发者。

有关实际示例和用例驱动的指南，请参阅 [用户指南](./user-guide.md)。

## 主要接口

使用 Requests 最简单的方法是通过顶层 `requests` 包直接提供的方法。

### `requests.request()`

这是所有其他请求方法（`get`、`post` 等）的基础函数。它允许你构建和发送任何类型的 HTTP 请求。

```python Request function icon=logos:python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req)
# <Response [200]>
```

**参数**

<x-field data-name="method" data-type="string" data-required="true" data-desc="新 Request 对象的 HTTP 方法：GET、OPTIONS、HEAD、POST、PUT、PATCH 或 DELETE。"></x-field>
<x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
<x-field data-name="params" data-type="dict, list, or bytes" data-required="false" data-desc="在请求的查询字符串中发送的数据。"></x-field>
<x-field data-name="data" data-type="dict, list, bytes, or file-like object" data-required="false" data-desc="在请求体中发送的数据。"></x-field>
<x-field data-name="json" data-type="object" data-required="false" data-desc="在请求体中发送的可 JSON 序列化的 Python 对象。"></x-field>
<x-field data-name="headers" data-type="dict" data-required="false" data-desc="随请求发送的 HTTP 标头字典。"></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-required="false" data-desc="随请求发送的 Cookie。"></x-field>
<x-field data-name="files" data-type="dict" data-required="false" data-desc="用于多部分编码上传的字典，例如 {'name': file-like-object}。"></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-required="false" data-desc="用于启用 Basic/Digest/自定义 HTTP 身份验证的验证对象。"></x-field>
<x-field data-name="timeout" data-type="float or tuple" data-required="false" data-desc="在放弃前等待服务器发送数据的秒数。可以是一个浮点数或一个 (connect, read) 元组。"></x-field>
<x-field data-name="allow_redirects" data-type="bool" data-default="true" data-required="false" data-desc="启用或禁用重定向。默认为 True。"></x-field>
<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="将协议映射到代理 URL 的字典。"></x-field>
<x-field data-name="verify" data-type="bool or string" data-default="true" data-required="false" data-desc="控制 TLS 证书验证。可以是一个布尔值或一个 CA 证书包的路径。"></x-field>
<x-field data-name="stream" data-type="bool" data-default="false" data-required="false" data-desc="如果为 False，将立即下载响应内容。"></x-field>
<x-field data-name="cert" data-type="string or tuple" data-required="false" data-desc="SSL 客户端证书文件（.pem）的路径或一个 ('cert', 'key') 元组。"></x-field>

**返回**

<x-field data-name="response" data-type="requests.Response" data-desc="包含服务器响应的 Response 对象。"></x-field>

### 便捷方法

Requests 为所有常见的 HTTP 动词提供了简便方法。

- `requests.get(url, params=None, **kwargs)`: 发送 GET 请求。
- `requests.post(url, data=None, json=None, **kwargs)`: 发送 POST 请求。
- `requests.put(url, data=None, **kwargs)`: 发送 PUT 请求。
- `requests.patch(url, data=None, **kwargs)`: 发送 PATCH 请求。
- `requests.delete(url, **kwargs)`: 发送 DELETE 请求。
- `requests.head(url, **kwargs)`: 发送 HEAD 请求。
- `requests.options(url, **kwargs)`: 发送 OPTIONS 请求。

这些方法接受与 `requests.request()` 相同的参数，但 `method` 参数已预先填充。

```python Convenience Methods icon=logos:python
import requests

# GET request with URL parameters
response = requests.get('https://httpbin.org/get', params={'key': 'value'})

# POST request with a JSON body
response = requests.post('https://httpbin.org/post', json={'user': 'kenneth'})

print(response.status_code)
# 200
```

## Session 对象

对于向同一主机发出多个请求，`Session` 对象允许你在请求之间持久化某些参数，例如 Cookie 和标头。它还使用连接池，这可以显著提高性能。

```python Session Object icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# Both requests will have the 'x-test' header
s.get('https://httpbin.org/headers')
s.get('https://httpbin.org/headers')
```

### `requests.Session`

一个提供 Cookie 持久化、连接池和配置的 Requests 会话。

#### Session 属性

<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="一个不区分大小写的字典，包含从此会话发送的每个请求的标头。"></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="一个包含此会话上设置的所有 Cookie 的 CookieJar。"></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="附加到每个请求的默认身份验证对象。"></x-field>
<x-field data-name="proxies" data-type="dict" data-desc="用于每个请求的代理字典。"></x-field>
<x-field data-name="params" data-type="dict" data-desc="附加到每个请求的查询字符串数据字典。"></x-field>
<x-field data-name="verify" data-type="bool or string" data-desc="默认 SSL 验证设置。"></x-field>
<x-field data-name="cert" data-type="string or tuple" data-desc="默认 SSL 客户端证书。"></x-field>
<x-field data-name="max_redirects" data-type="int" data-default="30" data-desc="允许的最大重定向次数。"></x-field>

#### Session 方法

`Session` 对象具有与顶层 API 相同的方法（`get`、`post`、`request` 等）。此外，它还提供以下方法：

- `prepare_request(request)`: 使用会话的设置（Cookie、标头等）准备一个 `Request` 对象，返回一个 `PreparedRequest`。
- `send(request, **kwargs)`: 发送一个 `PreparedRequest` 对象。
- `mount(prefix, adapter)`: 将传输适配器注册到 URL 前缀。
- `close()`: 关闭所有适配器和会话。

## 核心对象

这些是驱动 Requests 的主要对象。

### `requests.Request`

表示用户创建的 HTTP 请求。通常在创建后传递给 `Session.prepare_request()`。

**构造函数参数**

<x-field data-name="method" data-type="string" data-desc="HTTP 方法。"></x-field>
<x-field data-name="url" data-type="string" data-desc="请求的 URL。"></x-field>
<x-field data-name="headers" data-type="dict" data-desc="标头字典。"></x-field>
<x-field data-name="files" data-type="dict" data-desc="用于多部分上传的文件字典。"></x-field>
<x-field data-name="data" data-type="dict, list, bytes, or file-like" data-desc="请求体。"></x-field>
<x-field data-name="json" data-type="object" data-desc="请求体的 JSON 数据。"></x-field>
<x-field data-name="params" data-type="dict" data-desc="URL 参数。"></x-field>
<x-field data-name="auth" data-type="tuple or AuthBase" data-desc="身份验证处理器。"></x-field>
<x-field data-name="cookies" data-type="dict or CookieJar" data-desc="附加到请求的 Cookie。"></x-field>

### `requests.PreparedRequest`

表示一个完全准备好的请求，包含将要发送到服务器的确切字节。你不应手动实例化此类。

**属性**

<x-field data-name="method" data-type="string" data-desc="HTTP 动词。"></x-field>
<x-field data-name="url" data-type="string" data-desc="完整 URL。"></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="请求标头。"></x-field>
<x-field data-name="body" data-type="bytes or file-like" data-desc="请求体。"></x-field>

### `requests.Response`

包含服务器对 HTTP 请求的响应。

**属性**

<x-field data-name="status_code" data-type="int" data-desc="HTTP 状态码的整数表示（例如 200、404）。"></x-field>
<x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="不区分大小写的响应标头字典。"></x-field>
<x-field data-name="encoding" data-type="string" data-desc="用于解码 `response.text` 的编码。"></x-field>
<x-field data-name="url" data-type="string" data-desc="响应的最终 URL 位置，在任何重定向之后。"></x-field>
<x-field data-name="history" data-type="list[Response]" data-desc="请求历史（重定向）中的 Response 对象列表。"></x-field>
<x-field data-name="reason" data-type="string" data-desc="HTTP 状态的文本原因（例如 'OK'、'Not Found'）。"></x-field>
<x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="服务器返回的 Cookie 的 CookieJar。"></x-field>
<x-field data-name="elapsed" data-type="timedelta" data-desc="发送请求和接收到响应之间经过的时间。"></x-field>
<x-field data-name="request" data-type="PreparedRequest" data-desc="此响应对应的 PreparedRequest 对象。"></x-field>
<x-field data-name="ok" data-type="bool" data-desc="如果 `status_code` 小于 400，则返回 True，否则返回 False。"></x-field>
<x-field data-name="is_redirect" data-type="bool" data-desc="如果此响应是格式正确的 HTTP 重定向，则为 True。"></x-field>
<x-field data-name="content" data-type="bytes" data-desc="响应的内容，以字节为单位。"></x-field>
<x-field data-name="text" data-type="string" data-desc="响应的内容，以 Unicode 编码。"></x-field>
<x-field data-name="links" data-type="dict" data-desc="返回解析后的响应头链接（如果存在）。"></x-field>

**方法**

<x-field data-name="json(**kwargs)" data-type="method" data-desc="将 JSON 响应体解码为 Python 对象。"></x-field>
<x-field data-name="iter_content(chunk_size=1, decode_unicode=False)" data-type="method" data-desc="迭代响应数据。避免一次性将内容读入内存。"></x-field>
<x-field data-name="iter_lines(chunk_size=512, decode_unicode=False)" data-type="method" data-desc="逐行迭代响应数据。"></x-field>
<x-field data-name="raise_for_status()" data-type="method" data-desc="如果 HTTP 请求返回不成功的状态码，则引发 HTTPError。"></x-field>
<x-field data-name="close()" data-type="method" data-desc="将连接释放回连接池。"></x-field>

## 身份验证

Requests 提供了几种内置的身份验证处理器。

- `requests.auth.HTTPBasicAuth(username, password)`: 将 HTTP 基本身份验证附加到请求。
- `requests.auth.HTTPProxyAuth(username, password)`: 将 HTTP 代理身份验证附加到请求。
- `requests.auth.HTTPDigestAuth(username, password)`: 将 HTTP 摘要式身份验证附加到请求。

## 异常

Requests 会针对各种错误引发异常。所有异常都位于 `requests.exceptions` 模块中，并继承自 `requests.exceptions.RequestException`。

| Exception | Description |
|---|---|
| `RequestException` | 所有 Requests 异常的基类。 |
| `ConnectionError` | 因网络相关问题（如 DNS 解析失败、连接被拒绝）而引发。 |
| `HTTPError` | 由 `response.raise_for_status()` 针对不成功的状态码（4xx 或 5xx）引发。 |
| `ProxyError` | 因代理服务器问题而引发。 |
| `SSLError` | 因 SSL 相关错误而引发。 |
| `Timeout` | 超时异常的基类。 |
| `ConnectTimeout` | 连接超时时引发。 |
| `ReadTimeout` | 服务器在规定时间内未发送任何数据时引发。 |
| `URLRequired` | 未提供有效 URL 时引发。 |
| `TooManyRedirects` | 请求超过配置的最大重定向次数时引发。 |
| `MissingSchema` | URL 缺少协议方案（如 `http://`）时引发。 |
| `InvalidURL` | URL 格式不正确时引发。 |
| `JSONDecodeError` | `response.json()` 解码响应体失败时引发。 |

## 传输适配器

传输适配器提供了定义 Requests 如何与传输协议交互的机制。最常见的是 `HTTPAdapter`。

### `requests.adapters.HTTPAdapter`

urllib3 的内置 HTTP 适配器。

**构造函数参数**

<x-field data-name="pool_connections" data-type="int" data-default="10" data-desc="要缓存的 urllib3 连接池数量。"></x-field>
<x-field data-name="pool_maxsize" data-type="int" data-default="10" data-desc="连接池中要保存的最大连接数。"></x-field>
<x-field data-name="max_retries" data-type="int or Retry" data-default="0" data-desc="每个连接应尝试的最大重试次数。"></x-field>
<x-field data-name="pool_block" data-type="bool" data-default="false" data-desc="连接池是否应阻塞等待连接。"></x-field>

## 其他实用工具

### `requests.status_codes`

一个查找对象，允许通过常用名称访问 HTTP 状态码。

```python Status Codes icon=logos:python
import requests

print(requests.codes.ok) # 200
print(requests.codes.not_found) # 404
print(requests.codes['im_a_teapot']) # 418
```

### `requests.structures.CaseInsensitiveDict`

一个类似字典的对象，其键查找不区分大小写。这用于标头。

```python CaseInsensitiveDict icon=logos:python
from requests.structures import CaseInsensitiveDict

headers = CaseInsensitiveDict()
headers['Accept'] = 'application/json'

print(headers['accept']) # 'application/json'
```