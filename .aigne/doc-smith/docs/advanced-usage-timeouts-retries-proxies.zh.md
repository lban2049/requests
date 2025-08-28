# 超时、重试和代理

对于构建与 Web 服务交互的稳健应用程序而言，控制网络行为至关重要。本节将介绍如何配置超时以防止程序无限期挂起、如何为暂时性网络故障设置自动重试，以及如何通过代理路由请求。

## 超时

你可以使用 `timeout` 参数，告知 Requests 在等待指定秒数后停止等待响应。在大多数情况下，这是一项至关重要的设置，可防止程序在出现网络问题时无限期挂起。

`timeout` 值同时适用于请求的连接和读取两个阶段。

```python
# 为整个请求设置 5 秒的超时时间
response = requests.get('https://api.github.com/events', timeout=5)
```

若要进行更精细的控制，可以通过传递一个元组来分别指定连接超时和读取超时：

*   **连接超时**：允许客户端与服务器建立连接的时间。
*   **读取超时**：建立连接后，允许客户端等待服务器响应的时间。

```python
# 等待 3.05 秒以建立连接，然后等待 27 秒以接收服务器的响应
response = requests.get('https://api.github.com/events', timeout=(3.05, 27))
```

如果达到超时时间，Requests 将会抛出 `Timeout` 异常。你可以捕获此异常以妥善处理错误。

```python
import requests
from requests.exceptions import Timeout

try:
    response = requests.get('https://api.github.com/events', timeout=0.001)
except Timeout:
    print('请求超时。')
```

## 重试

默认情况下，Requests 不会自动重试失败的请求。若要实现重试策略，你需要使用自定义的 `HTTPAdapter`，并将其挂载到 `Session` 对象上。

### 简单重试

配置重试最简单的方法是为 `HTTPAdapter` 的 `max_retries` 参数提供一个整数。这将为失败的 DNS 查找、套接字连接和连接超时应用默认的重试机制。

```python
import requests
from requests.adapters import HTTPAdapter

# 创建一个会话
s = requests.Session()

# 配置一个采用简单重试策略的适配器
# 对于连接相关的错误，这将最多重试请求 3 次。
a = HTTPAdapter(max_retries=3)

# 为 HTTP 和 HTTPS 将适配器挂载到会话上
s.mount('http://', a)
s.mount('https://', a)

try:
    # 此请求将失败，但适配器会重试 3 次
    response = s.get('http://a-domain-that-does-not-exist.com')
except requests.exceptions.ConnectionError as e:
    print(f'多次重试后请求失败: {e}')
```

### 高级重试

若要进行更高级的控制，例如针对特定 HTTP 状态码进行重试或实现退避延迟，你可以将一个 `urllib3.util.retry.Retry` 对象传递给 `max_retries`。这使你能够对重试行为进行精细控制。

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

s = requests.Session()

# 配置一个更稳健的重试策略
retries = Retry(total=5,
                backoff_factor=0.1,
                status_forcelist=[ 500, 502, 503, 504 ])

adapter = HTTPAdapter(max_retries=retries)

s.mount('http://', adapter)
s.mount('https://', adapter)

try:
    # 这将在遇到 503 错误时以退避延迟的方式进行重试
    response = s.get('http://httpbin.org/status/503')
    response.raise_for_status()
except requests.exceptions.RequestException as e:
    print(f'请求失败: {e}')
```

以下是带有退避因子的重试流程示意图：

```d2
shape: sequence_diagram

客户端
"带适配器的会话"
服务器

客户端 -> "带适配器的会话": s.get('http://service.com/api')
"带适配器的会话" -> 服务器: GET /api
服务器 -> "带适配器的会话": 503 服务不可用

"带适配器的会话": {
  note: "状态码在强制重试列表中。以退避方式启动重试（例如，等待 0.1 秒）。"
}

"带适配器的会话" -> 服务器: GET /api (重试 1)
服务器 -> "带适配器的会话": 503 服务不可用

"带适配器的会话": {
  note: "状态码在强制重试列表中。以增加的退避时间启动重试（例如，等待 0.2 秒）。"
}

"带适配器的会话" -> 服务器: GET /api (重试 2)
服务器 -> "带适配器的会话": 200 OK
"带适配器的会话" -> 客户端: 响应 (200 OK)
```

## 代理

如果你需要通过代理服务器路由请求，可以在任何请求方法中使用 `proxies` 参数，或在 `Session` 对象上进行配置。

```python
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### 身份验证

如果你的代理需要身份验证，可以在代理 URL 中包含用户名和密码：

```python
proxies = {
   'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS 代理

Requests 也支持 SOCKS 代理，但你首先需要安装必要的第三方库：

```bash
pip install pysocks
```

安装后，你可以在代理 URL 中指定 SOCKS 协议方案。使用 `socks5` 进行本地 DNS 解析，或使用 `socks5h` 在代理服务器上解析 DNS。

```python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```

### 环境变量

如果未显式设置 `proxies` 参数，Requests 将自动使用环境变量中配置的代理。它会遵循 `HTTP_PROXY`、`HTTPS_PROXY` 和 `NO_PROXY` 的设置。

你可以在 shell 中配置这些变量：

```bash
export HTTP_PROXY="http://10.10.1.10:3128"
export HTTPS_PROXY="https://10.10.1.10:1080"

# 对特定主机、域或 IP 范围绕过代理
export NO_PROXY="localhost,127.0.0.1,example.com"
```

设置这些环境变量后，以下 Python 代码将自动使用已配置的代理，无需任何额外参数：

```python
# 此请求将通过 HTTPS_PROXY 中定义的代理发送
requests.get('https://httpbin.org/ip')

# 由于 NO_PROXY 的设置，此请求将绕过代理
requests.get('http://example.com')
```

掌握了这些网络配置后，你可以进一步增强连接的安全性。请在 [SSL 证书验证](./advanced-usage-ssl-cert-verification.md) 指南中了解更多信息。