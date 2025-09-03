# 超时、重试和代理

优化网络行为对于构建能够应对不可靠网络状况和复杂企业环境的弹性应用程序至关重要。本指南将介绍如何配置请求超时、连接失败时的自动重试，以及如何通过代理路由请求。

## 超时

你可以配置 Requests 在等待指定秒数后停止等待响应。`timeout` 参数可以防止程序在发生网络问题时无限期挂起。

对外部服务器的大多数请求都应设置超时。如果没有超时设置，代码可能会挂起数分钟甚至更长时间。

```python
import requests

# 最多等待 2.5 秒，超时则放弃
requests.get('https://httpbin.org/delay/3', timeout=2.5)
# 引发 ReadTimeout 错误
```

`timeout` 值可以是一个浮点数，表示等待服务器发送数据的总时间。若要进行更精细的控制，可以提供一个包含两个浮点数的元组：`(connect_timeout, read_timeout)`。

- **连接超时**：允许客户端与服务器建立连接的时间。
- **读取超时**：建立连接后，允许客户端从服务器接收数据的时间。

```python
import requests

# 1 秒用于连接，3 秒用于等待响应的第一个字节
r = requests.get('https://httpbin.org/delay/2', timeout=(1.0, 3.0))
print(r.status_code)

# 这将引发 ConnectTimeout
try:
    requests.get('https://httpbin.org/', timeout=(0.001, 3.0))
except requests.exceptions.ConnectTimeout:
    print("Connection timed out.")
```

如果希望无限期等待，可以将 `None` 作为超时值传入。但是，通常不建议在生产代码中使用此方法。


## 重试

默认情况下，Requests 不会重试失败的连接。要实现重试策略，需要使用 `Session` 对象并挂载一个配置了重试策略的自定义 `HTTPAdapter`。

`HTTPAdapter` 允许你为连接指定最大重试次数。此重试逻辑适用于 DNS 查找、套接字连接错误和连接超时等特定故障，但不适用于已向服务器发送数据的请求。

以下是如何配置 `Session` 以便最多重试请求 3 次：

```python
import requests
from requests.adapters import HTTPAdapter

# 创建一个 session 对象
s = requests.Session()

# 创建一个带重试策略的适配器
# 在本例中，它将在连接失败时重试 3 次
a = HTTPAdapter(max_retries=3)

# 将适配器挂载到 session 上，适配 http 和 https 前缀
s.mount('http://', a)
s.mount('https://', a)

# 使用 session 发出请求
try:
    response = s.get('http://a.non.existent.domain/')
except requests.exceptions.ConnectionError as e:
    print(f"Failed after multiple retries: {e}")

```

若要更精细地控制重试的状态码或实现指数退避，可以导入并配置 `urllib3.util.retry.Retry`，然后将其一个实例传递给 `max_retries` 参数。


## 代理

如果需要通过代理服务器路由请求，可以使用 `proxies` 参数。

### 基本代理用法

`proxies` 参数是一个字典，用于将协议方案映射到代理的 URL。

```python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

requests.get('http://example.org', proxies=proxies)
```

### 身份验证

如果代理需要身份验证，可以将其包含在代理 URL 中：

```python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}
```

### SOCKS 代理

要使用 SOCKS 代理，需要安装 `PySocks` 库：

```bash
pip install pysocks
```

安装后，可以将代理方案指定为 `socks5`、`socks5h`、`socks4` 或 `socks4a`。

```python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('http://example.org', proxies=proxies)
```
`socks5h` 表示 DNS 解析应在代理服务器上进行，这通常是所期望的行为。

### 环境变量

Requests 会自动从 `HTTP_PROXY`、`HTTPS_PROXY` 和 `NO_PROXY` 等环境变量中读取并使用代理设置。可以在 `Session` 对象上设置 `trust_env=False` 来禁用此行为。

```bash
export HTTP_PROXY="http://10.10.1.10:3128"
export HTTPS_PROXY="http://10.10.1.10:1080"
```

设置这些变量后，以下 Python 代码将自动使用已定义的代理，无需再传入 `proxies` 参数：

```python
import requests

# 此请求将通过 http://10.10.1.10:3128 发送
requests.get('http://example.org') 
```

### 绕过代理

可以使用 `NO_PROXY` 环境变量指定应绕过代理的主机。该变量应为以逗号分隔的域名、域后缀或 IP 地址列表。

例如，要为 `internal.example.com` 以及 `192.168.0.0/16` 网络中的所有主机绕过代理：

```bash
export NO_PROXY="internal.example.com,192.168.0.0/16"
```

Requests 会检查此变量，并将发往匹配主机的请求直接发送，从而绕过已配置的代理。

---

通过这些配置，你可以构建出更稳健的应用程序，从而从容地处理网络故障并在各种网络架构下运行。要保护连接安全，请继续阅读下一节关于 [SSL 证书验证](./advanced-usage-ssl-cert-verification.md) 的内容。
