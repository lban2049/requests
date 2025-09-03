# API 参考

本节为 Requests 库中的公共类、方法和函数提供了详细的参考。它旨在帮助需要全面了解可用工具及其特定参数的开发人员。

## 顶层 API

使用 Requests 最常见的方式是通过其简单的顶层 API。这些函数是方便的包装器，可在单次调用中处理请求的创建和发送。

### `requests.request(method, url, **kwargs)`

构造并发送一个 `Request`。这是所有其他顶层 HTTP 方法函数调用的基础函数。

**参数**

| 参数 | 描述 |
|---|---|
| `method` | 新 `Request` 对象的 HTTP 方法（例如 `'GET'`、`'POST'`、`'PUT'`）。 |
| `url` | 新 `Request` 对象的 URL。 |
| `params` | （可选）在 `Request` 的查询字符串中发送的字典、元组列表或字节流。 |
| `data` | （可选）在 `Request` 的正文中发送的字典、元组列表、字节流或类文件对象。 |
| `json` | （可选）在 `Request` 的正文中发送的可 JSON 序列化的 Python 对象。 |
| `headers` | （可选）随 `Request` 一同发送的 HTTP 标头字典。 |
| `cookies` | （可选）随 `Request` 一同发送的字典或 `CookieJar` 对象。 |
| `files` | （可选）用于多部分编码上传的字典。格式：`{'name': file-like-object}` 或 `{'name': ('filename', fileobj, 'content_type', custom_headers)}`。 |
| `auth` | （可选）用于启用基本/摘要/自定义 HTTP 身份验证的身份验证元组或可调用对象。 |
| `timeout` | （可选）等待服务器发送数据的秒数。可以是一个浮点数或 `(connect_timeout, read_timeout)` 元组。 |
| `allow_redirects` | （可选）一个布尔值，用于启用或禁用重定向。默认为 `True`。 |
| `proxies` | （可选）一个将协议映射到代理 URL 的字典。 |
| `verify` | （可选）一个用于控制 TLS 证书验证的布尔值，或一个指向 CA 证书包的字符串路径。默认为 `True`。 |
| `stream` | （可选）如果为 `False`（默认值），响应内容将立即被下载。 |
| `cert` | （可选）一个指向 SSL 客户端证书文件（`.pem`）的路径，或一个 `('cert', 'key')` 元组。 |

**返回：**一个 `requests.Response` 对象。

### 便捷方法

这些函数是使用指定方法调用 `requests.request()` 的快捷方式。

-   `requests.get(url, params=None, **kwargs)`：发送 GET 请求。
-   `requests.post(url, data=None, json=None, **kwargs)`：发送 POST 请求。
-   `requests.put(url, data=None, **kwargs)`：发送 PUT 请求。
-   `requests.patch(url, data=None, **kwargs)`：发送 PATCH 请求。
-   `requests.delete(url, **kwargs)`：发送 DELETE 请求。
-   `requests.head(url, **kwargs)`：发送 HEAD 请求。`allow_redirects` 默认设置为 `False`。
-   `requests.options(url, **kwargs)`：发送 OPTIONS 请求。

**示例：**
```python
import requests

response = requests.get('https://httpbin.org/get', params={'key': 'value'})
print(response.url)
# 输出: https://httpbin.org/get?key=value
```

## Session 对象

对于向同一主机发出多个请求的情况，`Session` 对象允许您在多个请求之间持久化某些参数，例如 Cookie 和标头。它还利用了连接池，这可以显著提升性能。

### `requests.Session()`

一个提供 Cookie 持久化、连接池和配置的 Requests 会话。

**基本用法**
```python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# 'x-test' 标头会在两个请求中都发送
s.get('https://httpbin.org/get')
s.get('https://httpbin.org/headers')
```

