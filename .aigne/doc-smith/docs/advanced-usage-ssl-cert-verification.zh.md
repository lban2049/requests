# SSL 证书验证

默认情况下，Requests 会为 HTTPS 请求验证 SSL 证书，这是一项关键的安全功能，可确保您连接到预期的服务器。此验证依赖一组受信任的证书颁发机构 (CA) 来验证服务器的证书。

本节将说明此验证的工作原理，以及如何针对特定场景进行管理，例如使用自定义 CA、提供用于身份验证的客户端证书，或为受信任的环境禁用验证。

## 默认验证

默认情况下，Requests 使用由 `certifi` 包提供的 CA 包。当您发出 HTTPS 请求时，此行为会自动启用。

```python
import requests

# 此请求将根据 certifi 的 CA 包验证服务器的 SSL 证书。
response = requests.get('https://httpbin.org/get')
```

如果验证失败，Requests 将引发 `SSLError`。

## 自定义 CA 包

您可以通过向 `verify` 参数传递 CA 包文件或 CA 证书目录的路径来指定自己的 CA 包，而不是使用默认的 CA 包。

```python
import requests

# 使用自定义 CA 包文件
ca_bundle_path = '/path/to/your/ca.pem'
response = requests.get('https://httpbin.org/get', verify=ca_bundle_path)

# 使用 CA 证书目录
ca_cert_dir_path = '/path/to/your/certs/'
response = requests.get('https://httpbin.org/get', verify=ca_cert_dir_path)
```

### 使用环境变量

Requests 也会遵循 `REQUESTS_CA_BUNDLE` 和 `CURL_CA_BUNDLE` 环境变量。如果将其中任何一个设置为有效路径，Requests 将其用作所有请求的默认 CA 包，从而覆盖 `certifi` 包。

```bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## 禁用 SSL 验证

在某些情况下，例如在本地开发或针对具有自签名证书的服务器进行测试时，您可能需要禁用 SSL 验证。您可以通过设置 `verify=False` 来实现。

> **警告：** 禁用 SSL 证书验证会使您的应用程序容易受到中间人 (MitM) 攻击。如果没有验证，则无法保证您正在与预期的服务器通信。这只应在受控的非生产环境中进行。

```python
import requests

# 这将禁用 SSL 证书验证并抑制任何警告。
response = requests.get('https://localhost:5000/get', verify=False)
```

## 客户端证书

对于双向 TLS (mTLS) 身份验证，您可能需要提供客户端证书。您可以使用 `cert` 参数来实现。该值可以是一个包含证书和私钥的单个文件的路径，也可以是一个包含证书文件和密钥文件路径的元组。

**单个文件（证书和密钥）**

```python
import requests

cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.example.com', cert=cert_file_path)
```

**单独的文件（证书和密钥）**

```python
import requests

cert_file_path = '/path/to/client.cert'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.example.com', cert=(cert_file_path, key_file_path))
```

## 使用 Session 持久化验证设置

如果您需要使用相同的验证设置向同一主机发出多个请求，使用 `Session` 对象会更高效。您可以在 Session 上配置 `verify` 和 `cert` 属性，这些设置将应用于使用该 Session 发出的所有后续请求。

```python
import requests

s = requests.Session()

# 为 Session 设置自定义 CA 包
s.verify = '/path/to/ca.pem'

# 为 Session 设置客户端证书
s.cert = ('/path/to/client.cert', '/path/to/client.key')

# 这两个设置都将用于此请求
response = s.get('https://api.example.com/data')
```

通过有效管理 SSL 设置，您可以确保应用程序安全通信，同时适应各种网络环境和身份验证要求。

---

要进行更高级的网络控制，例如定义自定义连接逻辑或处理特定协议，请继续阅读下一节关于[自定义适配器和钩子](./advanced-usage-adapters-and-hooks.md)的内容。