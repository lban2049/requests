# 快速入门

本页面提供了一份简明指南，指导你如何安装 Requests 库并发起你的第一个 HTTP 请求。只需几分钟，你就可以开始使用了。

## 前提条件

在安装 Requests 之前，请确保你有一个兼容的 Python 版本。Requests 官方支持 Python 3.9 及更新版本。

## 安装

安装 Requests 的推荐方法是使用 pip。打开你的终端并运行以下命令：

```console
$ python -m pip install requests
```

该命令会从 Python 包索引 (PyPI) 获取最新版本的 Requests 并进行安装，同时也会安装其所需的依赖项，例如 `urllib3`、`charset_normalizer`、`idna` 和 `certifi`。

## 发起你的第一个请求

安装 Requests 后，你就可以开始发起 Web 请求了。下面的示例展示了如何发送一个简单的 `GET` 请求并检查返回的内容。

```python
import requests

# 向一个公共测试 API 发送 GET 请求
r = requests.get('https://httpbin.org/get')

# 检查 HTTP 状态码（200 表示成功）
print(f"Status Code: {r.status_code}")

# 访问响应头
print(f"Content-Type: {r.headers['content-type']}")

# 将响应体作为 Python 字典获取
print("Response JSON:")
print(r.json())
```

运行此脚本将产生类似如下的输出：

```text
Status Code: 200
Content-Type: application/json; charset=utf8
Response JSON:
{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.32.3', 'X-Amzn-Trace-Id': 'Root=...'}, 'origin': '...', 'url': 'https://httpbin.org/get'}
```

这演示了基本的工作流程：使用像 `requests.get()` 这样的函数来发起请求，然后使用返回的 `Response` 对象来访问服务器响应的详细信息。

## 后续步骤

现在你已经成功安装了 Requests 并完成了一个基本请求，可以开始探索它的其他功能了。

<x-card data-title="用户指南" data-icon="lucide:book-open" data-href="/user-guide" data-cta="探索指南">
  深入阅读用户指南，了解如何发起不同类型的请求、处理各种响应类型、使用会话对象以提升性能等更多内容。
</x-card>

该指南为库的大多数核心功能提供了全面的示例。