# SSL 证书验证

默认情况下，Requests 会像网页浏览器一样为 HTTPS 请求验证 SSL 证书。这是一项关键的安全功能，可确保您连接到正确的服务器，并确保您的数据在传输过程中是加密的。默认情况下，Requests 使用 `certifi` 包提供的 CA 证书包。

本指南介绍了如何针对不同场景管理 SSL/TLS 验证，从使用自定义 CA 到提供客户端证书。

## 默认验证

默认情况下，Requests 会对所有 HTTPS 请求执行 SSL 验证。如果服务器的证书无法验证，将引发 `requests.exceptions.SSLError`。

```python 使用默认验证的请求
import requests

try:
    response = requests.get('https://httpbin.org/get')
    print('已通过 SSL 验证成功连接。')
except requests.exceptions.SSLError as e:
    print(f'SSL 错误：{e}')
```

在上面的代码中，`verify` 参数隐式地为 `True`。

## 自定义 CA 证书包

在企业环境中或与使用私有证书颁发机构 (CA) 的服务交互时，您可能需要使用自定义 CA 证书包。您可以为 `verify` 参数指定 CA 证书包文件（`.pem`）的路径。

```python 使用自定义 CA 证书包
import requests

ca_bundle_path = '/path/to/your/ca.pem'

try:
    response = requests.get('https://your-internal-service.com', verify=ca_bundle_path)
    print('已使用自定义 CA 证书包成功连接。')
except requests.exceptions.RequestException as e:
    print(f'发生错误：{e}')
```

此外，也可以通过设置环境变量 `REQUESTS_CA_BUNDLE` 或 `CURL_CA_BUNDLE` 为证书文件的路径，来配置 Requests 使用自定义 CA 证书包。

## 客户端证书

一些服务器要求客户端提供自己的证书进行身份验证，这个过程称为双向 TLS (mTLS)。您可以使用 `cert` 参数提供客户端证书。

如果您的证书和私钥在同一个文件中，您可以将文件路径作为字符串传递：

```python 单文件中的客户端证书
import requests

cert_file_path = '/path/to/your/client.pem'

response = requests.get(
    'https://api.secure-service.com/data',
    cert=cert_file_path
)

print(response.status_code)
```

如果您的证书和私钥在不同的文件中，请将它们作为元组传递：

```python 作为元组的客户端证书和密钥
import requests

cert_and_key = ('/path/to/your/client.crt', '/path/to/your/client.key')

response = requests.get(
    'https://api.secure-service.com/data',
    cert=cert_and_key
)

print(response.status_code)
```

## 禁用 SSL 验证

尽管在生产环境中非常不推荐，但在本地开发或针对使用自签名证书的服务器进行测试时，您可能需要禁用 SSL 验证。要禁用 SSL 验证，请将 `verify` 参数设置为 `False`。

**警告：** 禁用 SSL 验证会使您的应用程序面临中间人 (MitM) 攻击的风险。请仅在受控、可信的环境中使用此选项。

```python 禁用 SSL 验证（不安全）
import requests

# 注意：这可能会产生一个 InsecureRequestWarning
response = requests.get('https://self-signed.badssl.com/', verify=False)

print(f'已连接，状态码：{response.status_code}')
```

## 使用会话持久化验证设置

对于向同一主机发出多个请求的应用程序，使用 `Session` 对象会更高效。您可以在会话上配置 SSL 验证设置，这些设置将应用于该会话发出的所有后续请求。

```python 在会话对象上配置 SSL
import requests

s = requests.Session()

# 为此会话中的所有请求设置 CA 证书包
s.verify = '/path/to/your/ca.pem'

# 为此会话中的所有请求设置客户端证书
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# 此请求将使用已配置的 SSL 设置
response = s.get('https://api.your-internal-service.com/status')

print(response.json())
```

这种方法避免了为每个请求进行重复设置，并利用连接池来提高性能。

---

现在您已经了解了如何管理 SSL 证书验证，接下来可以探索如何通过创建[自定义适配器和钩子](./advanced-usage-adapters-and-hooks.md)来进一步扩展 Requests 的功能。