# Session 对象

Session 对象允许你在多个请求之间持久化某些参数。它还会在从 Session 实例发出的所有请求中持久化 cookie，并使用 `urllib3` 的连接池。因此，如果你向同一个主机发出多个请求，底层的 TCP 连接将被重用，这可以显著提升性能。

Session 对象拥有主 Requests API 的所有方法。

让我们在多个请求之间持久化一些 cookie：

```python Session 用法 icon=logos:python
s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

Session 也可以用于为请求方法提供默认数据。这通过向 Session 对象的属性提供数据来完成：

```python Session 默认值 icon=logos:python
s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# 'x-test' 和 'x-test2' 都会被发送
s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
```

Session 也可以用作上下文管理器，这将确保即使发生未处理的异常，会话也会被关闭。

```python 上下文管理器 icon=logos:python
with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

你传递给请求方法的任何字典都将与设置的会话级别的值合并。方法级别的参数会覆盖会话参数。

## 类定义

`class requests.sessions.Session`

### 属性

<x-field-group>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="一个不区分大小写的字典，包含从此 Session 发送的每个请求中要发送的标头。"></x-field>
  <x-field data-name="auth" data-type="tuple | object" data-desc="附加到每个请求的默认身份验证元组或对象。"></x-field>
  <x-field data-name="proxies" data-type="dict" data-desc="将协议或协议和主机映射到代理 URL 的字典。"></x-field>
  <x-field data-name="hooks" data-type="dict" data-desc="用于挂钩到请求过程的事件处理钩子。"></x-field>
  <x-field data-name="params" data-type="dict" data-desc="附加到每个请求的查询字符串数据字典。"></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="False" data-desc="默认是否流式传输响应内容。当设置为 True 时，不会立即下载响应内容。"></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="True">
    <x-field-desc markdown>控制 SSL 证书验证。可以是一个布尔值或一个指向 CA 包的路径。设置为 `False` 会禁用验证，这是不安全的，不建议在生产环境中使用。</x-field-desc>
  </x-field>
  <x-field data-name="cert" data-type="string | tuple" data-desc="默认 SSL 客户端证书。如果是一个字符串，它是证书文件（.pem）的路径。如果是一个元组，它是一个 ('cert', 'key') 对。"></x-field>
  <x-field data-name="max_redirects" data-type="integer" data-default="30" data-desc="允许的最大重定向次数。超过此限制会引发 TooManyRedirects 异常。"></x-field>
  <x-field data-name="trust_env" data-type="boolean" data-default="True" data-desc="如果为 True，则信任用于代理配置、默认身份验证和类似配置的环境设置。"></x-field>
  <x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="一个包含此会话上设置的所有 cookie 的 CookieJar。"></x-field>
  <x-field data-name="adapters" data-type="OrderedDict" data-desc="一个已注册的连接适配器字典，将 URL 前缀映射到适配器实例。"></x-field>
</x-field-group>

### 方法

#### request()

构造一个 `Request`，准备它，然后发送它。返回一个 `Response` 对象。

**参数**
<x-field-group>
  <x-field data-name="method" data-type="string" data-required="true" data-desc="新 Request 对象的方法：GET、OPTIONS、HEAD、POST、PUT、PATCH、DELETE。"></x-field>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
  <x-field data-name="params" data-type="dict | bytes" data-required="false" data-desc="在 Request 的查询字符串中发送的字典或字节。"></x-field>
  <x-field data-name="data" data-type="dict | list[tuple] | bytes | file" data-required="false" data-desc="在 Request 的正文中发送的对象。"></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="一个可在 Request 正文中发送的 JSON 可序列化的 Python 对象。"></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="随 Request 一起发送的 HTTP 标头字典。"></x-field>
  <x-field data-name="cookies" data-type="dict | CookieJar" data-required="false" data-desc="随 Request 一起发送的字典或 CookieJar 对象。"></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="用于多部分编码上传的 '名称': 文件类对象 字典。"></x-field>
  <x-field data-name="auth" data-type="tuple | callable" data-required="false" data-desc="用于启用基本/摘要/自定义 HTTP 身份验证的身份验证元组或可调用对象。"></x-field>
  <x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="在放弃之前等待服务器发送数据的秒数。可以是一个浮点数或一个 (connect, read) 元组。"></x-field>
  <x-field data-name="allow_redirects" data-type="boolean" data-default="True" data-required="false" data-desc="默认为 True，可以设置为 False 来禁用重定向。"></x-field>
  <x-field data-name="proxies" data-type="dict" data-required="false" data-desc="将协议或协议和主机名映射到代理 URL 的字典。"></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-required="false" data-desc="控制是否验证服务器的 TLS 证书。默认为会话的 verify 值。"></x-field>
  <x-field data-name="stream" data-type="boolean" data-required="false" data-desc="是否立即下载响应内容。默认为会话的 stream 值。"></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-required="false" data-desc="SSL 客户端证书文件（.pem）的路径或 ('cert', 'key') 元组。默认为会话的 cert 值。"></x-field>
