# 代理

在进行 HTTP 请求时，您可能出于各种原因需要通过代理服务器路由流量，例如网络安全、隐私或访问防火墙后的资源。Requests 提供了灵活的方式来配置和管理代理，无论是直接针对单个请求、通过持久化会话（Session），还是利用环境变量。

有关管理持久化连接和设置的常规信息，请参阅[会话对象](./api-reference-session-object.md)部分。要了解不同类型的请求，请参阅[HTTP 方法](./core-concepts-http-methods.md)。

## 设置代理

您可以使用一个字典为请求指定代理，该字典将 URL 方案映射到代理 URL。Requests 支持 HTTP、HTTPS 和 SOCKS 代理。

### 按请求设置代理

要为单个请求使用代理，请将 `proxies` 字典传递给请求方法（例如，`requests.get()`、`requests.post()`）。

**参数**

| 名称 | 类型 | 描述 |
|---|---|---|
| `proxies` | `dict` | 一个将协议或协议和主机映射到代理 URL 的字典（例如，`{'http': 'http://10.10.1.10:1234', 'https': 'http://10.10.1.10:4321'}`）。 |

**示例**

```python
import requests

proxies = {
    'http': 'http://10.10.1.10:1234',
    'https': 'http://10.10.1.10:4321',
}

try:
    response = requests.get('http://example.com', proxies=proxies)
    print(f"Response status code: {response.status_code}")
except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via proxy: {e}")
```

此示例为 `http://example.com` 的 GET 请求配置了 HTTP 和 HTTPS 代理。

### 会话级代理

为了在多个请求中保持持久的代理设置，请在 `Session` 对象上配置 `proxies` 属性。这样做更高效，因为它重用了底层的连接池。

**示例**

```python
import requests

s = requests.Session()
s.proxies = {
    'http': 'http://10.10.1.10:1234',
    'https': 'http://10.10.1.10:4321',
}

try:
    response1 = s.get('http://example.com/page1')
    print(f"Page 1 status: {response1.status_code}")

    response2 = s.get('https://example.com/page2')
    print(f"Page 2 status: {response2.status_code}")

except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via session proxy: {e}")
finally:
    s.close()
```

在此示例中，两个 `s.get()` 调用都将使用在 `Session` 对象上配置的代理。

## 环境变量和 `NO_PROXY`

Requests 可以自动检测并使用来自环境变量的代理设置。此行为由 `Session` 对象的 `trust_env` 属性控制，该属性默认为 `True`。

### 自动代理检测

Requests 检查以下环境变量（不区分大小写）以确定代理设置：

*   `HTTP_PROXY` 或 `http_proxy`：用于 HTTP 请求。
*   `HTTPS_PROXY` 或 `https_proxy`：用于 HTTPS 请求。
*   `ALL_PROXY` 或 `all_proxy`：如果未定义特定代理，则为任何方案的备用代理。

如果 `trust_env` 为 `True`（默认值），Requests 将把这些环境变量代理与在 Session 上或按请求显式设置的任何代理合并。显式定义的代理始终具有优先权。

### 绕过代理 (`NO_PROXY`)

`NO_PROXY` 环境变量（或 `no_proxy`）允许您指定一个主机名或 IP 地址列表，对于这些地址不应使用代理。这对于不需要代理的内部网络地址或本地主机非常有用。

Requests 评估 `NO_PROXY` 规则以确定给定 URL 是否应绕过代理。绕过逻辑支持：

*   **主机名匹配**：如果 URL 的主机名以 `NO_PROXY` 中的任何条目结尾（例如，`NO_PROXY` 中的 `example.com` 将绕过 `www.example.com`）。
*   **IP 地址匹配**：IPv4 地址的直接 IP 匹配。
*   **CIDR 表示法**：以 CIDR 格式指定的 IP 范围（例如，`192.168.1.0/24`）。

**`NO_PROXY` 示例**

