# 超时、重试和代理

要构建与 Web 服务交互的稳健应用程序，需要处理网络不稳定和多样的网络配置问题。Requests 提供了强大而直接的机制，用于控制连接超时、自动重试失败的请求以及通过代理路由流量。本指南将介绍如何配置这些高级网络行为，使你的应用程序更具弹性和适应性。

## 超时

通过设置超时，可以防止程序因网络请求而无限期挂起。大多数对外部服务器的请求都应附加超时设置。

默认情况下，除非显式设置 `timeout` 值，否则请求不会超时。如果没有超时设置，当服务器无响应时，你的代码可能会无限期挂起。

`timeout` 参数可以通过两种方式进行配置：

<x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="在放弃前等待服务器发送数据的时长。单个浮点数可同时设置连接和读取超时。元组可用于分别设置连接超时和读取超时，格式为 (connect_timeout, read_timeout)。"></x-field>

### 单一值超时

你可以为 `timeout` 指定一个单一的浮点数值，该值将同时应用于请求的 `connect` 和 `read` 阶段。

```python 设置全局超时 icon=logos:python
import requests

try:
    # 整个请求最多等待 3.05 秒
    response = requests.get('https://httpbin.org/delay/5', timeout=3.05)
except requests.exceptions.Timeout:
    print("请求超时。")

```

### 分别设置连接和读取超时

为了进行更精细的控制，你可以提供一个包含两个浮点数值的元组。第一个值是 `connect` 超时（建立初始连接所允许的时间），第二个值是 `read` 超时（从服务器接收字节之间所允许的时间）。

```python 分别设置连接和读取超时 icon=logos:python
import requests

try:
    # 等待 2 秒连接，等待 5 秒接收第一个字节
    response = requests.get('https://httpbin.org/delay/10', timeout=(2, 5))
except requests.exceptions.ConnectTimeout:
    print("连接超时。")
except requests.exceptions.ReadTimeout:
    print("读取超时。")

```

如果远程服务器非常慢，你可以通过将 `None` 作为超时值传递，来告知 Requests 永久等待响应。

## 重试

在遇到瞬时网络故障（如 DNS 查询失败或连接超时）时，你可能希望 Requests 自动重试请求。虽然 Requests 默认不执行此操作，但你可以使用 `HTTPAdapter` 配置此行为。

`HTTPAdapter` 上的 `max_retries` 参数允许你指定因连接相关错误而重试请求的次数。

```python 使用 HTTPAdapter 配置重试 icon=logos:python
import requests
from requests.adapters import HTTPAdapter

# 创建一个会话
session = requests.Session()

# 配置一个带重试策略的适配器
# 这将对失败的连接最多重试 3 次。
adapter = HTTPAdapter(max_retries=3)

# 将适配器挂载到会话上，同时支持 http 和 https
session.mount('http://', adapter)
session.mount('https://', adapter)

try:
    # 此请求将使用重试策略
    response = session.get('http://a.domain.that.does.not.exist')
except requests.exceptions.ConnectionError as e:
    print(f"多次重试后失败: {e}")

```

对于更复杂的重试逻辑（例如，自定义退避因子、根据特定 HTTP 状态码重试），你可以将 `urllib3.util.retry.Retry` 的实例传递给 `max_retries` 参数。

## 代理

如果你需要通过代理服务器路由请求，可以使用 `proxies` 参数。

<x-field data-name="proxies" data-type="dict" data-required="false" data-desc="一个将协议方案（如 'http'、'https'）映射到代理 URL 的字典。"></x-field>

### 基本代理

你可以通过向 `proxies` 参数传递一个字典，为单个请求配置代理。

```python 使用 HTTP 和 HTTPS 代理 icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https' 'http://10.10.1.10:1080',
}

# 此请求将通过 http 代理发送
requests.get('http://httpbin.org/get', proxies=proxies)

# 此请求将通过 https 代理发送
requests.get('https://httpbin.org/get', proxies=proxies)
```

### 环境变量

Requests 也遵循标准的代理环境变量，如 `HTTP_PROXY`、`HTTPS_PROXY` 和 `NO_PROXY`。如果设置了这些变量，Requests 会在 `trust_env` 为 `True`（默认值）的 `Session` 对象中自动使用它们。你仍然可以通过显式传递 `proxies` 参数来覆盖这些设置。

### 认证

如果你的代理需要认证，可以将凭据包含在代理 URL 中。

```python 使用基本认证的代理 icon=logos:python
import requests

proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://httpbin.org/get', proxies=proxies)
```

### SOCKS 代理

要使用 SOCKS 代理，你需要安装一个额外的依赖项。

```bash 终端 icon=lucide:terminal
pip install pysocks
```

安装后，你可以在代理 URL 中使用 `socks5` 或 `socks5h` 方案。`socks5h` 表示 DNS 解析也应由代理处理。

```python 使用 SOCKS 代理 icon=logos:python
import requests

proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

---

借助这些工具，你可以微调应用程序的网络行为，以优雅地处理各种情况。要进行更高级的网络控制，你可能需要了解如何管理 SSL 证书。

<x-card data-title="下一步：SSL 证书验证" data-href="/advanced-usage/ssl-cert-verification" data-icon="lucide:shield-check">
  学习如何管理 SSL/TLS 验证、使用自定义 CA 捆绑包或提供客户端证书。
</x-card>