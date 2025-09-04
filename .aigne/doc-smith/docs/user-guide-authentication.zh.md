# 身份验证

许多 Web 服务需要身份验证，Requests 为此提供了多种即用型方法。身份验证允许你在发出请求时提供凭据以验证你的身份。

在 Requests 中，身份验证通过向请求方法传递一个 `auth` 对象来处理。该库包含了针对常见身份验证方案（如 HTTP 基本身份验证和摘要身份验证）的内置类，并且也允许使用自定义身份验证机制。

## 基本身份验证

基本身份验证是一种广泛使用的、直接了当的方法。它随请求一起发送用户名和密码。Requests 使用一个包含两个元素的元组为此提供了便捷的简写方式。

要使用基本身份验证，请将一个 `(username, password)` 元组传递给 `auth` 参数：

```python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# 状态码：200
```

这个简单的元组是 `HTTPBasicAuth` 类的一种简写。上面的代码等同于以下代码：

```python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# 状态码：200
```

Requests 会自动处理用户名和密码所需的 Base64 编码，并将 `Authorization` 标头添加到你的请求中。

## 摘要身份验证

摘要身份验证通过使用质询-响应机制，避免了以明文形式发送密码，从而提供了比基本身份验证更安全的替代方案。

要使用摘要身份验证，你可以利用 `HTTPDigestAuth` 类：

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
# 状态码：200
```

Requests 会为你管理整个握手过程，包括处理服务器的 nonce 并为后续向同一域的请求构建正确的 `Authorization` 标头。

## 代理身份验证

如果你通过需要身份验证的代理路由请求，可以使用 `HTTPProxyAuth` 类。它的工作方式与 `HTTPBasicAuth` 类似，但设置的是 `Proxy-Authorization` 标头。

```python
import requests
from requests.auth import HTTPProxyAuth

# 这是一个虚构的代理 URL。请替换为你的实际代理。
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

# 创建一个代理身份验证对象
proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# 请求将通过具有指定身份验证的代理发送
response = requests.get(
    'https://httpbin.org/get',
    proxies=proxies,
    auth=proxy_auth
)

print(response.status_code)
```

## 自定义身份验证

如果你需要一个非内置的身份验证方案，可以创建自己的方案。任何能够修改 `PreparedRequest` 对象的可调用对象都可以作为身份验证处理程序。Requests 提供了 `requests.auth.AuthBase` 类来简化这一过程。

要创建自定义身份验证机制，请继承 `AuthBase` 并实现 `__call__` 方法。该方法应修改请求对象（通常是通过添加标头）并将其返回。

以下是一个自定义身份验证类的示例，它将一个令牌添加到一个自定义标头中：

```python
import requests

class TokenAuth(requests.auth.AuthBase):
    """将自定义令牌附加到 X-API-Token 标头。"""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        r.headers['X-API-Token'] = f'{self.token}'
        return r

response = requests.get(
    'https://httpbin.org/get',
    auth=TokenAuth('my-secret-api-token')
)

print(response.status_code)
print(response.json()['headers']['X-Api-Token'])
# my-secret-api-token
```

这展示了身份验证系统的灵活性，允许你与几乎任何身份验证方案集成。

现在你已经了解了如何保护你的请求，下一步是学习如何处理计划外的情况。为此，请参阅[错误处理](./user-guide-error-handling.md)指南。