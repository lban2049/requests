# 概述

![Requests Logo](../../../ext/requests-logo.svg)

Requests 是一个为人类构建的、优雅简洁的 Python HTTP 库。它能让你轻松发送 HTTP/1.1 请求，并封装了复杂的网络连接，使你能够专注于与服务交互和在应用程序中处理数据。

它是下载次数最多的 Python 包之一，每周下载量约 3000 万次，同时也是 GitHub 上超过 1,000,000 个代码库的依赖项。你可以完全信赖此代码。

```d2
direction: down

"你的 Python 应用": {
  shape: rectangle
}

"Requests 库": {
  shape: package
  "import requests"
}

"Web 服务器 / API": {
  shape: cylinder
  "远程 HTTP 服务"
}

"你的 Python 应用" -> "Requests 库": "进行简单的 API 调用\n(例如，requests.get())"
"Requests 库" -> "Web 服务器 / API": "处理复杂的 HTTP 细节\n(连接池、SSL 等)"
"Web 服务器 / API" -> "Requests 库": "HTTP 响应"
"Requests 库" -> "你的 Python 应用": "返回一个简单的 Response 对象"
```

## 一个简单的请求

使用 Requests，你只需几行代码即可执行一个复杂的、带身份验证的 GET 请求，并能通过直观的方法与响应数据进行交互。

```python
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
'{"authenticated": true, ...'
>>> r.json()
{'authenticated': True, ...}
```

## 核心功能

Requests 具备全面的功能集，足以支持构建稳健可靠的 HTTP 应用程序。

<x-cards data-columns="2">
  <x-card data-title="连接管理" data-icon="lucide:plug-zap">
    Keep-Alive、连接池和连接超时等功能可确保网络通信的稳健与高效。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round">
    内置对基本和摘要式身份验证方案的支持。
  </x-card>
  <x-card data-title="会话持久化" data-icon="lucide:cookies">
    使用会话对象可以在对同一主机的多次请求之间保持 Cookie 和其他参数。
  </x-card>
  <x-card data-title="数据处理" data-icon="lucide:file-code-2">
    自动内容解压、多部分文件上传以及用于处理 JSON 数据的简单 `.json()` 方法。
  </x-card>
  <x-card data-title="SSL 验证" data-icon="lucide:shield-check">
    默认提供浏览器风格的 TLS/SSL 验证，以确保连接安全。
  </x-card>
  <x-card data-title="代理支持" data-icon="lucide:route">
    可轻松通过 SOCKS 代理路由你的请求。
  </x-card>
</x-cards>

## 后续步骤

<x-card data-title="开始使用" data-icon="lucide:rocket" data-href="/getting-started" data-cta="安装 Requests">
  准备好开始了吗？请遵循简单明了的分步说明来安装该库并发出你的第一个 HTTP 请求。
</x-card>