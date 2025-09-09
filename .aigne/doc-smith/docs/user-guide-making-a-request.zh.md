# 发送请求

使用 Requests 库发送 HTTP 请求既简单又直观。本指南将引导你了解最常见的 HTTP 方法，并展示如何通过参数、标头和不同类型的请求体来自定义请求。

## 发送 GET 请求

要发送 `GET` 请求，请使用 `requests.get()` 函数。这是从 URL 检索数据最常用的方法之一。

```python 发送一个简单的 GET 请求 icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
# 响应对象 `r` 现在包含了服务器的响应。
```

### 在 URL 中传递参数

通常，你需要在 URL 的查询字符串（例如 `?key=value`）中传递数据。你可以提供一个字典或元组列表作为 `params` 参数，而无需手动构建 URL。Requests 会为你正确地编码这些参数。

```python 传递 URL 参数 icon=logos:python
import requests

# 使用字典作为参数
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

# 你可以验证所构建的 URL
print(r.url)
# 输出: https://httpbin.org/get?key1=value1&key2=value2
```

如果你需要为同一个键传递多个值，可以使用一个元组列表：

```python icon=logos:python
# 使用元组列表传递多个值
payload_tuples = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload_tuples)
print(r.url)
# 输出: https://httpbin.org/get?key1=value1&key1=value2
```

## 其他 HTTP 方法

Requests 为所有其他标准 HTTP 方法提供了简单的函数：`POST`、`PUT`、`PATCH`、`DELETE`、`HEAD` 和 `OPTIONS`。它们都和 `GET` 一样简单明了。

```python 使用各种 HTTP 方法 icon=logos:python
r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

## 在请求体中传递数据

对于 `POST`、`PUT` 和 `PATCH` 等方法，你通常需要在请求体中发送数据。Requests 通过 `data` 和 `json` 参数简化了这一过程。

### 发送表单编码的数据

要发送 HTML 表单形式的数据，你可以将一个字典传递给 `data` 参数。你的数据字典在发送请求时将自动被表单编码。

```python POST 表单数据 icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post('https://httpbin.org/post', data=payload)

# 表单数据可在响应的 'form' 字段中找到
print(r.json()['form'])
# 输出: {'key1': 'value1', 'key2': 'value2'}
```

### 发送 JSON 数据

对于现代 API 而言，以 JSON 格式发送数据非常普遍。你可以使用 `json` 参数，而无需自己对数据进行编码。Requests 会自动将你的 Python 对象序列化为 JSON 字符串，并将 `Content-Type` 标头设置为 `application/json`。

```python POST JSON 数据 icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
payload = {'some': 'data'}

r = requests.post(url, json=payload)
# Content-Type 标头会自动设置为 'application/json'
```

### 上传文件（Multipart-Encoded）

Requests 也支持 multipart-encoded 文件上传。你可以将一个包含类文件对象的字典传递给 `files` 参数。

```python 上传文件 icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}

r = requests.post(url, files=files)
print(r.text)
```

你还可以通过向 `files` 字典值传递一个元组来显式设置文件名、内容类型和自定义标头。

```python 自定义文件上传 icon=logos:python
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}
r = requests.post(url, files=files)
```

## 自定义标头

如果你需要向请求添加自定义 HTTP 标头，可以将一个字典传递给 `headers` 参数。

```python 添加自定义标头 icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

---

现在你已经了解如何创建和自定义请求，下一步是理解从服务器返回的响应。为此，让我们继续下一节。

<x-card data-title="Handling Responses" data-icon="lucide:arrow-right-circle" data-href="/user-guide/handling-responses" data-cta="Next Step">
  学习如何访问响应内容、状态码和标头。
</x-card>