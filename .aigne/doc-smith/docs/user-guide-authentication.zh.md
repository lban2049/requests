# 身份验证

许多 Web 服务需要身份验证才能授予对其资源的访问权限。Requests 提供了一种使用 `auth` 参数处理此问题的直接方法，它原生支持几种常见的身份验证方案，并允许自定义实现。

## 基本身份验证

基本身份验证是一种广泛使用的简单身份验证方法。它会在你的请求中发送用户名和密码。Requests 提供了一种使用 `(username, password)` 元组的便捷简写方式。

```python Basic Auth with a Tuple icon=logos:python
import requests

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=('user', 'pass')
)

print(f'状态码: {response.status_code}')
print(f'响应文本: {response.text}')
```

这个元组是 `HTTPBasicAuth` 类的一个快捷方式。你也可以直接使用这个类来编写更明确的代码。

```python Basic Auth with HTTPBasicAuth Class icon=logos:python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass',
    auth=HTTPBasicAuth('user', 'pass')
)

print(f'状态码: {response.status_code}')
```

## 摘要式身份验证

摘要式身份验证是一种比基本身份验证更安全的方法，因为它不会以明文形式通过网络发送密码。Requests 无缝地处理了这种方案的复杂性。要使用它，你可以将 `HTTPDigestAuth` 类的一个实例传递给 `auth` 参数。

```python Digest Authentication icon=logos:python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/auth/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f'状态码: {response.status_code}')
print(f'响应文本: {response.text}')
```

Requests 会自动处理摘要式身份验证所需的挑战-响应握手。

## 代理身份验证

如果你的请求需要通过需要身份验证的代理路由，你可以使用 `HTTPProxyAuth` 类。它的工作方式与 `HTTPBasicAuth` 类似，但设置的是 `Proxy-Authorization` 标头。

```python Proxy Authentication icon=logos:python
import requests
from requests.auth import HTTPProxyAuth

# 注意：这是一个代理的占位符 URL。
# 请替换为你的实际代理地址。
proxies = {
   'http': 'http://10.10.1.10:3128',
}

auth = HTTPProxyAuth('user', 'pass')

# 此请求将通过带身份验证的代理发送。
response = requests.get('https://httpbin.org/get', proxies=proxies, auth=auth)

print(f'状态码: {response.status_code}')
```

有关配置代理的更多详细信息，请参阅 [超时、重试和代理](./advanced-usage-timeouts-retries-proxies.md) 部分。

## 自定义身份验证

如果你的身份验证方案未被内置方法涵盖，Requests 允许你创建自己的方案。只需创建一个继承自 `requests.auth.AuthBase` 的类并实现 `__call__` 方法。该方法应接受一个请求对象并返回修改后的请求对象。

以下是一个添加自定义标头的自定义身份验证类的示例：

```python Custom Authentication Class icon=logos:python
import requests

class TokenAuth(requests.auth.AuthBase):
    """将自定义令牌附加到给定的 Request 对象。"""
    def __init__(self, token):
        # 在此处设置任何与身份验证相关的数据
        self.token = token

    def __call__(self, r):
        # 修改并返回请求
        r.headers['X-TokenAuth'] = f'{self.token}'
        return r

# 用法
response = requests.get('https://httpbin.org/get', auth=TokenAuth('my-secret-token'))

print(response.json()['headers']['X-Tokenauth'])
```

这一强大功能允许你集成任何身份验证机制，包括像 OAuth 这样的流行方案，这些方案通常有专门的库提供与 Requests 兼容的 `AuthBase` 实现。

---

现在你已经了解了如何保护你的请求，下一步是学习如何处理出现问题时的情况。请继续阅读 [错误处理](./user-guide-error-handling.md) 部分，了解如何管理异常和错误响应。