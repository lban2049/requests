# 代理

如果您需要通过代理服务器路由 HTTP 请求，Requests 可以轻松实现。您可以为单个请求配置代理设置，也可以为整个 `Session` 对象进行配置。

## 基本用法

要使用代理，您可以向任何请求方法传递一个 `proxies` 字典。该字典应将 URL 协议（如 'http' 或 'httpss'）映射到代理的 URL。

```python Proxies Example icon=logos:python
import requests

proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}

response = requests.get('https://httpbin.org/get', proxies=proxies)
print(response.json())
```

在此示例中，对 `http://` URL 的请求将通过 `http://10.10.1.10:3128` 路由，而对 `https://` URL 的请求将通过 `http://10.10.1.10:1080`。

您还可以在 `Session` 对象上配置代理，以将其应用于通过该会话发出的所有请求：

```python Session with Proxies icon=logos:python
import requests

session = requests.Session()
session.proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'https://10.10.1.10:1080',
}

# All subsequent requests made with this session will use the configured proxies
response = session.get('https://httpbin.org/get')
```

## 代理身份验证

如果您的代理需要身份验证，您可以遵循标准的 `user:password@host:port` 语法，将用户名和密码包含在代理 URL 中。

```python Authenticated Proxy icon=logos:python
proxies = {
    'http': 'http://user:password@10.10.1.10:3128/',
}

requests.get('http://httpbin.org/get', proxies=proxies)
```

## SOCKS 代理

Requests 还支持 SOCKS 代理。此功能默认未启用，需要一个外部依赖项。您可以使用 pip 安装所需的包：

```bash
pip install requests[socks]
```

安装后，使用 SOCKS 代理就像使用 HTTP 代理一样简单。SOCKS 代理的协议可以是 `socks5` 或 `socks5h`。

- `socks5`: 代理您的请求，但 DNS 解析在您的本地计算机上进行。
- `socks5h`: DNS 解析由代理服务器执行，这对于访问无法从本地网络解析的主机非常有用。

```python SOCKS Proxy Example icon=logos:python
proxies = {
    'http': 'socks5h://user:pass@host:port',
    'https': 'socks5h://user:pass@host:port'
}

requests.get('https://httpbin.org/get', proxies=proxies)
```

## 环境变量

Requests 会自动检测并使用您系统环境变量中的代理设置。它会查找以下变量（首先检查小写版本）：

- `HTTP_PROXY` 或 `http_proxy`
- `HTTPS_PROXY` 或 `https_proxy`
- `ALL_PROXY` 或 `all_proxy`

如果设置了这些变量，Requests 将默认对所有请求使用它们。您可以通过传递 `proxies` 字典来为单个请求覆盖此设置，也可以为 `Session` 完全禁用它。

要禁用环境变量代理，请将 `Session` 对象上的 `trust_env` 属性设置为 `False`：

```python Disabling Environment Proxies icon=logos:python
import requests

# This session will ignore any proxy settings from environment variables
session = requests.Session()
session.trust_env = False

# This request will be sent directly, without a proxy
response = session.get('https://httpbin.org/get')
```

### 使用 `NO_PROXY` 绕过代理

您可以通过设置 `NO_PROXY`（或 `no_proxy`）环境变量来指定应绕过代理的主机。这应该是一个由逗号分隔的域名、主机名或 IP 地址列表。

例如：

```bash
export NO_PROXY="localhost,127.0.0.1,example.com,192.168.1.0/24"
```

通过此设置，任何对 `localhost`、`127.0.0.1`、`example.com`（或其任何子域）或 `192.168.1.0/24` 范围内的任何 IP 的请求都将直接发送，忽略代理设置。

## 高级代理选择

为了实现更精细的控制，`proxies` 字典允许您为特定的协议和主机名指定代理。在为给定 URL 选择代理时，Requests 会按以下顺序在 `proxies` 字典中搜索键：

1.  `scheme://hostname`（例如 `https://httpbin.org`）
2.  `scheme`（例如 `https`）
3.  `all://hostname`（例如 `all://httpbin.org`）
4.  `all`

这允许设置强大的路由规则。例如，您可以将特定域名的流量路由到一个代理，同时将所有其他流量发送到另一个代理。

```python Granular Proxy Rules icon=logos:python
proxies = {
    # Route traffic for this specific host to a SOCKS proxy
    'https://api.internal.corp': 'socks5://user:pass@host:port',

    # Route all other HTTPS traffic to a general HTTP proxy
    'https': 'http://proxy.example.com:8080'
}

# This request will use the SOCKS proxy
requests.get('https://api.internal.corp/data', proxies=proxies)

# This request will use the general HTTPS proxy
requests.get('https://httpbin.org/get', proxies=proxies)
```

既然您已经掌握了代理，您可能想了解 [SSL 证书验证](./advanced-usage-ssl-verification.md) 或如何使用 [Session 对象](./advanced-usage-session-objects.md) 在多个请求之间保持这些设置。