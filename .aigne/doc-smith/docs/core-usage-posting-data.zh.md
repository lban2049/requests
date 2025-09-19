# 发送 POST 数据

通常，你会希望在请求体中发送数据，尤其是在使用 `POST`、`PUT` 和 `PATCH` 方法时。Requests 库为你处理了编码过程，使得这个操作非常简单。

## 发送表单编码的数据

最常见的数据发送方式是作为简单的键值对，类似于 HTML 表单。为此，你可以将一个字典传递给 `post` 方法的 `data` 参数。你的字典在发送前会被自动进行表单编码。

```python 发送表单编码的数据 icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post("https://httpbin.org/post", data=payload)

print(r.json())
```

`httpbin.org` 响应中的 `form` 字段证实了数据已在服务器端被正确接收和解码。

```json 响应体 icon=mdi:code-json
{
  ...
  "form": {
    "key1": "value1", 
    "key2": "value2"
  }, 
  ...
}
```

如果你需要为同一个键发送多个值，也可以向 `data` 参数传递一个元组列表。

## 发送 JSON 数据

现代 API 通常倾向于使用 JSON 编码的数据。你可以直接将字典传递给 `json` 参数，而无需自己将其编码为字符串。Requests 会自动处理序列化，并将 `Content-Type` 标头设置为 `application/json`。

```python 发送 JSON 负载 icon=logos:python
import requests

url = 'https://httpbin.org/post'
payload = {'some': 'data'}

r = requests.post(url, json=payload)

print(r.json())
```

如你在响应中所见，`json` 字段包含了你的原始负载，并且 `Content-Type` 标头也已正确设置。

```json 响应体 icon=mdi:code-json
{
  ...
  "json": {
    "some": "data"
  }, 
  "headers": {
    "Content-Type": "application/json", 
    ...
  },
  ...
}
```

## 多部分编码的文件上传

Requests 支持多部分编码的文件上传，这使得向服务器发送文件变得很容易。只需向 `files` 参数提供一个类文件对象即可。

```python 上传文件 icon=logos:python
import requests

url = 'https://httpbin.org/post'
# 假设 'report.txt' 是同一目录下的一个文件
with open('report.txt', 'rb') as f:
    files = {'file': f}
    r = requests.post(url, files=files)

print(r.json()['files'])
```

为了实现更多控制，你可以通过传递一个元组（而不仅仅是文件对象）来显式设置文件名、内容类型和自定义标头。该元组可以有多种结构：

-   `('filename', file_object)`
-   `('filename', file_object, 'content_type')`
-   `('filename', file_object, 'content_type', custom_headers)`

```python 自定义文件上传 icon=logos:python
import requests

url = 'https://httpbin.org/post'
files = {
    'file': ('report.csv', 'col1,col2\ndata1,data2\n', 'text/csv', {'Expires': '0'})
}

r = requests.post(url, files=files)

print(r.text)
```

你也可以像往常一样提供 `data` 参数，在文件上传的同时发送其他表单数据。

```python 上传文件及表单数据 icon=logos:python
import requests

url = 'https://httpbin.org/post'
with open('report.txt', 'rb') as f:
    files = {'file': f}
    form_data = {'author': 'john_doe', 'year': '2024'}
    
    r = requests.post(url, files=files, data=form_data)

response_data = r.json()
print("Files received:", response_data['files'])
print("Form data received:", response_data['form'])
```

## 原始请求体

对于像 `PUT` 或 `PATCH` 这样的方法，或者如果你需要发送一个非表单编码的负载，你可以直接将一个 `string` 或 `bytes` 传递给 `data` 参数。这些数据将按原样发送。

```python 使用 PUT 发送原始数据 icon=logos:python
import requests
import json

url = 'https://httpbin.org/put'
payload = {'message': 'hello world'}

r = requests.put(url, data=json.dumps(payload))

print(r.json()['data'])
# 输出：'{"message": "hello world"}'
```

以上涵盖了在请求体中发送数据的主要方式，从简单的表单到复杂的文件上传。