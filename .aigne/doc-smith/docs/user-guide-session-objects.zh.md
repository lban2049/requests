# 会话对象

Session 对象允许你在多个请求之间保持某些参数。它还会在所有从 Session 实例发出的请求中保持 cookie，并利用 `urllib3` 的连接池。因此，如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

A Session 对象拥有 Requests 主 API 的所有方法。

让我们在多个请求之间保持某些 cookie：

```python Session Cookie Persistence icon=logos:python
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

Session 也可以用来为请求方法提供默认数据。这可以通过为 Session 对象的属性提供数据来实现：

```python Session with Default Headers icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'true'})

# 'x-test-header' 在两个请求中都会被发送
r_one = s.get('https://httpbin.org/headers')
print(r_one.json())

r_two = s.get('https://httpbin.org/headers', headers={'x-another-header': 'true'})
print(r_two.json())
```

### 参数优先级

任何传递给请求方法的字典都将与会话级别的值合并。但是，对于该单个请求，方法级别的参数将覆盖会话参数中的任何重复键。

例如：

```python Overriding Session Headers icon=logos:python
import requests

s = requests.Session()

# 为会话设置一个默认的头信息
s.headers.update({'Accept': 'application/json'})

# 此请求将使用会话的 'Accept' 头信息
res_json = s.get('https://httpbin.org/headers')
print(f"Request 1 Accept header: {res_json.json()['headers']['Accept']}")

# 此请求将仅在本次调用中覆盖会话的 'Accept' 头信息
res_html = s.get('https://httpbin.org/headers', headers={'Accept': 'text/html'})
print(f"Request 2 Accept header: {res_html.json()['headers']['Accept']}")

# 第三个请求将恢复使用会话的默认头信息
res_json_again = s.get('https://httpbin.org/headers')
print(f"Request 3 Accept header: {res_json_again.json()['headers']['Accept']}")
```

这也适用于其他会话级别的设置，例如 auth、params、proxies、verify 和 cert。

### 作为上下文管理器的 Session

所有会话都可以用作上下文管理器。这能确保即使在引发异常的情况下，会话也会被自动关闭。这对于清理连接非常有用。

```python Session as Context Manager icon=logos:python
with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Status Code: {response.status_code}")
# 会话在此处自动关闭
```

使用 Session 对象是提高请求效率和代码整洁度的绝佳方式，尤其是在与同一 API 端点进行多次交互时。

要了解如何在多个请求中管理凭据，请继续阅读 [身份验证](./user-guide-authentication.md) 指南。