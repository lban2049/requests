# Session 对象

Session 对象是 Requests 最强大的功能之一。它允许你在多个请求之间保持某些参数。它还会在通过 Session 实例发出的所有请求中保持 cookie，并会使用 `urllib3` 的连接池。这意味着，如果你向同一主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

Session 对象拥有主 Requests API 的所有方法。

让我们在多个请求之间保持一些 cookie：

```python Session Cookie 持久化 icon=logos:python
import requests

s = requests.Session()

# 第一个设置 cookie 的请求
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

# 对同一域名的第二个请求将自动包含该 cookie
r = s.get('https://httpbin.org/cookies')

print(r.text)
# {
#   "cookies": { 
#     "sessioncookie": "123456789"
#   }
# }
```

## 持久化参数

Session 也可以用来为请求方法提供默认数据。这可以通过向 Session 对象的属性提供数据来实现：

```python 持久化会话级请求头 icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# 'x-test' 和 'x-test2' 都会被发送
r_with_both = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
print(r_with_both.json()['headers'])

# 在后续请求中，会话级的请求头依然存在
r_with_session_header = s.get('https://httpbin.org/headers')
print(r_with_session_header.json()['headers'])
```

你传递给请求方法的任何字典都将与设置的会话级别的值合并。方法级别的参数会覆盖会话参数。

让我们看看传递 `None` 值会发生什么。这对于在特定请求中从会话中移除某个请求头很有用：

```python 覆盖会话参数 icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# 此请求将不包含 'x-test' 请求头
r = s.get('https://httpbin.org/headers', headers={'x-test': None})

print(r.json()['headers'])
# {
#   "Accept": "*/*", 
#   "Accept-Encoding": "gzip, deflate", 
#   "Host": "httpbin.org", 
#   "User-Agent": "python-requests/2.28.1", 
#   "X-Amzn-Trace-Id": "..."
# }
```

## 使用 Session 作为上下文管理器

所有会话也都可以用作上下文管理器。这将确保即使引发异常，会话也会被自动关闭。这是使用 Session 的推荐方式。

```python Session 作为上下文管理器 icon=logos:python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get('https://httpbin.org/cookies')
    print(r.json())
```

对于发出高效且有状态的 HTTP 请求而言，使用会话至关重要。既然你已经了解了如何在多个请求之间保持数据，就可以探索如何处理不同类型的身份验证。

接下来，让我们深入了解[身份验证](./user-guide-authentication.md)。