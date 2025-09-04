# 快速入门

本指南提供了安装 Requests 库和发起首次 HTTP 请求的基本步骤，旨在帮助你快速上手。

## 安装

开始之前，请确保你已安装受支持的 Python 版本（3.9+）。Requests 已发布在 Python Package Index (PyPI) 上，可以使用 `pip` 进行安装。

```console
$ python -m pip install requests
```

这条命令会下载并安装 Requests 及其所有必要的依赖项，包括 `urllib3`、`certifi`、`charset_normalizer` 和 `idna`。

## 发起你的第一个请求

安装 Requests 后，你就可以开始发起 HTTP 请求了。这个过程非常简单。以下是如何向一个测试端点发送 `GET` 请求的示例。

```python
import requests

r = requests.get('https://httpbin.org/get')
```

执行此代码后，你将得到一个名为 `r` 的 `Response` 对象。该对象包含了服务器返回的所有信息，例如内容、状态码和标头。

## 检查响应

`Response` 对象让你可以轻松访问服务器响应的详细信息。以下是你将使用的一些最常见的属性。

### 状态码

你可以检查 HTTP 状态码来验证请求是否成功。状态码 `200` 表示成功。

```python
>>> r.status_code
200
```

### 响应标头

服务器的响应标头以一个类字典对象的形式提供。你可以通过键来访问任何标头。

```python
>>> r.headers['content-type']
'application/json'
```

### 响应体

对于基于文本的响应，你可以使用 `.text` 属性以字符串形式访问其内容。

```python
>>> r.text
'{\n  "args": {}, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.XX.X", \n  }, \n  "origin": "...", \n  "url": "https://httpbin.org/get"\n}'
```

如果端点返回 JSON（这在许多 API 中很常见），Requests 提供了一个方便的内置 JSON 解码器。

```python
>>> r.json()
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.XX.X'}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

## 后续步骤

现在你已经成功安装了 Requests 并执行了一个基本的 `GET` 请求。要深入了解该库的功能，下一步是探索构造请求和处理不同类型数据的各种方法。

<x-card data-title="发起请求" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request" data-cta="继续阅读用户指南">
  学习如何使用 GET、POST 和 PUT 等各种 HTTP 方法，以及如何传递 URL 参数、标头和请求体。
</x-card>