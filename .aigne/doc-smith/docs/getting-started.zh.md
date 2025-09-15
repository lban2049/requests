# 快速入门

本指南提供了一条简单的途径，用于安装 Requests 库并发出你的第一个 HTTP 请求。只需几分钟，你就可以从网络上获取数据。

## 安装

在开始之前，请确保你已安装受支持的 Python 版本。Requests 官方支持 Python 3.9 及更高版本。

要安装 Requests，请打开你的终端或命令提示符，并使用 Python 包安装程序 `pip`：

```console Installing Requests icon=logos:python
$ python -m pip install requests
```

该命令将下载并安装最新版本的 Requests 及其基本依赖项，如 `urllib3`、`idna`、`charset_normalizer` 和 `certifi`，以便你拥有开始所需的一切。

## 发出你的第一个请求

安装 Requests 后，发出 Web 请求变得非常简单。让我们从一个基本的 `GET` 请求开始，从一个测试服务中检索一些数据。

以下示例演示了如何发出请求、检查响应并访问其内容。

```python Your First Request icon=logos:python
import requests

# 向一个简单的测试端点发出 GET 请求
r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# 1. 检查 HTTP 状态码
# 状态码 200 OK 表示请求成功
print(f"Status Code: {r.status_code}")

# 2. 访问响应头
# 响应头以一个类字典对象的形式返回
print(f"Content-Type: {r.headers['content-type']}")

# 3. 以文本形式访问响应体
# .text 属性包含原始字符串内容
print(f"Response Text: {r.text}")

# 4. 以 JSON 格式访问响应体
# .json() 方法将 JSON 响应解码为 Python 字典
json_data = r.json()
print(f"JSON Data: {json_data}")
```

我们来分解一下这里发生了什么：

1.  **`import requests`**：我们首先导入该库。
2.  **`requests.get(...)`**：这是该库的核心。它会构建并向指定 URL 发送一个 HTTP `GET` 请求。在本例中，我们还传递了一个 `auth` 元组来轻松处理基本身份验证。
3.  **`r.status_code`**：`r` 对象是 `Response` 类的一个实例，包含了服务器的响应。`status_code` 属性可以让你检查请求是否成功。值为 `200` 表示成功。
4.  **`r.headers`**：这个类字典对象让你能够访问所有的 HTTP 响应头。
5.  **`r.text`**：此属性以纯字符串形式提供响应的有效负载。
6.  **`r.json()`**：当你使用返回 JSON 的 API 时，这个内置方法非常有用。它会自动将响应文本解码为 Python 字典或列表，使数据立即可用。

## 接下来做什么？

恭喜！你已经成功安装了 Requests 并从 Web 获取了数据。现在你已经了解了发出请求和处理响应的基础知识。

要深入了解该库的功能，用户指南是完美的下一步。学习如何发送数据、自定义请求头、管理会话等。

<x-card data-title="用户指南：发出请求" data-icon="lucide:arrow-right-circle" data-href="/user-guide/making-a-request">
探索不同的 HTTP 方法，如 POST 和 PUT，传递 URL 参数，并处理各种类型的请求体。
</x-card>