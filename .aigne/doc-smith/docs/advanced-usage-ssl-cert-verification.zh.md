# SSL 证书验证

Requests 默认会为 HTTPS 请求验证 SSL 证书，以防止中间人攻击。此验证依赖于一个受信任的证书颁发机构 (CAs) 系统，其工作方式与你的网络浏览器类似。本节将介绍如何在不同场景下管理此行为，例如使用私有 CA 或客户端证书。

## 默认 CA 验证

当你向 `https://` URL 发出请求时，Requests 会检查服务器的证书是否有效且受信任。此行为默认启用。

```python
import requests

# 这段代码可以直接运行，因为 httpbin.org 拥有一个有效且受信任的证书。
response = requests.get('https://httpbin.org/get')
print(response.status_code)
# 200
```

Requests 内部使用 `certifi` 包来提供其默认的受信任根证书集。你可以通过以下方式找到其使用的 CA 包路径：

```python
from certifi import where

print(where())
# /path/to/your/virtualenv/lib/pythonX.X/site-packages/certifi/cacert.pem
```

## 自定义 CA 包

如果你交互的服务使用了私有或自签名证书（例如在企业或测试环境中），你可以通过 `verify` 参数提供一个特定 CA 包的路径，来让 Requests 信任该证书。

```python
# 使用单个 CA 包文件 (.pem)
requests.get('https://internal.service.com', verify='/path/to/ca.pem')

# 使用一个包含多个 CA 证书的目录
requests.get('https://internal.service.com', verify='/path/to/certs/')
```

为方便起见，你也可以在 `Session` 对象上进行设置，这样该会话发出的所有后续请求都会应用此设置。

```python
import requests

s = requests.Session()
s.verify = '/path/to/ca.pem'

# 此请求将使用你的自定义 CA 包
response = s.get('https://another.internal.service.com')
```

此外，如果你的代码中没有显式设置 `verify` 参数，Requests 会遵循 `REQUESTS_CA_BUNDLE` 和 `CURL_CA_BUNDLE` 这两个环境变量。

## 客户端证书

有些服务要求客户端使用证书进行身份验证，这一过程称为双向 TLS (mTLS)。你可以使用 `cert` 参数来提供客户端证书。

如果你的证书和私钥合并在单个文件中：

```python
requests.get(
    'https://api.service.com/resource',
    cert='/path/to/client.pem'
)
```

如果你的证书和密钥是独立文件，请将它们以元组形式传入：

```python
requests.get(
    'https://api.service.com/resource',
    cert=('/path/to/client.crt', '/path/to/client.key')
)
```

与 `verify` 设置类似，`cert` 也可以在 `Session` 对象上配置，以应用于该会话中的所有请求。

## 禁用验证

在本地开发或完全受信任的内部网络中，你可能需要跳过 SSL 验证。可以通过设置 `verify=False` 来实现。

**警告**：此操作不安全，不应在生产环境中使用，因为它会使你的应用程序易受中间人攻击。

```python
import requests

# 这将禁用证书验证。
# 这可能会导致 urllib3 发出 InsecureRequestWarning 警告。
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

## 验证工作流

下图说明了 Requests 如何决定使用哪种验证方法。

```d2
direction: down

start: "开始请求"
is_https: "URL 协议是 HTTPS？" {
  shape: diamond
}
no_tls: "不使用 TLS 继续"
verify_false: "verify=False？" {
    shape: diamond
}
disable_verify: "禁用验证 (不安全)" {
    style.fill: "#fce7c6"
}
is_path: "verify 是一个路径？" {
    shape: diamond
}
custom_ca: "使用路径下的自定义 CA 包"
default_ca: "使用默认 CA 包 (certifi)"
make_request: "发出请求"
end: "结束"

start -> is_https
is_https -- "No" -> no_tls
is_https -- "Yes" -> verify_false

verify_false -- "Yes" -> disable_verify
verify_false -- "No" -> is_path

is_path -- "Yes" -> custom_ca
is_path -- "No" -> default_ca

disable_verify -> make_request
custom_ca -> make_request
default_ca -> make_request
no_tls -> end
make_request -> end
```

现在，你可以为各种场景管理 SSL/TLS 证书验证，包括使用自定义 CA 和提供客户端证书。如需更深入地自定义 Requests 处理网络连接的方式，请参阅下一节关于[自定义适配器和钩子](./advanced-usage-adapters-and-hooks.md)的内容。
