# API 参考

本节为 Requests 库中所有公共类、方法和函数提供了详细而全面的参考。如需更多实用示例和叙述式指南，请参阅[用户指南](./user-guide.md)。

## 顶层函数

`requests` 模块提供了一组顶层函数，这些函数对应最常见的 HTTP 方法。它们是简单的包装器，可为你管理一个临时的 `Session` 对象。

### `requests.request(method, url, **kwargs)`

构造并发送一个 `Request`。这是所有其他顶层函数调用的基础函数。

**参数**

| Parameter | Description |
|---|---|
| `method` | 新 `Request` 对象的 HTTP 方法（例如 `'GET'`、`'POST'`）。 |
| `url` | 新 `Request` 对象的 URL。 |
| `params` | （可选）在查询字符串中发送的字典、元组列表或字节流。 |
| `data` | （可选）在请求体中发送的字典、元组列表、字节流或文件类对象。 |
| `json` | （可选）在请求体中发送的可 JSON 序列化的 Python 对象。 |
| `headers` | （可选）随请求发送的 HTTP 标头字典。 |
| `cookies` | （可选）随请求发送的字典或 `CookieJar` 对象。 |
| `files` | （可选）用于多部分编码上传的字典（例如 `{'name': file-like-object}`）。 |
| `auth` | （可选）用于启用 Basic/Digest/Custom HTTP Auth 的身份验证对象。 |
| `timeout` | （可选）等待服务器发送数据的秒数。可以是一个浮点数或一个 `(connect, read)` 元组。 |
| `allow_redirects` | （可选）一个布尔值，用于启用或禁用重定向。默认为 `True`。 |
| `proxies` | （可选）一个将协议映射到代理 URL 的字典。 |
| `verify` | （可选）一个用于控制 SSL 证书验证的布尔值，或一个指向 CA 证书包的字符串路径。默认为 `True`。 |
| `stream` | （可选）如果为 `False`，响应内容将立即被下载。默认为 `False`。 |
| `cert` | （可选）一个指向 SSL 客户端证书文件（`.pem`）的路径。可以是一个单独的文件或一个 `('cert', 'key')` 元组。 |

**返回：** 一个 `requests.Response` 对象。

### 便捷函数

为方便起见，Requests 为常见的 HTTP 方法提供了调用 `request()` 并传入相应方法参数的函数。

- `requests.get(url, params=None, **kwargs)`：发送 GET 请求。
- `requests.post(url, data=None, json=None, **kwargs)`：发送 POST 请求。
- `requests.put(url, data=None, **kwargs)`：发送 PUT 请求。
- `requests.patch(url, data=None, **kwargs)`：发送 PATCH 请求。
- `requests.delete(url, **kwargs)`：发送 DELETE 请求。
- `requests.head(url, **kwargs)`：发送 HEAD 请求。
- `requests.options(url, **kwargs)`：发送 OPTIONS 请求。

这些函数接受与 `requests.request()` 相同的关键字参数。

```python 使用顶层函数 icon=logos:python
import requests

response = requests.get('https://api.github.com/events')
print(response.status_code)

payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.post('https://httpbin.org/post', data=payload)
print(response.json())
```

## Session 对象

当向同一主机发出多个请求时，`Session` 对象允许你在多个请求之间保持某些参数，例如 Cookie 和标头。它还利用了 `urllib3` 的连接池，这可以显著提升性能。

### `requests.Session()`

一个提供 Cookie 持久化、连接池和配置的 Requests 会话。

