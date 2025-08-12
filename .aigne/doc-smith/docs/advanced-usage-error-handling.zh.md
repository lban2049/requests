# 错误处理

在进行 HTTP 请求时，可能会出现各种问题，从网络断开连接到服务器端问题或无效响应。Requests 库提供了一套全面的自定义异常，帮助您预测并优雅地处理这些问题，使您的应用程序更具弹性。有关所有异常类型的完整列表和详细描述，请参阅[异常](./api-reference-exceptions.md)部分。

## 捕获所有 Requests 异常

Requests 抛出的所有异常都继承自 `requests.exceptions.RequestException`。这个基类允许您使用单个 `try-except` 块捕获 Requests 库特有的任何错误。

```python
import requests

try:
    response = requests.get('https://httpbin.org/get')
    response.raise_for_status() # 对错误的 HTTP 状态码抛出异常
    print("Request successful!")
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")
```

此示例演示了如何在 `GET` 请求期间或调用 `raise_for_status()` 时捕获可能发生的任何 `RequestException`。

## 处理特定错误类型

虽然捕获 `RequestException` 对于通用错误处理很有用，但您通常需要更细粒度的控制，以针对不同类型的问题做出不同的响应。Requests 为不同的错误场景提供了特定的异常类。

### HTTP 状态码错误

如果 HTTP 请求返回不成功的状态码（4xx 或 5xx），Requests 可能会抛出 `requests.exceptions.HTTPError`。您可以使用 `Response.raise_for_status()` 方法轻松检查并处理这些错误。

`Response.raise_for_status()` 在响应的状态码介于 400 到 600 之间（客户端错误或服务器错误）时会抛出 `HTTPError`。如果状态码小于 400，则不会抛出错误。

```python
import requests

try:
    # 此 URL 将返回 404 Not Found 错误
    response = requests.get('https://httpbin.org/status/404')
    response.raise_for_status() # 这将抛出 HTTPError
    print("Request successful!")
except requests.exceptions.HTTPError as e:
    print(f"HTTP Error: {e}")
    print(f"Status Code: {e.response.status_code}")
    print(f"Reason: {e.response.reason}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

此示例专门捕获 `HTTPError` 以从响应中提取状态码和原因，使您能够对客户端或服务器错误做出适当的反应。

### 连接和超时错误

当连接到服务器出现问题或服务器响应时间过长时，会发生这些错误。Requests 为这些场景提供了几种异常：

*   `requests.exceptions.ConnectionError`: 针对网络相关错误（例如，DNS 故障、连接被拒绝等）抛出。这是 `ProxyError` 和 `SSLError` 的基类。
*   `requests.exceptions.Timeout`: 超时异常的基类，如果您想同时捕获连接超时和读取超时，则很有用。
*   `requests.exceptions.ConnectTimeout`: 尝试连接到远程服务器时请求超时。
*   `requests.exceptions.ReadTimeout`: 连接建立后，服务器在规定时间内未发送任何数据。
*   `requests.exceptions.SSLError`: 请求期间发生 SSL 错误。
*   `requests.exceptions.ProxyError`: 发生代理连接错误。

```python
import requests

try:
    # 通过尝试连接到不存在的主机或拒绝连接的服务来模拟连接错误。
    # 对于超时，请设置一个非常短的超时值。
    response = requests.get('http://nonexistent-domain-12345.com', timeout=0.001)
    print("Request successful!")
except requests.exceptions.ConnectTimeout:
    print("Connection timed out!")
except requests.exceptions.ReadTimeout:
    print("Server did not send data in time!")
