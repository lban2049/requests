# 超时

大多数对外部服务器的请求都应设置超时，以防服务器未能及时响应。如果没有超时，你的代码可能会挂起几分钟甚至更长时间。设置超时可以确保你的应用程序保持响应灵敏，并能优雅地处理网络问题。

## 基本超时

你可以通过 `timeout` 参数告知 Requests 在等待响应多少秒后停止。这个单一的值将同时应用于 `connect` 和 `read` 超时。

```python Timeout Example icon=logos:python
import requests

try:
    # 最多等待 2.5 秒让服务器响应
    response = requests.get('https://httpbin.org/delay/5', timeout=2.5)
    print("Request successful!")
except requests.exceptions.Timeout:
    print("The request timed out.")

```

在此示例中，请求将会超时，因为服务器被配置为在响应前等待 5 秒，但我们的超时时间设置为 2.5 秒。当发生超时时，Requests 会引发一个 `requests.exceptions.Timeout` 异常。

## 精细控制超时

为了进行更精细的控制，你可以通过向 `timeout` 参数传递一个元组来分别指定 `connect` 和 `read` 超时。

- **连接超时**：允许客户端与服务器建立连接的时间。
- **读取超时**：连接建立后，允许客户端等待服务器发送响应的时间。

```python Connect and Read Timeouts icon=logos:python
import requests
from requests.exceptions import ConnectTimeout, ReadTimeout

try:
    # 3.05 秒用于连接，5 秒用于等待响应
    response = requests.get('https://httpbin.org/delay/10', timeout=(3.05, 5))
    print("Request successful!")
except ConnectTimeout:
    print("Connection timed out. The server did not respond to the connection request in time.")
except ReadTimeout:
    print("Read timed out. The server did not send any data in the allotted time.")

```

如果远程服务器启动发送数据的速度非常慢，你可以设置一个较长的 `read` 超时，同时保持较低的 `connect` 超时，以便快速检测服务器是否宕机。

## 永久等待

如果你想无限期地等待响应，可以将 `timeout` 设置为 `None`。这是默认行为，但在生产环境中强烈不建议这样做，因为它可能导致你的应用程序挂起。

```python No Timeout (Wait Forever) icon=logos:python
# 该请求将一直挂起，直到服务器响应，这可能永远不会发生。
# 请谨慎使用。
response = requests.get('https://httpbin.org/delay/10', timeout=None)
```

---

正确配置超时是构建健壮应用程序的关键一步。要了解更多关于处理这些及其他潜在问题的信息，请继续阅读下一节关于[错误处理](./advanced-usage-error-handling.md)的内容。