```python Session 用法 icon=logos:python
import requests

# 使用上下文管理器可确保会话被正确关闭
with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get('https://httpbin.org/cookies')

    print(r.text)
    # '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

### Session 方法

`Session` 对象拥有顶层 API 的所有方法：
- `Session.request()`
- `Session.get()`
- `Session.post()`
- `Session.put()`
- `Session.patch()`
- `Session.delete()`
- `Session.head()`
- `Session.options()`
- `Session.send()`：发送一个 `PreparedRequest`。

### Session 属性

你可以在发出请求前通过设置 `Session` 对象的属性来对其进行配置：

| Attribute | Description |
|---|---|
| `headers` | 一个不区分大小写的标头字典，将在每个请求中发送。 |
| `cookies` | 一个包含会话 Cookie 的 `RequestsCookieJar` 对象。 |
| `auth` | 一个默认的身份验证元组或对象。 |
| `proxies` | 一个用于请求的代理字典。 |
| `hooks` | 一个事件处理钩子字典。唯一支持的钩子是 `'response'`。 |
| `params` | 一个查询字符串数据字典，将附加到每个请求中。 |
| `verify` | 默认 SSL 验证设置（`True`、`False` 或指向 CA 证书包的路径）。 |
| `cert` | 默认 SSL 客户端证书。 |
| `max_redirects` | 允许的最大重定向次数。默认为 30。 |
| `stream` | 流式响应内容的默认设置。默认为 `False`。 |
| `trust_env` | 如果为 `True`，则信任代理等环境变量设置。默认为 `True`。 |
| `adapters` | 一个已挂载的传输适配器字典。 |

## 主要接口对象

这些是你在使用 Requests 时与之交互的主要对象。

### `requests.Request(method, url, **kwargs)`

一个用户创建的 `Request` 对象，用于准备一个将要发送到服务器的 `PreparedRequest`。它持有请求在被处理前的所有信息。

### `requests.PreparedRequest`

完全可变的对象，包含将要发送到服务器的确切字节流。通常你不会手动创建它，而是从 `Session.prepare_request()` 或 `Request.prepare()` 接收。其属性包括 `method`、`url`、`headers` 和 `body`。

### `requests.Response`

`Response` 对象包含服务器对 HTTP 请求的响应。

**属性**

| Attribute | Description |
|---|---|
| `status_code` | HTTP 状态的整数代码（例如 `200`、`404`）。 |
| `headers` | 不区分大小写的响应标头字典。 |
| `encoding` | 解码 `r.text` 时使用的编码。如果为 `None`，则会自动猜测。 |
| `text` | 响应的内容，Unicode 格式。 |
| `content` | 响应的内容，字节流格式。 |
| `url` | 经过任何重定向后响应的最终 URL 位置。 |
| `history` | 请求历史（重定向）中的 `Response` 对象列表。 |
| `reason` | HTTP 状态的文本原因（例如 `'OK'`、`'Not Found'`）。 |
| `cookies` | 服务器返回的 Cookie 的 `RequestsCookieJar` 对象。 |
| `elapsed` | 一个 `timedelta` 对象，表示从发送请求到响应到达所经过的时间。 |
| `request` | 此响应对应的 `PreparedRequest` 对象。 |
| `ok` | 一个布尔值，如果 `status_code` 小于 400，则为 `True`。 |
| `is_redirect` | 一个布尔值，如果响应是重定向，则为 `True`。 |

**方法**

| Method | Description |
|---|---|
| `json(**kwargs)` | 如果响应体包含有效的 JSON，则将其解码为 Python 对象。 |
| `raise_for_status()` | 如果 HTTP 请求返回了不成功的状态码（4xx 或 5xx），则引发 `HTTPError`。 |
| `close()` | 将连接释放回连接池。在不使用 `stream=True` 时通常不需要。 |
| `iter_content(chunk_size=1, decode_unicode=False)` | 以块的形式迭代响应数据。 |
| `iter_lines(chunk_size=512, decode_unicode=False)` | 一次一行地迭代响应数据。 |

## 异常

Requests 会针对各种错误引发异常。所有异常都位于 `requests.exceptions` 模块中，并继承自 `requests.exceptions.RequestException`。

下图展示了异常的层级结构：

```d2 异常层级结构
direction: down

RequestException: { 
  shape: class 
}

InvalidJSONError: { 
  shape: class 
}
HTTPError: { 
  shape: class 
}
ConnectionError: { 
  shape: class 
}
Timeout: { 
  shape: class 
}
URLRequired: { 
  shape: class 
}
TooManyRedirects: { 
  shape: class 
}
MissingSchema: { 
  shape: class 
}
InvalidSchema: { 
  shape: class 
}
InvalidURL: { 
  shape: class 
}
ChunkedEncodingError: { 
  shape: class 
}
ContentDecodingError: { 
  shape: class 
}
StreamConsumedError: { 
  shape: class 
}
RetryError: { 
  shape: class 
}
UnrewindableBodyError: { 
  shape: class 
}

