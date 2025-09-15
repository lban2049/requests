# Session 对象

Session 对象允许你在多个请求之间保持某些参数。它还会在通过 Session 实例发出的所有请求中持久化 cookie，并使用 `urllib3` 的连接池。因此，如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

Session 对象拥有主 Requests API 的所有方法。

让我们在多个请求之间持久化一些 cookie：

```python Session Cookie 持久化 icon=logos:python
import requests

s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# 预期输出：
# {
#   "cookies": {
#     "sessioncookie": "123456789"
#   }
# }
```

## 持久化参数

Session 也可以用来为请求方法提供默认数据。这可以通过为 Session 对象的属性提供数据来实现。你传递给请求方法的任何字典都将与已设置的会话级别的值合并。

例如，在会话级别设置的请求头将与你传递给特定请求的任何请求头合并。但是，方法调用中的请求头将优先。

```python 合并 Session 和请求的请求头 icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'session-value'})

# 会话请求头与特定于请求的请求头一起发送
r = s.get('https://httpbin.org/headers', headers={'x-test-header-2': 'request-value'})
print(r.json()["headers"])

# 在请求方法调用中设置的请求头会覆盖会话级别的请求头
r_override = s.get('https://httpbin.org/headers', headers={'x-test-header': 'request-override-value'})
print(r_override.json()["headers"])
```

任何作为参数传递给请求方法的对象（例如 `auth`、`cert`）也可以在会话级别进行设置。

```python 会话级别的身份验证 icon=logos:python
import requests

s = requests.Session()
s.auth = ('user', 'pass')

# 身份验证信息将自动用于此请求
r = s.get('https://httpbin.org/basic-auth/user/pass')

print(f"Status Code: {r.status_code}")
print(r.json())
# 预期输出：
# Status Code: 200
# {'authenticated': True, 'user': 'user'}
```

请注意，方法级别的参数不会在多个请求之间持久化。如果你想为所有未来的请求设置一个参数，必须在 session 对象上进行设置。要移除一个持久化参数，可以在 session 上将其设置为 `None`。

## Session 作为上下文管理器

所有 Session 都可以用作上下文管理器。这能确保在退出 `with` 块时，即使引发了异常，会话也会自动关闭。这对于清理连接池中的连接很有用。

```python 使用上下文管理器的 Session icon=logos:python
with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Request successful with status code: {response.status_code}")

# 现在会话已关闭，连接也已清理。
```

使用 Session 对象是管理状态、处理身份验证以及在与 Web 服务交互时提升应用程序性能的有效方式。为了确保请求的安全性，下一步是深入了解不同的身份验证方法。

现在你已经了解了如何在多个请求之间管理状态，让我们来探索如何处理[身份验证](./user-guide-authentication.md)。