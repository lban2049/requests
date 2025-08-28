# 身份验证

许多 Web 服务需要身份验证才能授予对资源的访问权限。Requests 通过 `auth` 参数直接支持各种身份验证方案，从而简化了这一过程。

## 基本身份验证

HTTP 基本身份验证是一种广泛使用、简单直接的身份验证方法。要在 Requests 中使用它，你可以向 `auth` 参数提供一个 `(username, password)` 元组。

```python
import requests

# 使用元组简写形式进行基本身份验证
response = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

print(f"Status Code: {response.status_code}")
# 状态码：200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

这个元组是一种方便的简写形式。在内部，Requests 会将其转换为 `requests.auth` 模块中的 `HTTPBasicAuth` 对象。你也可以直接创建并使用此对象，使代码更加明确。

```python
import requests
from requests.auth import HTTPBasicAuth

response = requests.get(
    'https://httpbin.org/basic-auth/user/pass', 
    auth=HTTPBasicAuth('user', 'pass')
)

print(f"Status Code: {response.status_code}")
# 状态码：200
```
当你使用基本身份验证时，Requests 会自动构建并添加 `Authorization` 标头到你的请求中，并附上正确编码的凭证。

## 摘要式身份验证

摘要式身份验证通过使用质询-响应机制，避免了以明文形式发送密码，为基本身份验证提供了一种更安全的替代方案。它的使用方法与基本身份验证一样简单。

首先，导入 `HTTPDigestAuth`，然后将其一个实例传递给 `auth` 参数。

```python
import requests
from requests.auth import HTTPDigestAuth

url = 'https://httpbin.org/digest-auth/qop/user/pass'

response = requests.get(url, auth=HTTPDigestAuth('user', 'pass'))

print(f"Status Code: {response.status_code}")
# 状态码：200
print(response.json())
# {'authenticated': True, 'user': 'user'}
```

Requests 会为你处理整个质询-响应流程，包括初始的未授权请求和随后的已授权请求。

## 自定义身份验证

Requests 具有可插拔的身份验证系统，允许你实现非内置的身份验证方案。你可以通过创建一个可调用类来创建自定义身份验证处理程序，该类在发送 `Request` 对象之前对其进行修改。

最简单的方法是继承 `requests.auth.AuthBase` 并实现 `__call__` 方法。该方法接收 `PreparedRequest` 对象，应根据需要对其进行修改（例如，通过添加自定义标头），并且必须返回修改后的对象。

以下是基于令牌的身份验证方案的自定义处理程序示例：

```python
import requests
from requests.auth import AuthBase

class TokenAuth(AuthBase):
    """将自定义令牌附加到 Authorization 标头。"""
    def __init__(self, token):
        self.token = token

    def __call__(self, r):
        # 通过添加 Authorization 标头来修改请求 `r`
        r.headers['Authorization'] = f'Token {self.token}'
        return r

# 使用自定义身份验证处理程序
response = requests.get('https://httpbin.org/headers', auth=TokenAuth('12345abcde'))

print(response.json())

# 预期响应显示自定义 Authorization 标头：
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Token 12345abcde", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/x.x.x", 
#     "X-Amzn-Trace-Id": "..."
#   }
# }
```

这种模块化的方法提供了与任何自定义或复杂身份验证协议集成的灵活性。

---

既然你已经可以对请求进行身份验证，下一步就是学习如何管理计划之外的情况。请继续阅读 [错误处理](./user-guide-error-handling.md) 指南以了解更多详情。