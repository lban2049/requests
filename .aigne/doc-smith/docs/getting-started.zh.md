# 快速入门

本指南提供了让 Requests 库启动并运行的基本步骤。你将学习如何安装它以及如何发出你的第一个简单的 HTTP 请求。

## 安装

在开始之前，请确保你已安装受支持的 Python 版本。Requests 官方支持 Python 3.9 及更高版本。

Requests 可在 Python Package Index (PyPI) 上找到，并可以使用 `pip` 轻松安装。

```console 安装 Requests icon=logos:python
$ python -m pip install requests
```

命令完成后，Requests 就安装在你的环境中，可以随时使用。

## 发出你的第一个请求

安装 Requests 后，发出 HTTP 请求就变得非常简单。让我们从一个基本的 `GET` 请求开始，从一个测试端点检索一些数据。

```python 发出一个 GET 请求 icon=logos:python
import requests

# 向 httpbin.org 测试端点发送 GET 请求
r = requests.get('https://httpbin.org/get')

# 检查 HTTP 状态码是否为成功请求 (200 OK)
print(f"Status Code: {r.status_code}")

# 响应内容可以解码为 JSON
print("Response JSON:")
print(r.json())
```

我们来分解一下这段代码中发生了什么：

1.  **`import requests`**：首先，我们导入 `requests` 库。
2.  **`r = requests.get(...)`**：我们调用 `get()` 函数向指定 URL 发送 HTTP GET 请求。该函数返回一个包含服务器响应的 `Response` 对象。
3.  **`r.status_code`**：此属性为你提供 HTTP 状态码。值为 `200` 表示请求成功。
4.  **`r.json()`**：如果响应内容是 JSON 格式，你可以使用这个方便的方法将其直接解析为 Python 字典。

## 后续步骤

你已成功安装 Requests 并进行了你的第一次 API 调用。要了解如何发送数据、处理不同的 HTTP 方法以及管理标头，请继续阅读用户指南。

<x-card data-title="用户指南：发出请求" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request">
  学习如何使用 GET、POST、PUT 等各种 HTTP 方法，以及如何传递 URL 参数、标头和请求正文。
</x-card>