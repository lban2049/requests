# API 参考

本节为 Requests 库中的所有公共类、方法和函数提供了详细而全面的参考。它旨在帮助需要了解库 API 具体细节的开发人员。

有关实际示例和常见用例，请参阅[用户指南](./user-guide.md)。

## 顶层接口

`requests` 模块提供了一组顶层函数，这些函数对应最常见的 HTTP 请求方法。这些函数是围绕 `requests.request` 函数的简单封装。

### `requests.request()`

这是所有其他 HTTP 方法函数派生所依据的核心函数。它会构造并发送一个 `Request`。

```python
requests.request(method, url, **kwargs)
```

**参数**

| Parameter | Description |
|---|---|
| `method` | 用于新 `Request` 对象的 HTTP 方法：`GET`、`OPTIONS`、`HEAD`、`POST`、`PUT`、`PATCH` 或 `DELETE`。 |
| `url` | 用于新 `Request` 对象的 URL。 |
| `params` | （可选）在 `Request` 的查询字符串中发送的字典、元组列表或字节流。 |
| `data` | （可选）在 `Request` 的正文中发送的字典、元组列表、字节流或类文件对象。 |
| `json` | （可选）在 `Request` 的正文中发送的可 JSON 序列化的 Python 对象。 |
| `headers` | （可选）随 `Request` 发送的 HTTP 请求头字典。 |
| `cookies` | （可选）随 `Request` 发送的字典或 `CookieJar` 对象。 |
| `files` | （可选）用于多部分编码上传的字典。例如 `{'name': file-like-object}`。 |
| `auth` | （可选）用于启用基本/摘要/自定义 HTTP 认证的认证元组或对象。 |
| `timeout` | （可选）在放弃前等待服务器发送数据的秒数。可以是一个浮点数，或一个 `(connect_timeout, read_timeout)` 元组。 |
| `allow_redirects` | （可选）布尔值。设为 `True` 以启用 GET/OPTIONS/POST/PUT/PATCH/DELETE/HEAD 重定向。默认为 `True`。 |
| `proxies` | （可选）一个将协议映射到代理 URL 的字典。 |
| `verify` | （可选）可以是一个布尔值（控制 TLS 证书验证），也可以是一个字符串（CA 证书包的路径）。默认为 `True`。 |
| `stream` | （可选）如果为 `False`（默认值），响应内容将立即被下载。 |
| `cert` | （可选）如果为字符串，则为 SSL 客户端证书文件（.pem）的路径。如果为元组，则为 `('cert', 'key')`。 |

**返回**

- 一个 `requests.Response` 对象。

**用法**

```python
import requests

req = requests.request('GET', 'https://httpbin.org/get')
print(req.status_code)
# 200
```

### 便捷方法

为方便起见，Requests 为每种 HTTP 方法提供了相应的函数：

- **`requests.get(url, params=None, **kwargs)`**：发送 GET 请求。
- **`requests.post(url, data=None, json=None, **kwargs)`**：发送 POST 请求。
- **`requests.put(url, data=None, **kwargs)`**：发送 PUT 请求。
- **`requests.patch(url, data=None, **kwargs)`**：发送 PATCH 请求。
- **`requests.delete(url, **kwargs)`**：发送 DELETE 请求。
- **`requests.head(url, **kwargs)`**：发送 HEAD 请求。
- **`requests.options(url, **kwargs)`**：发送 OPTIONS 请求。

## Session 对象

`Session` 对象允许你在多个请求之间保持某些参数。它还会在该 Session 实例发出的所有请求中保持 cookie，并使用 `urllib3` 的连接池。如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

```python
requests.Session()
```

**用法**

