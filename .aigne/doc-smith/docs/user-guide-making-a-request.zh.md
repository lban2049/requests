# 发起请求

发起 HTTP 请求是大多数现代应用程序的基础部分，而 Requests 库使这一过程变得非常简单。本指南将介绍发送数据和与 Web 服务交互的常用方法。

我们先从一个简单的 GET 请求开始，获取一些数据。

```python 发起一个简单的 GET 请求 icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
print(r.status_code)
# 200
```

就是这么简单！现在，我们来探索如何根据你的需求自定义这些请求。

## 传递 URL 参数

通常，你需要在 URL 的查询字符串中发送数据。你可以向 `params` 参数提供一个键值对字典，而不用手动构建 URL。Requests 会为你正确地格式化 URL。

```python 传递 URL 参数 icon=logos:python
import requests

# 定义要发送的参数
payload = {'key1': 'value1', 'key2': 'value2'}

# 发起 GET 请求
r = requests.get('https://httpbin.org/get', params=payload)

# 打印最终请求的 URL
print(r.url)
# https://httpbin.org/get?key1=value1&key2=value2
```

你也可以将一个列表作为值来传递：

```python 传递列表项 icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
# https://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

## 在请求体中发送数据

对于 `POST`、`PUT` 和 `PATCH` 等 HTTP 方法，你通常需要在请求体中发送数据。

### 表单编码数据

最常见的数据发送方式是表单编码数据，这也是浏览器在提交简单表单时所做的工作。你可以向 `data` 参数传递一个字典。

```python 发送表单编码数据 icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}

r = requests.post('https://httpbin.org/post', data=payload)

# httpbin.org 会在响应中回显表单数据
print(r.json()['form'])
# {'key1': 'value1', 'key2': 'value2'}
```

### JSON 编码数据

许多现代 API 倾向于使用 JSON 编码的数据。你可以直接使用 `json` 参数，而无需自己将字典编码为 JSON。Requests 会自动处理编码过程，并将 `Content-Type` 标头设置为 `application/json`。

```python 发送 JSON 数据 icon=logos:python
import requests

payload = {'some': 'data'}

r = requests.post('https://httpbin.org/post', json=payload)

# httpbin.org 会回显 JSON 数据和标头
response_json = r.json()
print(response_json['json'])
# {'some': 'data'}
print(response_json['headers']['Content-Type'])
# application/json
```

### 多部分文件上传

Requests 让上传多部分编码文件变得很简单。只需向 `files` 参数提供一个类文件对象即可。

```python 上传文件 icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}

r = requests.post(url, files=files)

print(r.json()['files'])
# {'file': '... report.xls 的内容 ...'}
```

如果需要，你也可以通过传递一个元组来显式设置文件名、内容类型和自定义标头。

```python 自定义文件上传 icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {'file': ('report.csv', 'some,data,to,send\n', 'application/vnd.ms-excel', {'Expires': '0'})}

r = requests.post(url, files=files)

print(r.json()['files'])
# {'file': 'some,data,to,send\n'}
```

## 其他 HTTP 方法

Requests 为每个主要的 HTTP 动词都提供了一个便捷方法。所有方法的用法都保持一致。

```python 使用其他 HTTP 方法 icon=logos:python
import requests

r = requests.put('https://httpbin.org/put', data={'key': 'value'})
print(f"PUT status: {r.status_code}")

r = requests.delete('https://httpbin.org/delete')
print(f"DELETE status: {r.status_code}")

r = requests.head('https://httpbin.org/get')
print(f"HEAD status: {r.status_code}")

r = requests.options('https://httpbin.org/get')
print(f"OPTIONS status: {r.status_code}")
```

## 自定义标头

要添加或修改 HTTP 标头，请向 `headers` 参数传递一个字典。一个常见的用例是设置自定义的 `User-Agent` 字符串。

```python 发送自定义标头 icon=logos:python
import requests

url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}

r = requests.get(url, headers=headers)
```

---

既然你已经学会了如何创建和自定义请求，下一步就是处理服务器的响应。请继续阅读下一节，了解如何[处理响应](./user-guide-handling-responses.md)。