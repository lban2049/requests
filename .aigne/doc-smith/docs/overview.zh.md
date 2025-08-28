# 概述

![Requests Logo](../../../ext/requests-logo.svg)

Requests 是一个为人类设计的、优雅而简单的 Python HTTP 库。它让你可以轻松发送 HTTP/1.1 请求，并处理查询字符串、表单编码和连接管理等复杂工作。作为下载次数最多的 Python 包之一（每周下载量约 3000 万次），同时也是 GitHub 上超过 1,000,000 个代码仓库的依赖项，Requests 提供了值得你信赖的可靠代码。

### 快速示例

发起网页请求非常简单。以下是获取网页并检查响应的方法：

```python
import requests

r = requests.get('https://www.python.org')

>>> r.status_code
200

>>> 'Python is a programming language' in r.text
True
```

此示例发送一个 `GET` 请求。该库会管理连接，并返回一个包含服务器数据和元数据的 `Response` 对象。

### 请求-响应周期

该库简化了你的应用程序与 Web 服务器之间的交互。其流程包括创建请求、接收响应和处理返回的数据。

```mermaid
flowchart TD
    A["你的应用程序"] -- "调用 requests.get(...)" --> B["Requests 库"];
    B -- "发送 HTTP 请求" --> C["Web 服务器"];
    C -- "返回 HTTP 响应" --> B;
    B -- "创建响应对象" --> A;
    A -- "访问 r.status_code, r.text" --> D["处理数据"];
```

### 核心功能

Requests 旨在支持可靠 HTTP 应用程序的开发，并包含众多功能。

<x-cards data-columns="3">
  <x-card data-title="连接池" data-icon="lucide:network">
    通过 Keep-Alive 复用底层 TCP 连接，以提高性能。
  </x-card>
  <x-card data-title="会话对象" data-icon="lucide:cookie">
    在多个请求之间保持参数、Cookie 和标头，以实现有状态的交互。
  </x-card>
  <x-card data-title="SSL 验证" data-icon="lucide:shield-check">
    自动验证 HTTPS 请求的服务器证书，类似于网页浏览器。
  </x-card>
  <x-card data-title="自动解压" data-icon="lucide:file-archive">
    原生解码 `gzip` 和 `deflate` 压缩内容。
  </x-card>
  <x-card data-title="文件上传" data-icon="lucide:upload">
    通过直观的界面简化多部分文件上传的发送过程。
  </x-card>
  <x-card data-title="内置身份验证" data-icon="lucide:key-round">
    包含用于基本和摘要式身份验证方案的简单辅助工具。
  </x-card>
</x-cards>

---

本概述介绍了 Requests 库的用途和主要功能。要安装该库并发起你的第一个请求，请继续阅读下一部分。

➡️ **下一步：[快速入门](./getting-started.md)**