```python
import requests

s = requests.Session()
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

### Session 方法

`Session` 对象具有与顶层 `requests` 模块相同的方法接口：

- `session.request(method, url, **kwargs)`
- `session.get(url, **kwargs)`
- `session.post(url, **kwargs)`
- `session.put(url, **kwargs)`
- `session.patch(url, **kwargs)`
- `session.delete(url, **kwargs)`
- `session.head(url, **kwargs)`
- `session.options(url, **kwargs)`

其他有用的方法包括：

- **`session.mount(prefix, adapter)`**：将一个连接适配器注册到一个前缀上。这允许你为特定服务定义特殊的传输行为。
- **`session.close()`**：关闭所有适配器和会话。

## Response 对象

每次使用 Requests 发出调用时，你都会收到一个 `Response` 对象。该对象包含服务器对你的 HTTP 请求的响应。

### 响应属性

| Attribute | Description |
|---|---|
| `status_code` | HTTP 状态码的整数表示（例如 `200`、`404`）。 |
| `headers` | 不区分大小写的响应头字典。 |
| `encoding` | 用于解码 `r.text` 的编码。 |
| `text` | Unicode 形式的响应内容。 |
| `content` | 字节流形式的响应内容。 |
| `json()` | 一个返回响应的 JSON 解码内容（如果存在）的方法。 |
| `url` | 响应的最终 URL 位置（在任何重定向之后）。 |
| `ok` | 一个布尔值，如果 `status_code` 小于 400 则为 `True`，否则为 `False`。 |
| `reason` | HTTP 状态的文本原因（例如 “OK”、“Not Found”）。 |
| `cookies` | 服务器发回的 cookie 的 `RequestsCookieJar` 对象。 |
| `elapsed` | 一个 `timedelta` 对象，表示从发送请求到响应到达之间经过的时间。 |
| `request` | 此响应所对应的 `PreparedRequest` 对象。 |
| `history` | 请求历史中的 `Response` 对象列表（重定向）。 |

### 响应方法

| Method | Description |
|---|---|
| `json(**kwargs)` | 将响应体解码为 Python 对象。如果响应体不包含有效的 JSON，则会引发 `requests.exceptions.JSONDecodeError`。 |
| `raise_for_status()` | 如果 HTTP 请求返回了不成功的状态码（4xx 或 5xx），则会引发 `HTTPError`。 |
| `iter_content(chunk_size=1, decode_unicode=False)` | 迭代响应数据，适用于流式传输大型响应。 |
| `iter_lines(chunk_size=512, decode_unicode=False, delimiter=None)` | 逐行迭代响应数据。 |
| `close()` | 将连接释放回连接池。 |

## 异常

Requests 会针对各种错误引发异常。所有异常都是 `requests.exceptions.RequestException` 的子类。

| Exception | Description |
|---|---|
| `RequestException` | 发生的任何模糊问题的基础异常。 |
| `ConnectionError` | 因网络相关问题（例如 DNS 解析失败、连接被拒绝）而引发。 |
| `HTTPError` | 由 `response.raise_for_status()` 针对不成功的状态码（4xx 或 5xx）引发。 |
| `Timeout` | 请求超时。这是 `ConnectTimeout` 和 `ReadTimeout` 的父类。 |
| `ConnectTimeout` | 尝试连接到远程服务器时请求超时。 |
| `ReadTimeout` | 服务器在指定时间内未发送任何数据。 |
| `TooManyRedirects` | 请求超出了配置的最大重定向次数。 |
| `MissingSchema` | URL 方案（例如 `http` 或 `https`）缺失。 |
| `InvalidURL` | 提供的 URL 无效。 |
| `JSONDecodeError` | 当 `response.json()` 无法解码响应内容时引发。 |

## 身份验证

Requests 提供了几种内置的身份验证处理器。

### `HTTPBasicAuth`

为请求附加 HTTP 基本认证。

```python
from requests.auth import HTTPBasicAuth
requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
```

一种简便方法是向 `auth` 参数传递一个元组 `(username, password)`：

```python
requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
```

### `HTTPDigestAuth`

为请求附加 HTTP 摘要认证。

```python
from requests.auth import HTTPDigestAuth
url = 'https://httpbin.org/digest-auth/qop/user/pass'
requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
```

## 其他类和对象

- **`requests.Request`**：用户创建的对象，用于准备一个 `PreparedRequest`，然后发送到服务器。
- **`requests.PreparedRequest`**：一个完全准备好的请求对象，包含将要发送到服务器的确切字节流。你通常不需要手动创建它。
- **`requests.structures.CaseInsensitiveDict`**：一个不区分大小写的类字典对象，用于处理响应头。
- **`requests.cookies.RequestsCookieJar`**：一个 `CookieJar`，其行为也像字典，用于 `session.cookies` 和 `response.cookies`。
- **`requests.status_codes.codes`**：一个特殊的查找对象，提供按名称访问 HTTP 状态码的功能（例如，`requests.codes.ok` 的值为 `200`）。