# 概述

Requests 是一个为人类设计的、简单而优雅的 Python HTTP 库。它使你能够极其轻松地发送 HTTP/1.1 请求，并抽象了手动创建查询字符串和进行表单编码的复杂性。

作为下载次数最多的 Python 包之一，Requests 每周的下载量约为 **3000 万次**，并受到 GitHub 上超过 **1,000,000** 个代码库的信任。你完全可以信赖这份代码。

以下是一个简单的实际应用示例：

```python 一个简单的 GET 请求 icon=logos:python
import requests

r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))

# 检查状态码
>>> r.status_code
200

# 查看响应头
>>> r.headers['content-type']
'application/json; charset=utf8'

# 以文本形式访问响应体
>>> r.text
'{"authenticated": true, ...'

# 或者，将其解码为 JSON
>>> r.json()
{'authenticated': True, ...}
```

## 主要特性

Requests 能够满足构建稳健、可靠的 HTTP 应用程序的需求，并提供了丰富的开箱即用特性：

*   Keep-Alive 和连接池
*   国际化域名和 URL
*   带 Cookie 持久化的会话
*   浏览器风格的 TLS/SSL 验证
*   基本和摘要式身份认证
*   类字典的 Cookies
*   自动内容解压和解码
*   多部分文件上传
*   SOCKS 代理支持
*   连接超时
*   流式下载
*   自动遵循 `.netrc`
*   分块 HTTP 请求

## 接下来该做什么？

准备好深入了解了吗？以下是开启你的 Requests 之旅的后续步骤。

<x-cards>
  <x-card data-title="入门指南" data-icon="lucide:rocket" data-href="/getting-started">
    关于如何安装该库并发出第一个 HTTP 请求的简单分步说明。
  </x-card>
  <x-card data-title="用户指南" data-icon="lucide:book-open" data-href="/user-guide">
    通过涵盖常见用例、代码优先的实用示例，探索 Requests 的核心功能。
  </x-card>
</x-cards>