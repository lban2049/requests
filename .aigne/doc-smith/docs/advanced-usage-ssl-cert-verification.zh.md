# SSL 证书验证

Requests 默认会为 HTTPS 请求验证 SSL 证书，这是一项防止中间人攻击的关键安全功能。这种验证依赖于一个受信任的证书颁发机构 (CA) 系统，与网络浏览器类似。本节将介绍如何管理 SSL/TLS 验证，内容涵盖使用自定义 CA 到提供用于身份验证的客户端证书。

## 默认 CA 验证

默认情况下，当你向一个 `https://` URL 发出请求时，Requests 会根据一组受信任的 CA 证书包来验证服务器的证书。

```python
import requests

# 此请求会成功，因为 httpbin.org 拥有受默认 CA 证书包信任的有效证书。
response = requests.get('https://httpbin.org/get')
print(response.status_code)
# 200
```

Requests 使用 `certifi` 包来提供这套默认的受信任根证书。你可以找到它使用的 CA 证书包文件：

```python
import certifi

print(certifi.where())
# /path/to/your/virtualenv/lib/pythonX.X/site-packages/certifi/cacert.pem
```

## 自定义 CA 证书包

在企业或开发环境中，你可能需要连接到使用私有或自签名证书的服务。你可以通过向 `verify` 参数传入 CA 证书包文件的路径或证书目录的路径，来让 Requests 信任一组特定的 CA。

```python
# 使用单个 CA 证书包文件 (.pem)
requests.get('https://internal.service.com', verify='/path/to/ca.pem')

# 使用包含多个 CA 证书的目录
requests.get('https://internal.service.com', verify='/path/to/certs/')
```

要在多个请求中保持此设置，你可以在一个 `Session` 对象上进行配置：

```python
import requests

s = requests.Session()
s.verify = '/path/to/ca.pem'

# 使用此会话发出的所有请求都将使用自定义 CA 证书包
response = s.get('https://another.internal.service.com')
```

另外，如果在你的代码中没有设置 `verify`，Requests 会自动使用 `REQUESTS_CA_BUNDLE` 或 `CURL_CA_BUNDLE` 环境变量中指定的 CA 证书包。

## 客户端证书

某些服务要求客户端提供自己的证书进行身份验证，这个过程称为双向 TLS (mTLS)。你可以使用 `cert` 参数来提供客户端证书。

如果你的私钥和证书在同一个文件中：

```python
requests.get(
    'https://api.service.com/resource',
    cert='/path/to/client.pem'
)
```

如果密钥和证书是分开的文件，将它们以元组的形式传入：

```python
requests.get(
    'https://api.service.com/resource',
    cert=('/path/to/client.crt', '/path/to/client.key')
)
```

与 `verify` 设置一样，你可以在 `Session` 对象上设置 `cert`，以将其应用于该会话中发出的所有请求。

## 禁用验证

对于本地测试或在完全受信任的网络上，你可能需要禁用 SSL 证书验证。你可以通过设置 `verify=False` 来实现。 

**警告**：这样做非常不安全，绝不应在生产环境中使用。禁用验证会使你的应用程序面临中间人攻击的风险。

```python
import requests

# 这将禁用证书验证，并可能触发 InsecureRequestWarning。
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

## 验证工作流

下图说明了 Requests 如何确定对 HTTPS 请求使用哪种验证方法。

```d2
direction: down

start: "开始请求"
is_https: "URL 是否为 HTTPS？" {
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
custom_ca: "使用自定义 CA 证书包"
default_ca: "使用默认 CA 证书包 (certifi)"
make_request: "发出请求"
end: "结束"

start -> is_https
is_https -- "否" -> no_tls
is_https -- "是" -> verify_false

verify_false -- "是" -> disable_verify
verify_false -- "否" -> is_path

is_path -- "是" -> custom_ca
is_path -- "否" -> default_ca

disable_verify -> make_request
custom_ca -> make_request
default_ca -> make_request
no_tls -> end
make_request -> end
```

现在，你已经掌握了在各种场景下处理 SSL/TLS 证书验证的方法。要更深入地控制网络行为，请参阅下一节关于[自定义适配器和钩子](./advanced-usage-adapters-and-hooks.md)的内容。