except requests.exceptions.ConnectionError as e:
    print(f"Network/Connection Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

此代码区分各种网络相关问题，使您能够实现特定的重试逻辑或回退机制。

### URL 和请求配置错误

这些错误通常源于无效的 URL 或不正确的请求参数，甚至在请求通过网络发送之前就会发生：

*   `requests.exceptions.MissingSchema`: 缺少 URL 方案（例如，`http` 或 `https`）。
*   `requests.exceptions.InvalidURL`: 提供的 URL 存在某种无效（例如，主机格式错误）。
*   `requests.exceptions.InvalidSchema`: 提供的 URL 方案无效或不支持。
*   `requests.exceptions.URLRequired`: 发出请求需要一个有效的 URL。
*   `requests.exceptions.TooManyRedirects`: 超出最大重定向次数。
*   `requests.exceptions.InvalidHeader`: 提供的标头值存在某种无效。

```python
import requests

try:
    # 此 URL 缺少方案
    requests.get('www.example.com/path')
except requests.exceptions.MissingSchema as e:
    print(f"URL Scheme Error: {e}")

try:
    # 此 URL 格式错误
    requests.get('http://[::1]:invalid-port/')
except requests.exceptions.InvalidURL as e:
    print(f"Invalid URL Error: {e}")

try:
    # 重定向过多，默认限制为 30
    requests.get('https://httpbin.org/redirect/31')
except requests.exceptions.TooManyRedirects as e:
    print(f"Too many redirects: {e}")
```

这些示例展示了如何捕获与请求设置相关的错误，从而允许您验证输入或调整请求参数。

### 内容解码错误

当 Requests 在解码响应正文时遇到问题，例如 JSON 解析问题或一般内容解码问题时，会发生这些异常。

*   `requests.exceptions.JSONDecodeError`: 无法将文本解码为 JSON。
*   `requests.exceptions.ContentDecodingError`: 未能解码响应内容。
*   `requests.exceptions.ChunkedEncodingError`: 服务器声明了分块编码但发送了无效块。
*   `requests.exceptions.StreamConsumedError`: 此响应的内容已被消耗（例如，在不流式传输时尝试迭代响应两次）。

```python
import requests

try:
    # 此端点返回纯文本，而非 JSON
    response = requests.get('https://httpbin.org/plain')
    data = response.json() # 这将抛出 JSONDecodeError
except requests.exceptions.JSONDecodeError as e:
    print(f"JSON Decoding Error: {e}")
    print(f"Response text: {response.text[:50]}...")

try:
    # 模拟内容解码失败的场景
    # （这更难通过 httpbin 直接重现）
    # 一个有效的用例是服务器发送了格式错误的压缩数据。
    response = requests.get('https://httpbin.org/image/png', stream=True)
    # 如果可能，手动触发内容解码问题，或说明概念
    # 如果 PNG 数据损坏，这可能会抛出 ContentDecodingError
    _ = response.content # 访问内容可能会触发解码
except requests.exceptions.ContentDecodingError as e:
    print(f"Content Decoding Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

本节重点介绍了如何处理解释响应正文时的问题，这对于依赖结构化数据格式的应用程序至关重要。

### 其他特定错误

Requests 还为更小众的场景定义了其他特定异常：

*   `requests.exceptions.RetryError`: 如果自定义重试逻辑失败则抛出。
*   `requests.exceptions.UnrewindableBodyError`: Requests 在尝试回绕正文（通常是文件类对象）时遇到错误，这在重定向或重试期间发生。

```python
import requests
from requests.exceptions import RetryError

# RetryError 示例（需要自定义重试逻辑，这超出了 Requests 的基本用法）
# 这是一个占位符，用于演示当您的重试机制抛出此异常时如何捕获它。

try:
    # 假设这里有一些涉及重试的自定义逻辑
    # 如果该逻辑失败并抛出 RetryError：
    raise RetryError("Custom retry attempt failed after multiple tries")
except RetryError as e:
    print(f"Custom Retry Logic Failed: {e}")

# UnrewindableBodyError 示例（在简单的 GET/POST 中较不常见，在大文件上传中较常见）
# 如果您提供一个不可查找的文件类对象作为正文，并且发生重定向/重试
class NonSeekableFile:
    def read(self, n):
        return b'data' * n
    # 没有 .tell() 或 .seek() 方法

try:
    # 如果请求在重定向期间尝试回绕它（例如，POST 带有不可查找的正文到重定向），这可能会抛出 UnrewindableBodyError
    requests.post('https://httpbin.org/redirect-to?url=https://httpbin.org/post', data=NonSeekableFile())
except requests.exceptions.UnrewindableBodyError as e:
    print(f"Unrewindable Body Error: {e}")
except requests.exceptions.RequestException as e:
    print(f"An unexpected error occurred: {e}")
```

这些异常对于调试和处理与请求重试和流式正文相关的高级场景非常有用。

---

有效地处理异常是使用 Requests 构建健壮应用程序的基石。通过理解和捕获特定的错误类型，您可以实施精确的恢复策略，提供有意义的用户反馈，或记录问题以进行调试。有关每种异常类型及其继承层次结构的更深入探讨，请参阅[异常](./api-reference-exceptions.md)部分。