如果您在环境中设置 `NO_PROXY`：

```bash
# 在 Linux/macOS 上
export HTTP_PROXY="http://yourproxy:8080"
export NO_PROXY="localhost,127.0.0.1,example.internal,192.168.1.0/24"

# 在 Windows (PowerShell) 上
$env:HTTP_PROXY="http://yourproxy:8080"
$env:NO_PROXY="localhost,127.0.0.1,example.internal,192.168.1.0/24"
```

Requests 将自动对外部 URL 使用 `HTTP_PROXY`，但对于 `localhost`、`127.0.0.1`、任何以 `example.internal` 结尾的地址或 `192.168.1.0/24` 内的 IP 地址，将绕过代理。

Here is how Requests resolves which proxy to use for a given request:

```mermaid
flowchart TD
    A["开始请求"] --> B{"是否提供了 'proxies' 参数？"}
    B -- "是" --> C["使用 'proxies' 参数"]
    B -- "否" --> D{"是否设置了 Session 的 'proxies' 属性？"}
    D -- "是" --> E["使用 Session 的 'proxies'"]
    D -- "否" --> F{"Session 的 'trust_env' 是否为 True？"}
    F -- "是" --> G["检查环境变量代理\n(HTTP_PROXY, HTTPS_PROXY, ALL_PROXY)"]
    F -- "否" --> I["无环境变量代理"]
    G --> H["将环境变量代理与当前设置合并"]
    C --> K["解析最终代理 (请求 + 会话 + 环境变量)"]
    E --> K
    H --> K
    I --> K
    K --> L{"根据 URL 评估 'NO_PROXY' 规则"}
    L -- "URL 匹配 NO_PROXY" --> M["绕过代理"]
    L -- "URL 不匹配 NO_PROXY" --> N["按 URL 方案选择代理"]
    M --> O["直接发送请求"]
    N --> P["通过选定代理发送请求"]
    O --> Q["接收响应"]
    P --> Q
```

## 代理认证

如果您的代理服务器需要认证，您可以直接在代理 URL 中包含用户名和密码。Requests 将自动构建并发送 `Proxy-Authorization` 头部。

**示例**

```python
import requests

proxies = {
    'http': 'http://user:password@proxy.example.com:8080/',
    'https': 'http://user:password@proxy.example.com:8080/',
}

try:
    response = requests.get('http://httpbin.org/get', proxies=proxies)
    print(f"Response status code with authenticated proxy: {response.status_code}")
except requests.exceptions.ProxyError as e:
    print(f"Proxy authentication failed or proxy unreachable: {e}")
```

此示例演示了直接在代理 URL 中指定凭据。

## SOCKS 代理

Requests 支持 SOCKS 代理。要使用它们，您需要安装 `PySocks` 包（`pip install "requests[socks]"`）。安装后，您可以使用 `socks5` 或 `socks4` 方案指定 SOCKS 代理 URL。

**示例**

```python
import requests

proxies = {
    'http': 'socks5://user:password@127.0.0.1:9050',
    'https': 'socks5://user:password@127.0.0.1:9050'
}

try:
    response = requests.get('http://example.com', proxies=proxies)
    print(f"Response status code via SOCKS proxy: {response.status_code}")
except requests.exceptions.InvalidSchema as e:
    print(f"SOCKS dependencies missing: {e}. Please install requests[socks].")
except requests.exceptions.ConnectionError as e:
    print(f"Could not connect via SOCKS proxy: {e}")
```

此示例配置 Requests 以使用带认证的 SOCKS5 代理。

---

理解并正确配置代理对于有效管理网络请求至关重要。Requests 提供了一个强大的系统，用于处理各种代理场景，从简单的按请求设置到带有绕过规则的复杂环境驱动配置。接下来，请在[SSL 验证和客户端证书](./advanced-usage-ssl-verification-client-certificates.md)部分探索如何通过管理 TLS 证书来确保安全通信。