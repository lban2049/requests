# 错误处理

当通过网络与外部服务交互时，您必须预料到可能会出现问题。Requests 使用异常来标志这些问题。本指南介绍了该库引发的常见异常以及如何在您的应用程序中优雅地处理它们。

所有 Requests 库特有的异常都继承自 `requests.exceptions.RequestException`，这使其成为一个方便的基类，可以捕获源自该库的任何错误。

### 异常层次结构

下图说明了 Requests 中一些最常见异常的继承层次结构。理解这个结构可以帮助您在正确的粒度级别上捕获异常。

```d2
direction: down

"IOError": {
  shape: class
}

"requests.exceptions.RequestException": {
  shape: class
  style {
    stroke: "#4285F4"
    stroke-width: 2
  }
}

"IOError" -> "requests.exceptions.RequestException"

sub_exceptions: {
  grid-columns: 3
  "HTTPError": { shape: class }
  "ConnectionError": { shape: class }
  "Timeout": { shape: class }
  "TooManyRedirects": { shape: class }
  "URLRequired": { shape: class }
  "InvalidJSONError": { shape: class }
}

"requests.exceptions.RequestException" -> sub_exceptions

connection_sub: {
  "ProxyError": { shape: class }
  "SSLError": { shape: class }
}
"ConnectionError" -> connection_sub

timeout_sub: {
  "ConnectTimeout": { shape: class }
  "ReadTimeout": { shape: class }
}
"Timeout" -> timeout_sub
"ConnectionError" -> timeout_sub.ConnectTimeout
```


### 使用 `HTTPError` 处理错误的状码

当服务器返回不成功的 HTTP 状态码（在 4xx 或 5xx 范围内）时，Requests 不会自动引发异常。但是，您可以使用 `response.raise_for_status()` 方法来引发异常。如果响应的状态码错误，该方法将引发一个 `HTTPError`。

```python
import requests

for url in ['https://httpbin.org/get', 'https://httpbin.org/status/500']:
    try:
        response = requests.get(url)
        # 如果响应成功，则不会引发异常
        response.raise_for_status()
    except requests.exceptions.HTTPError as errh:
        print(f"Http 错误: {errh}")
        # 您可以检查异常对象上的响应
        if errh.response is not None:
            print(f"状态码: {errh.response.status_code}")
    except requests.exceptions.RequestException as err:
        print(f"其他错误: {err}")
    else:
        print(f"成功获取 {url}")

```
这种方法对于快速断言请求是否成功，同时集中处理客户端和服务器错误非常有用。

### 使用 `ConnectionError` 处理网络问题

如果您遇到网络级别的问题，例如 DNS 故障、连接被拒绝或代理错误，Requests 将会引发一个 `ConnectionError`。

```python
import requests

try:
    response = requests.get('https://not-a-real-domain.xyz')
except requests.exceptions.ConnectionError as errc:
    print(f"连接错误: {errc}")
```

更具体的异常，如 `ProxyError` 和 `SSLError`，继承自 `ConnectionError`，因此如果您需要不同地处理这些情况，可以单独捕获它们。

### 处理 `Timeout` 异常

请求可能会在两个不同的阶段超时：连接到服务器和从服务器读取数据。Requests 提供了一个基础的 `Timeout` 异常，可以同时捕获这两种情况。

- `ConnectTimeout`: 请求在尝试连接到远程服务器时超时。
- `ReadTimeout`: 服务器在指定的时间内没有发送任何数据。

```python
import requests

try:
    # httpbin.org/delay/3 将需要 3 秒才能响应
    response = requests.get('https://httpbin.org/delay/3', timeout=1)
except requests.exceptions.Timeout as errt:
    print(f"超时错误: {errt}")
```
在此示例中，请求将会超时，因为 `timeout` 参数（1 秒）小于服务器的响应时间（3 秒）。捕获 `requests.exceptions.Timeout` 是处理连接和读取超时的可靠方法。

### 使用 `TooManyRedirects` 处理重定向过多

默认情况下，Requests 将遵循最多 30 次重定向。如果超过此限制，它将引发一个 `TooManyRedirects` 异常以防止无限循环。

```python
import requests

# 这个 URL 的重定向次数将超过默认的 30 次限制。
try:
    response = requests.get('https://httpbin.org/redirect/31')
except requests.exceptions.TooManyRedirects as errr:
    print(f"重定向次数过多: {errr}")
```

### 捕获基础异常 `RequestException`

如果您想为 Requests 库可能抛出的任何异常创建一个通用的处理程序，您可以捕获基础的 `requests.exceptions.RequestException`。

```python
import requests

try:
    response = requests.get('https://invalid-schema://example.com')
    response.raise_for_status()
except requests.exceptions.RequestException as err:
    print(f"请求出现问题: {err}")
```

### 常见异常快速参考

以下是您可能遇到的最常见异常的摘要表。

| Exception | Description |
|---|---|
| `requests.exceptions.RequestException` | 基础异常类。Requests 引发的所有其他异常都继承自它。 |
| `requests.exceptions.HTTPError` | 当调用 `response.raise_for_status()` 时，针对不成功的状态码（4xx 或 5xx）引发。 |
| `requests.exceptions.ConnectionError` | 针对网络相关问题（如 DNS 故障或连接被拒）引发。 |
| `requests.exceptions.ProxyError` | `ConnectionError` 的子类，针对代理特定问题引发。 |
| `requests.exceptions.SSLError` | `ConnectionError` 的子类，针对 SSL 握手失败引发。 |
| `requests.exceptions.Timeout` | 请求超时时的基础异常。 |
| `requests.exceptions.ConnectTimeout` | 在建立连接超时时引发。这些请求可以安全地重试。 |
| `requests.exceptions.ReadTimeout` | 当服务器在指定的超时期限内未发送数据时引发。 |
| `requests.exceptions.TooManyRedirects` | 当请求超过允许的最大重定向次数时引发。 |
| `requests.exceptions.MissingSchema` | 当提供的 URL 没有协议方案（例如 `http://` 或 `https://`）时引发。 |
| `requests.exceptions.InvalidURL` | 如果提供的 URL 在其他方面格式不正确时引发。 |
| `requests.exceptions.JSONDecodeError` | 当 `response.json()` 无法将响应内容解析为有效的 JSON 时引发。 |

通过预见这些常见错误，您可以构建更具弹性和可靠性的应用程序。要了解如何配置网络行为（如超时和重试），请参阅[高级用法](./advanced-usage.md)部分。