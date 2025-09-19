# 异常

本页面提供了 Requests 库抛出的所有公共异常的完整列表，解释了它们的继承关系和发生时机。所有异常都可以从 `requests.exceptions` 导入。

当发生网络或协议错误时，Requests 将会抛出异常。理解这些异常有助于您构建能够妥善处理连接问题、超时和无效服务器响应的稳健应用程序。

## 异常层次结构

下图展示了 Requests 中异常的继承层次结构。该库的基础异常是 `requests.exceptions.RequestException`。

```d2
direction: down

IOError
ValueError

IOError -> RequestException

RequestException: {
  HTTPError
  URLRequired
  TooManyRedirects
  ChunkedEncodingError
  ContentDecodingError
  StreamConsumedError
  RetryError
  UnrewindableBodyError

  Timeout: {
    ReadTimeout
  }

  ConnectionError: {
    ProxyError
    SSLError
    ConnectTimeout
  }

  InvalidJSONError: {
    JSONDecodeError
  }

  InvalidURL: {
    InvalidProxyURL
  }
  
  MissingSchema
  InvalidSchema
  InvalidHeader
}

Timeout -> ConnectionError.ConnectTimeout

ValueError -> InvalidURL
ValueError -> MissingSchema
ValueError -> InvalidSchema
ValueError -> InvalidHeader
```

## 异常类

以下是该库抛出的所有异常类的详细列表。

| 异常 | 描述 |
| :--- | :--- |
| `RequestException` | 此模块中所有其他异常的基类。 |
| `HTTPError` | 针对不成功的 HTTP 响应（4xx 或 5xx 状态码）抛出。 |
| `ConnectionError` | 针对网络相关错误（例如，DNS 解析失败、连接被拒绝）抛出。 |
| `ProxyError` | 一种专门用于处理代理特定问题的 `ConnectionError`。 |
| `SSLError` | 一种专门用于处理 SSL 相关错误的 `ConnectionError`。 |
| `Timeout` | 超时错误的基类。可捕获 `ConnectTimeout` 和 `ReadTimeout`。 |
| `ConnectTimeout` | 在尝试建立连接时发生超时后抛出。 |
| `ReadTimeout` | 当服务器在指定时间内未发送任何数据时抛出。 |
| `URLRequired` | 当请求未提供有效 URL 时抛出。 |
| `TooManyRedirects` | 当请求超过配置的最大重定向次数时抛出。 |
| `MissingSchema` | 当 URL 协议（例如 `http` 或 `https`）缺失时抛出。 |
| `InvalidSchema` | 当提供的 URL 协议无效或不受支持时抛出。 |
| `InvalidURL` | 当提供的 URL 格式不正确时抛出。 |
| `InvalidProxyURL` | 当提供的代理 URL 格式不正确时抛出。 |
| `InvalidHeader` | 当为请求标头提供了无效值时抛出。 |
| `InvalidJSONError` | JSON 相关错误的基类。 |
| `JSONDecodeError` | 当解码 JSON 响应体失败时抛出。 |
| `ChunkedEncodingError` | 当服务器在分块编码的响应中发送了无效的数据块时抛出。 |
| `ContentDecodingError` | 当解码响应内容（例如，从 gzip 解码）失败时抛出。 |
| `StreamConsumedError` | 当尝试访问已被消费的响应内容时抛出。 |
| `RetryError` | 当自定义重试逻辑失败时抛出。 |
| `UnrewindableBodyError` | 当 Requests 需要回滚请求体但无法执行时抛出。 |

### `class requests.exceptions.RequestException`

> 处理您的请求时发生了一个不明确的异常。

这是 Requests 抛出的所有其他异常所继承的基础异常。您可以用它来捕获源自该库的任何错误。

```python 捕获基础异常
import requests

try:
    # 可能失败的操作
    response = requests.get('http://invalid-url-that-does-not-exist.local')
except requests.exceptions.RequestException as e:
    print(f"发生了一个错误: {e}")
```

### `class requests.exceptions.HTTPError`

> 发生了一个 HTTP 错误。

当对具有 4xx 或 5xx 状态码的 `Response` 对象调用 `raise_for_status()` 方法时，会抛出此异常。

### `class requests.exceptions.ConnectionError`

> 发生了连接错误。

此异常针对任何网络相关问题抛出，例如 DNS 解析失败或连接被拒绝。

### `class requests.exceptions.ProxyError`

> 发生了代理错误。

这是 `ConnectionError` 的一个子类，专门用于处理与代理连接相关的错误。

### `class requests.exceptions.SSLError`

> 发生了 SSL 错误。

这是 `ConnectionError` 的一个子类，专门用于处理 SSL 握手和验证错误。

### `class requests.exceptions.Timeout`

> 请求超时。

捕获此错误将同时捕获 `ConnectTimeout` 和 `ReadTimeout` 错误。当请求未在指定的超时时间内完成时，会抛出此异常。

### `class requests.exceptions.ConnectTimeout`

> 尝试连接到远程服务器时请求超时。

此错误表示在配置的超时时间内无法建立到服务器的连接。产生此错误的请求可以安全地重试。

### `class requests.exceptions.ReadTimeout`

> 服务器在指定的时间内没有发送任何数据。

当连接成功建立，但服务器未能在配置的读取超时时间内发送响应时，会发生此错误。

### `class requests.exceptions.URLRequired`

> 发起请求需要一个有效的 URL。

如果您在未提供 URL 的情况下尝试发起请求，则会抛出此异常。

### `class requests.exceptions.TooManyRedirects`

> 重定向次数过多。

如果请求超过了允许的最大重定向次数，则会抛出此异常，该次数可在 `Session` 对象上配置。

### `class requests.exceptions.MissingSchema`

> URL 协议（例如 http 或 https）缺失。

针对类似 `'example.com/api'` 而非 `'https://example.com/api'` 的 URL 抛出。

### `class requests.exceptions.InvalidSchema`

> 提供的 URL 协议无效或不受支持。

针对 Requests 不处理的协议的 URL（例如 `'ftp://example.com'`）抛出。

### `class requests.exceptions.InvalidURL`

> 提供的 URL 因某种原因无效。

这是一个针对格式不正确的 URL 的通用异常，适用于不符合其他更具体的 URL 相关异常的情况。

### `class requests.exceptions.InvalidProxyURL`

> 提供的代理 URL 无效。

`InvalidURL` 的一个子类，专门针对格式不正确的代理 URL 抛出。

### `class requests.exceptions.JSONDecodeError`

> 无法将文本解码为 json

当您调用 `response.json()` 但响应体不包含有效的 JSON 时，会抛出此异常。

## 警告类

Requests 还定义了一组自定义警告，以指示那些不一定需要抛出异常的潜在问题。

### `class requests.exceptions.RequestsWarning`

> Requests 的基础警告类。

Requests 中的所有其他警告都继承自此类。

### `class requests.exceptions.FileModeWarning`

> 文件以文本模式打开，但 Requests 已确定其二进制长度。

此警告表示在上传未以二进制模式（`'rb'`）打开的文件时存在潜在问题。

### `class requests.exceptions.RequestsDependencyWarning`

> 导入的依赖项与预期的版本范围不匹配。

如果像 `urllib3` 或 `chardet` 这样的依赖项版本不兼容，则会发出此警告，这可能导致意外行为。