</x-field-group>

**返回**
<x-field data-name="response" data-type="requests.Response" data-desc="一个 Response 对象。"></x-field>

#### get()

发送一个 GET 请求。

**参数**
<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="request 接受的可选参数。"></x-field>
</x-field-group>

**返回**
<x-field data-name="response" data-type="requests.Response" data-desc="一个 Response 对象。"></x-field>

#### post()

发送一个 POST 请求。

**参数**
<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
  <x-field data-name="data" data-type="dict | list[tuple] | bytes | file" data-required="false" data-desc="在 Request 的正文中发送的对象。"></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="一个可在 Request 正文中发送的 JSON 可序列化的 Python 对象。"></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="request 接受的可选参数。"></x-field>
</x-field-group>

**返回**
<x-field data-name="response" data-type="requests.Response" data-desc="一个 Response 对象。"></x-field>

#### 其他 HTTP 方法

`Session` 对象还为所有其他 HTTP 动词提供了便捷方法：

*   `options(url, **kwargs)`
*   `head(url, **kwargs)`
*   `put(url, data=None, **kwargs)`
*   `patch(url, data=None, **kwargs)`
*   `delete(url, **kwargs)`

这些方法的功能与 `get()` 和 `post()` 类似，接受一个 URL 和可选的关键字参数，这些参数会传递给底层的 `request()` 方法。

#### prepare_request()

构造一个用于传输的 `PreparedRequest` 并返回它。`PreparedRequest` 合并了来自 `Request` 实例和 `Session` 的设置。

**参数**
<x-field data-name="request" data-type="requests.Request" data-required="true" data-desc="要使用此会话设置准备的 Request 实例。"></x-field>

**返回**
<x-field data-name="prepared_request" data-type="requests.PreparedRequest" data-desc="准备好的请求对象。"></x-field>

#### send()

发送一个给定的 `PreparedRequest`。

**参数**
<x-field-group>
  <x-field data-name="request" data-type="requests.PreparedRequest" data-required="true" data-desc="要发送的 PreparedRequest 对象。"></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="request 接受的可选参数（例如，stream、timeout、verify）。"></x-field>
</x-field-group>

**返回**
<x-field data-name="response" data-type="requests.Response" data-desc="一个 Response 对象。"></x-field>

#### mount()

将连接适配器注册到一个前缀。适配器按前缀长度降序排序。

**参数**
<x-field-group>
  <x-field data-name="prefix" data-type="string" data-required="true" data-desc="要挂载适配器的 URL 前缀（例如，'https://' 或 'http://my-api.com'）。"></x-field>
  <x-field data-name="adapter" data-type="requests.adapters.BaseAdapter" data-required="true" data-desc="连接适配器实例。"></x-field>
</x-field-group>

#### close()

关闭所有适配器，从而关闭会话。此操作会清理所有打开的连接。

### 已弃用的函数

#### session()

`requests.sessions.session()`

返回一个用于上下文管理的 `Session`。

> **自 1.0.0 版本起已弃用：** 此方法仅为向后兼容而保留。新代码应使用 `requests.Session()` 来创建会话。