**上下文管理器用法**
```python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

**Session 方法**

`Session` 对象拥有与顶层 API 相同的所有 HTTP 方法函数（`get`、`post`、`put` 等）。当您在 `Session` 对象上调用方法时，它会使用在该会话上设置的配置。

**关键属性**

| 属性 | 描述 |
|---|---|
| `headers` | 将在每个请求中发送的 `CaseInsensitiveDict` 标头字典。 |
| `cookies` | 一个 `RequestsCookieJar` 对象，包含在该会话上设置的所有 Cookie。 |
| `auth` | 附加到每个请求的默认身份验证。 |
| `proxies` | 用于每个请求的代理字典。 |
| `params` | 附加到每个请求的查询字符串数据字典。 |
| `verify` | 默认的 SSL 验证设置。默认为 `True`。 |
| `cert` | 默认的 SSL 客户端证书。 |
| `max_redirects` | 允许的最大重定向次数。默认为 30。 |

## Response 对象

当您发出请求时，Requests 会返回一个包含服务器响应的 `Response` 对象。

### `requests.Response`

此对象包含服务器对 HTTP 请求的响应。

**关键属性和方法**

| 属性/方法 | 描述 |
|---|---|
| `status_code` | 整数形式的 HTTP 状态码（例如 `200`、`404`）。 |
| `headers` | 一个包含响应标头的 `CaseInsensitiveDict` 对象。 |
| `encoding` | 用于解码 `r.text` 的编码。 |
| `text` | 响应的内容，Unicode 格式。 |
| `content` | 响应的内容，字节流格式。 |
| `json(**kwargs)` | 将响应正文解码为 JSON。失败时引发 `JSONDecodeError`。 |
| `ok` | 一个布尔值，如果 `status_code` 小于 400，则为 `True`。 |
| `is_redirect` | 一个布尔值，如果响应是格式正确的 HTTP 重定向，则为 `True`。 |
| `url` | 响应的最终 URL 位置。 |
| `reason` | HTTP 状态的文本原因（例如 `'OK'`、`'Not Found'`）。 |
| `cookies` | 服务器返回的 Cookie 的 `RequestsCookieJar` 对象。 |
| `elapsed` | 一个 `timedelta` 对象，表示从发送请求到响应到达所经过的时间。 |
| `history` | 一个 `Response` 对象列表，包含请求的历史记录（重定向）。 |
| `request` | 此响应所对应的 `PreparedRequest` 对象。 |
| `raise_for_status()` | 如果 HTTP 请求返回了不成功的状态码（4xx 或 5xx），则引发 `HTTPError`。 |
| `iter_content()` | 迭代响应数据，对流式传输大文件很有用。 |
| `close()` | 将连接释放回连接池。 |

## 异常

Requests 会针对各种错误引发异常。所有异常都可在 `requests.exceptions` 模块中找到。

-   `requests.exceptions.RequestException`：Requests 中所有异常的基类。
-   `requests.exceptions.ConnectionError`：因网络相关问题（例如 DNS 解析失败、连接被拒绝）而引发。
-   `requests.exceptions.HTTPError`：由 `raise_for_status()` 在遇到不成功的状态码（4xx 或 5xx）时引发。
-   `requests.exceptions.URLRequired`：在未提供有效 URL 时引发。
-   `requests.exceptions.TooManyRedirects`：当请求超过配置的最大重定次数时引发。
-   `requests.exceptions.ConnectTimeout`：当连接超时时引发。
-   `requests.exceptions.ReadTimeout`：当服务器在规定时间内未发送任何数据时引发。
-   `requests.exceptions.Timeout`：`ConnectTimeout` 和 `ReadTimeout` 的基类。
-   `requests.exceptions.SSLError`：因 SSL 相关错误而引发。
-   `requests.exceptions.ProxyError`：因代理错误而引发。
-   `requests.exceptions.JSONDecodeError`：当 `response.json()` 无法解码响应正文时引发。

## 身份验证

Requests 提供了多种内置的身份验证处理程序。

-   `requests.auth.HTTPBasicAuth(username, password)`：将 HTTP 基本身份验证附加到请求。
    ```python
    from requests.auth import HTTPBasicAuth
    requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
    ```
-   `requests.auth.HTTPDigestAuth(username, password)`：将 HTTP 摘要式身份验证附加到请求。
-   `requests.auth.HTTPProxyAuth(username, password)`：将 HTTP 代理身份验证附加到请求。

## 其他实用组件

-   `requests.codes`：一个 `LookupDict` 对象，可通过通用名称访问 HTTP 状态码（例如 `requests.codes.ok` 的值为 `200`）。
-   `requests.models.Request`：一个用于在准备和发送请求之前创建带参数请求的对象。
-   `requests.models.PreparedRequest`：包含将要发送到服务器的确切字节流的对象。`Session.send()` 接受此对象。
-   `requests.adapters.HTTPAdapter`：一种传输适配器，可以挂载到 `Session` 上以自定义连接行为，例如设置重试策略。