RequestException -> InvalidJSONError
RequestException -> HTTPError
RequestException -> ConnectionError
RequestException -> Timeout
RequestException -> URLRequired
RequestException -> TooManyRedirects
RequestException -> MissingSchema
RequestException -> InvalidSchema
RequestException -> InvalidURL
RequestException -> ChunkedEncodingError
RequestException -> ContentDecodingError
RequestException -> StreamConsumedError
RequestException -> RetryError
RequestException -> UnrewindableBodyError

JSONDecodeError: { 
  shape: class 
}
ProxyError: { 
  shape: class 
}
SSLError: { 
  shape: class 
}
ReadTimeout: { 
  shape: class 
}
ConnectTimeout: { 
  shape: class 
}

InvalidJSONError -> JSONDecodeError
ConnectionError -> ProxyError
ConnectionError -> SSLError
Timeout -> ReadTimeout

ConnectionError -> ConnectTimeout
Timeout -> ConnectTimeout
```

**常见异常**

- `requests.exceptions.RequestException`: 库中所有其他异常都继承的基础异常类。
- `requests.exceptions.ConnectionError`: 因网络相关问题（如 DNS 解析失败或连接被拒绝）而引发。
- `requests.exceptions.HTTPError`: 由 `raise_for_status()` 方法针对不成功的状态码（4xx 或 5xx）而引发。
- `requests.exceptions.Timeout`: 当请求超时时引发。这是更具体超时异常的基类。
- `requests.exceptions.ConnectTimeout`: 在尝试连接到远程服务器时发生超时而引发。
- `requests.exceptions.ReadTimeout`: 当服务器在规定时间内未发送任何数据时引发。
- `requests.exceptions.TooManyRedirects`: 当请求超过配置的最大重定向次数时引发。

```python 处理异常 icon=logos:python
import requests

try:
    response = requests.get('https://example.com/nonexistent', timeout=1)
    response.raise_for_status() # 为 404 引发 HTTPError
except requests.exceptions.Timeout:
    print('请求超时')
except requests.exceptions.HTTPError as err:
    print(f'发生 HTTP 错误: {err}')
except requests.exceptions.RequestException as err:
    print(f'发生错误: {err}')
```

## 身份验证

Requests 提供了几种内置的身份验证处理器。它们被传递给请求中的 `auth` 参数。

- `requests.auth.HTTPBasicAuth(username, password)`：将 HTTP Basic Authentication 附加到请求中。
- `requests.auth.HTTPProxyAuth(username, password)`：将 HTTP Proxy Authentication 附加到请求中。
- `requests.auth.HTTPDigestAuth(username, password)`：将 HTTP Digest Authentication 附加到请求中。
- `requests.auth.AuthBase`：用于创建自定义身份验证方案的基类。

```python 基本身份验证 icon=logos:python
from requests.auth import HTTPBasicAuth

# 显式使用类
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=HTTPBasicAuth('user', 'pass'))
print(response.status_code)

# 一种简便的写法是传递一个元组
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
print(response.status_code)
```

## 底层类与对象

这些组件提供了高级控制功能，并构成了该库的基石。

- **`requests.adapters.HTTPAdapter`**：一种传输适配器，允许你配置连接池、重试次数和其他底层 HTTP 设置。你可以使用 `Session.mount()` 将适配器挂载到 `Session` 对象上。
- **`requests.structures.CaseInsensitiveDict`**：一种类字典对象，其键查找不区分大小写。用于请求和响应标头。
- **`requests.cookies.RequestsCookieJar`**：一种 `CookieJar`，它还提供了一个类字典接口来管理 Cookie。
- **`requests.codes`**：一个查找对象，通过通用名称提供对 HTTP 状态码的访问（例如，`requests.codes.ok` 为 `200`，`requests.codes.not_found` 为 `404`）。

```python 使用状态码 icon=logos:python
import requests

response = requests.get('https://httpbin.org/status/418')
if response.status_code == requests.codes.im_a_teapot:
    print("我是一个茶壶！")
```