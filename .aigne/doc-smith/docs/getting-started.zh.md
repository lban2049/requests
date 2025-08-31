# 入门指南

本页将简要介绍如何安装 Requests 库并发出你的第一个 HTTP 请求。只需几分钟，你就可以上手使用。

## 前提条件

安装 Requests 前，请确保你的 Python 版本兼容。根据项目设置文件的说明，Requests 官方支持 Python 3.9 及以上版本。

## 安装

推荐使用 Python 的包安装工具 `pip` 来安装 Requests。请打开终端并运行以下命令：

```console
$ python -m pip install requests
```

该命令会从 Python Package Index (PyPI) 获取最新版本的 Requests 并进行安装。同时，它还会安装库所需的依赖项，如 `urllib3`、`charset_normalizer`、`idna` 和 `certifi` 等，这些依赖项负责处理安全、可靠地发出 HTTP 请求的底层细节。

## 发出你的第一个请求

安装 Requests 后，你就可以开始发出 Web 请求了。其基本流程是向一个 URL 发送请求，然后处理服务器返回的响应。

下图说明了这一基本的请求-响应周期：

```d2
shape: sequence_diagram
direction: right

"你的 Python 脚本"
"Requests 库"
"Web 服务器 (httpbin.org)"

"你的 Python 脚本" -> "Requests 库": 调用 `requests.get(...)`
"Requests 库" -> "Web 服务器 (httpbin.org)": "发送 HTTP GET 请求" {
  style.animated: true
}
"Web 服务器 (httpbin.org)" -> "Requests 库": "返回 HTTP 响应" {
  style.animated: true
  style.stroke: green
}
"Requests 库" -> "你的 Python 脚本": "返回响应对象" {
  style.stroke: green
}
```

下面介绍如何在代码中实现这一过程。以下示例展示了如何发送一个简单的 `GET` 请求，并查看服务器返回的内容。

```python
import requests

# 向一个公开的测试 API 发送 GET 请求
r = requests.get('https://httpbin.org/get')

# 检查 HTTP 状态码（200 表示成功）
print(f"Status Code: {r.status_code}")

# 访问响应头
print(f"Content-Type: {r.headers['content-type']}")

# 将响应体以 Python 字典格式获取
print("响应 JSON:")
print(r.json())
```

运行此脚本将产生类似如下的输出：

```text
Status Code: 200
Content-Type: application/json
Response JSON:
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate, br', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=...'}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

这展示了基本的工作流程：使用 `requests.get()` 等函数发出请求，然后通过返回的 `Response` 对象（`r`）来访问服务器响应的详细信息，包括状态码、响应头和 JSON 格式的响应体。

## 后续步骤

既然你已经成功安装了 Requests 并发出了一个基本请求，就可以开始探索它的其他功能了。

<x-card data-title="用户指南" data-icon="lucide:book-open" data-href="/user-guide" data-cta="浏览指南">
  深入阅读用户指南，了解如何发出不同类型的请求、处理各种响应类型、使用会话对象提高性能等内容。
</x-card>

该指南为库的大部分核心功能都提供了全面的示例。