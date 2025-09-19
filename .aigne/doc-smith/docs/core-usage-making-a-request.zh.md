# 发出请求

使用 Requests 库发出 HTTP 请求简单而直观。对于每个主要的 HTTP 动词，都有一个相应的函数来处理请求-响应周期。本节将介绍发出请求和检查收到的响应的基本模式。

## 发出 GET 请求

`GET` 方法用于从指定资源请求数据。这是最常见的请求类型。要发送 `GET` 请求，只需使用 `requests.get()` 函数。

```python 一个简单的 GET 请求 icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

发出请求后，Requests 会返回一个 `Response` 对象。该对象包含服务器返回的所有信息，包括状态码、标头和响应正文。成功的 `GET` 请求通常会返回 `200` 状态码。

```python 检查响应状态 icon=logos:python
>>> r.status_code
200
```

## 其他 HTTP 方法

Requests 为每个标准 HTTP 方法都提供了一个简单的函数，使你的代码清晰易读。

<x-cards data-columns="2">
  <x-card data-title="POST" data-icon="lucide:send-to-back">
    用于向指定资源提交实体，通常会导致服务器上的状态变更或副作用。
  </x-card>
  <x-card data-title="PUT" data-icon="lucide:arrow-up-square">
    用请求的有效载荷替换目标资源的所有当前表示。
  </x-card>
  <x-card data-title="DELETE" data-icon="lucide:trash-2">
    删除指定的资源。
  </x-card>
  <x-card data-title="PATCH" data-icon="lucide:edit">
    用于对资源进行部分修改。
  </x-card>
  <x-card data-title="HEAD" data-icon="lucide:heading-1">
    请求与 GET 请求相同的响应，但不包含响应正文。在完整下载前，可用于检查标头等元数据。
  </x-card>
  <x-card data-title="OPTIONS" data-icon="lucide:sliders-horizontal">
    用于描述目标资源的通信选项。
  </x-card>
</x-cards>

以下是每种方法的快速示例：

```python HTTP 方法调用示例 icon=logos:python
# POST 请求
r = requests.post('https://httpbin.org/post', data={'key': 'value'})

# PUT 请求
r = requests.put('https://httpbin.org/put', data={'key': 'value'})

# DELETE 请求
r = requests.delete('https://httpbin.org/delete')

# HEAD 请求
r = requests.head('https://httpbin.org/get')

# OPTIONS 请求
r = requests.options('https://httpbin.org/get')

# PATCH 请求
r = requests.patch('https://httpbin.org/patch', data={'key':'value'})
```

有关在请求正文中发送数据的更多详细信息，请参阅 [提交数据](./core-usage-posting-data.md) 部分。

## 通用 `request()` 函数

在底层，所有特定的 HTTP 方法函数（`get`、`post` 等）都是对核心 `requests.request()` 函数的便捷封装。如果你更喜欢直接使用，或者需要动态指定方法，可以直接调用它。

例如，`requests.get(url)` 等同于 `requests.request('get', url)`。

```python 使用通用 request() 函数 icon=logos:python
>>> import requests
>>> req = requests.request('GET', 'https://httpbin.org/get')
>>> req
<Response [200]>
```

`request` 函数接受以下参数：

<x-field-group>
  <x-field data-name="method" data-type="string" data-required="true" data-desc="请求的 HTTP 方法：GET、OPTIONS、HEAD、POST、PUT、PATCH 或 DELETE。"></x-field>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="新 Request 对象的 URL。"></x-field>
  <x-field data-name="params" data-type="dict | list | bytes" data-required="false" data-desc="在 Request 的查询字符串中发送的数据。"></x-field>
  <x-field data-name="data" data-type="dict | list | bytes | file" data-required="false" data-desc="在 Request 的正文中发送的数据。"></x-field>
  <x-field data-name="json" data-type="object" data-required="false" data-desc="在 Request 正文中发送的可序列化为 JSON 的 Python 对象。"></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="随 Request 一同发送的 HTTP 标头字典。"></x-field>
  <x-field data-name="cookies" data-type="dict | CookieJar" data-required="false" data-desc="随 Request 一同发送的字典或 CookieJar 对象。"></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="用于多部分编码上传的字典。"></x-field>
  <x-field data-name="auth" data-type="tuple" data-required="false" data-desc="用于启用基本/摘要/自定义 HTTP 认证的认证元组。"></x-field>
  <x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="在放弃前等待服务器发送数据的秒数。"></x-field>
  <x-field data-name="allow_redirects" data-type="boolean" data-default="true" data-required="false" data-desc="启用或禁用重定向。默认为 True。"></x-field>
  <x-field data-name="proxies" data-type="dict" data-required="false" data-desc="协议到代理 URL 的映射字典。"></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="true" data-required="false" data-desc="控制 TLS 证书验证。可以是一个布尔值或一个指向 CA 证书包的路径。"></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="false" data-required="false" data-desc="如果为 False，响应内容将立即被下载。"></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-required="false" data-desc="指向 SSL 客户端证书文件（.pem）的路径或一个 ('cert', 'key') 元组。"></x-field>
</x-field-group>

## 检查响应

获取 `Response` 对象后，你就可以访问所需的所有信息。如前所示，你可以检查状态码，也可以查看标头和响应正文。

```python 检查 Response 对象 icon=logos:python
>>> r = requests.get('https://httpbin.org/json')

# 检查状态码是否成功
>>> r.status_code
200

# 查看响应标头，返回的是一个 Python 字典
>>> r.headers['content-type']
'application/json'

# 获取服务器的编码
>>> r.encoding
'utf-8'

# 以纯文本形式访问响应正文
>>> r.text
'{\n  "slideshow": {\n    "author": "Yours Truly", \n    "date": "date of publication", ...'

# 或者，对于 JSON 响应，让 Requests 处理解码
>>> r.json()
{'slideshow': {'author': 'Yours Truly', 'date': 'date of publication', ...}}
```

## 后续步骤

现在你已经了解了如何发出基本请求，接下来可以探索如何进一步自定义它们：

<x-cards data-columns="3">
  <x-card data-title="传递 URL 参数" data-icon="lucide:at-sign" data-href="/core-usage/passing-url-parameters">
    了解如何向请求 URL 添加查询字符串。
  </x-card>
  <x-card data-title="处理响应内容" data-icon="lucide:file-text" data-href="/core-usage/handling-response-content">
    深入了解如何以各种格式访问响应正文。
  </x-card>
  <x-card data-title="提交数据" data-icon="lucide:file-up" data-href="/core-usage/posting-data">
    探索在请求正文中发送数据的不同方法。
  </x-card>
</x-cards>