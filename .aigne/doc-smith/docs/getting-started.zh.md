# 入门指南

本指南介绍了安装 Requests 库和发起首次 HTTP 请求的基本步骤，旨在帮助你快速上手。

## 安装

在开始之前，请确保你已安装受支持的 Python 版本。Requests 官方支持 Python 3.9 及更高版本。

Requests 已发布于 Python Package Index (PyPI)，可使用 pip 进行安装：

```console
$ python -m pip install requests
```

该命令将下载并安装 Requests 及其所需的依赖项，例如 `urllib3`、`charset_normalizer`、`idna` 和 `certifi`。

## 发起首次请求

安装 Requests 后，发起 Web 请求非常简单。我们先从向一个测试端点发起基本的 `GET` 请求开始。

```python
import requests

r = requests.get('https://httpbin.org/get')
```

这样就完成了！你刚刚发送了一个 HTTP GET 请求。变量 `r` 现在持有一个 `Response` 对象，其中包含了服务器返回的所有信息，包括内容和状态码。

### 检查响应

你可以轻松地检查响应，以确定请求是否成功，并查看其中包含的数据。

**检查状态码**

`200 OK` 状态是 HTTP 请求成功的标准响应。你可以通过 `status_code` 属性来检查：

```python
>>> r.status_code
200
```

**访问响应内容**

Requests 可以自动将 JSON 响应解码为 Python 字典。使用 `.json()` 方法即可访问数据：

```python
>>> r.json()
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', ...}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

如果响应内容不是 JSON 格式，你可以使用 `.text` 访问原始文本内容：

```python
>>> r.text
'{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", ...
```

## 后续步骤

你已成功安装 Requests 并完成了首次 API 调用。要了解如何发起不同类型的请求（如 `POST` 或 `PUT`）、传递参数以及处理各种响应类型，请继续阅读用户指南。

<x-card data-title="Making a Request" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request" data-cta="Continue to User Guide">
了解如何使用各种 HTTP 方法、传递 URL 参数、设置标头以及在请求正文中发送数据。
</x-card>