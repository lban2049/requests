# 身份验证

许多 Web 服务需要通过身份验证来授予资源访问权限。Requests 提供了多种内置的身份验证机制和一种创建自定义机制的简单方法，从而简化了这一过程。身份验证通过向请求方法传递一个 `auth` 对象来处理。

本指南将介绍最常见的身份验证方案。要更深入地了解 Requests 如何处理请求和响应周期，您可以查阅[发出请求](./user-guide-making-a-request.md)和[处理响应](./user-guide-handling-responses.md)。

```d2
direction: down

Request-Object: {
  label: "用户的请求对象"
  shape: rectangle
  "URL、方法等"
  "设置了 auth=(...)"
}

Auth-Handler: {
  label: "身份验证处理程序类\n（例如，HTTPBasicAuth）"
  shape: rectangle
}

Prepared-Request: {
  label: "PreparedRequest"
  shape: rectangle
  "标头已修改"
}

Server: {
  shape: cylinder
}

Request-Object -> Auth-Handler: "1. 调用 Auth 对象"
Auth-Handler -> Prepared-Request: "2. 添加 'Authorization' 标头"
Prepared-Request -> Server: "3. 发送到服务器"
```

## 基本身份验证

基本身份验证是一种广泛使用的简单方法，它会随请求一同发送用户名和密码。Requests 使用一个包含两个元素的元组为此提供了便捷的简写方式。

要使用基本身份验证，请将一个 `(username, password)` 元组传递给 `auth` 参数：

```python Basic Auth with a Tuple icon=logos:python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# 状态码：200
```

该元组是 `requests.auth` 模块中 `HTTPBasicAuth` 类的简写。以上代码等同于以下代码：

```python Basic Auth with HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'Status Code: {response.status_code}')
# 状态码：200
```

在这两种情况下，Requests 都会处理用户名和密码的 Base64 编码，并为您的请求添加相应的 `Authorization` 标头。

## 摘要式身份验证

摘要式身份验证提供了一种比基本身份验证更安全的替代方案。它采用质询-响应机制，避免了以明文形式发送密码。

要使用摘要式身份验证，您可以使用 `HTTPDigestAuth` 类：

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'Status Code: {response.status_code}')
# 状态码：200
```

Requests 会为您管理整个握手过程。它会自动处理来自服务器的初始 `401 Unauthorized` 响应，从 `WWW-Authenticate` 标头中提取 `nonce` 和其他详细信息，构建正确的 `Authorization` 标头，并重新发送请求。

## 代理身份验证

如果您通过需要身份验证的代理来路由请求，可以使用 `HTTPProxyAuth` 类。其工作方式与 `HTTPBasicAuth` 类似，但设置的是 `Proxy-Authorization` 标头，而非 `Authorization` 标头。

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# 这是一个虚构的代理 URL。请替换为您的实际代理。
proxies = {
   'http': 'http://10.10.1.10:3128',
   'https': 'http://10.10.1.10:1080',
}

# 创建一个代理身份验证对象
proxy_auth = HTTPProxyAuth('proxy_user', 'proxy_password')

# 请求将通过带有指定身份验证的代理发送
response = requests.get(
    'https://httpbin.org/get',
    proxies=proxies,
    auth=proxy_auth
)

print(response.status_code)
```

## 自定义身份验证

如果您需要一个未内置的身份验证方案（例如自定义的基于令牌的系统），您可以创建自己的方案。任何能够修改 `PreparedRequest` 的可调用对象都可以作为身份验证处理程序。Requests 提供了 `requests.auth.AuthBase` 类，使这一过程更加简洁。

要创建自定义身份验证机制，请继承 `AuthBase` 并实现 `__call__` 方法。该方法应接受请求对象作为参数，根据需要对其进行修改（通常是添加标头），然后返回该对象。

以下是一个自定义身份验证类的示例，该类将一个令牌添加到自定义的 `X-API-Token` 标头中：

```python Custom Token Authentication icon=logos:python
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
# 200
# my-secret-api-token
```

这展示了身份验证系统的灵活性，让您可以与 API 所需的几乎任何身份验证方案进行集成。

既然您已经了解了如何保护请求，下一步就是学习如何处理计划外的情况。为此，请参阅[错误处理](./user-guide-error-handling.md)指南。