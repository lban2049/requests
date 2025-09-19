# Session 对象

Session 对象允许你在多个请求之间持久化某些参数。它还会在从 Session 实例发出的所有请求中持久化 Cookie，并利用 `urllib3` 的连接池。如果你向同一个主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

Session 对象拥有主 Requests API 的所有方法。

让我们在多个请求之间持久化一些 Cookie：

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

## 持久化参数

Session 也可以用于为请求方法提供默认数据。这通过为 Session 对象的属性提供数据来完成。这些属性将应用于使用该 Session 发出的所有后续请求。

### Session 属性

可以在 `Session` 对象上设置以下属性来配置默认请求值：

<x-field-group>
  <x-field data-name="headers" data-type="dict" data-desc="一个不区分大小写的字典，包含在每个请求中发送的标头。"></x-field>
  <x-field data-name="auth" data-type="tuple | object" data-desc="附加到每个请求的默认身份验证元组或对象。"></x-field>
  <x-field data-name="params" data-type="dict" data-desc="附加到每个请求的查询字符串数据字典。"></x-field>
  <x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="一个包含此会话上设置的所有 Cookie 的 CookieJar。"></x-field>
  <x-field data-name="proxies" data-type="dict" data-desc="将协议或主机名映射到代理 URL 的字典。"></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="true" data-desc="默认 SSL 验证设置。可以是一个布尔值或一个 CA 证书包的路径。"></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-desc="默认 SSL 客户端证书。可以是一个 .pem 文件的路径或一个 ('cert', 'key') 元组。"></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="false" data-desc="是否立即下载响应内容的默认设置。"></x-field>
  <x-field data-name="max_redirects" data-type="number" data-default="30" data-desc="一个请求允许的最大重定向次数。"></x-field>
  <x-field data-name="hooks" data-type="dict" data-desc="用于响应的事件处理钩子。"></x-field>
</x-field-group>

### 示例：默认标头和身份验证

以下示例展示了如何在会话上设置默认的标头和身份验证，这些设置将存在于所有后续请求中。

```python Setting Default Parameters icon=logos:python
import requests

s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# 'x-test' 和 'x-test2' 都会在此请求中发送
response = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})

print(response.json())
# 预期输出可能如下所示：
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Basic dXNlcjpwYXNz", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.31.0", 
#     "X-Amzn-Trace-Id": "Root=...", 
#     "X-Test": "true", 
#     "X-Test2": "true"
#   }
# }
```

## 覆盖 Session 参数

虽然 Session 提供了合理的默认值，但你可以在单个请求的基础上覆盖这些设置。任何直接传递给请求方法（`get`、`post` 等）的参数都将与会话级别的参数合并。在发生冲突时，方法级别的参数将优先。

例如，如果你想为特定请求省略会话级别的标头，可以在方法调用中将该标头的值设置为 `None`。

```python Overriding Session Headers icon=logos:python
import requests

with requests.Session() as s:
    s.headers.update({'x-test': 'true', 'x-common': 'default-value'})

    # 此请求包含所有会话标头
    # 并覆盖 'x-test' 同时添加 'x-test2'
    response1 = s.get('https://httpbin.org/headers', headers={'x-test': 'false', 'x-test2': 'true'})
    print('Response 1 Headers:', response1.json()['headers']['X-Test'], response1.json()['headers']['X-Test2'])
    # 预期：Response 1 Headers: false true

    # 此请求仅在此次调用中移除 'x-test' 标头
    response2 = s.get('https://httpbin.org/headers', headers={'x-test': None})
    print('Does Response 2 have X-Test?:', 'X-Test' in response2.json()['headers'])
    # 预期：Does Response 2 have X-Test?: False
```


## 作为上下文管理器的 Session

所有 Session 都可以用作上下文管理器。这是推荐的方法，因为它能确保即使发生未处理的异常，Session 也能通过 `Session.close()` 正确关闭。关闭 Session 会清理连接池中的所有底层连接。

```python Session as Context Manager icon=logos:python
with requests.Session() as s:
    r = s.get('https://httpbin.org/get')
    print(f'Status Code: {r.status_code}')

# Session 会在此处自动关闭
```

通过使用 Session 对象，当处理对同一服务的多个 API 调用时，你可以显著提高代码的性能和清晰度。对于更复杂的场景，你可能需要研究 Session 如何与[身份验证](./advanced-usage-authentication.md)机制交互。