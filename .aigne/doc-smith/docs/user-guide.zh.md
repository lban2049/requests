# 用户指南

本指南将深入探讨 Requests 库的核心功能。内容超越基础知识，涵盖了您将遇到的最常见用例，从发出各类请求到处理响应和管理会话。其中的示例都非常实用，可直接用于您的项目。

如果您尚未安装 Requests 或还未发出您的第一个请求，请从 [入门指南](./getting-started.md) 开始。

### 标准工作流程

使用 Requests 库的典型交互遵循一个简单的模式：构建请求、发送请求，然后处理响应。这一工作流程能让您高效地与 Web 服务和 API 进行交互。

```d2
direction: down

"Start" -> "构建请求\n(例如, requests.get)"
"构建请求\n(例如, requests.get)" -> "向 URL 发送请求"
"向 URL 发送请求" -> "接收响应对象"
"接收响应对象" -> inspect_response: "检查响应"

inspect_response -> "检查状态码\n(r.raise_for_status())" -> "处理潜在的 HTTPError" -> "结束"
inspect_response -> "访问内容\n(r.text, r.json(), r.content)" -> "使用响应中的数据" -> "结束"
```

本指南分为以下几个部分，每个部分都侧重于该库的一个关键方面。

<x-cards data-columns="2">
  <x-card data-title="发出请求" data-icon="lucide:send" data-href="/user-guide/making-a-request">
    学习如何发送 GET、POST 和 PUT 等各种 HTTP 请求。本节内容涵盖传递 URL 参数、自定义标头以及不同类型的请求正文（包括表单数据和 JSON 负载）。
  </x-card>
  <x-card data-title="处理响应" data-icon="lucide:arrow-down-left" data-href="/user-guide/handling-responses">
    请求发送后，您会收到一个 `Response` 对象。本节将介绍如何以不同格式（文本、JSON、二进制）访问响应正文、检查状态码以及读取标头。
  </x-card>
  <x-card data-title="会话对象" data-icon="lucide:book-copy" data-href="/user-guide/session-objects">
    使用 `Session` 对象可以在多个请求之间保持参数和 Cookie。通过复用 TCP 连接，这种方式可以提高性能，对于高效的 API 客户端而言至关重要。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round" data-href="/user-guide/authentication">
    许多 API 都需要身份验证。Requests 提供了多种内置方案（如基本认证和摘要认证），可帮助您轻松保护请求安全。
  </x-card>
  <x-card data-title="错误处理" data-icon="lucide:shield-alert" data-href="/user-guide/error-handling">
    网络请求可能会失败。一个稳健的应用程序必须能够处理连接问题、超时和错误的 HTTP 状态。学习如何捕获和管理异常，以构建更具韧性的应用程序。
  </x-card>
</x-cards>

---

掌握这些核心概念后，您将能够轻松处理大多数常见的 HTTP 交互。当您准备好应对更复杂的场景时，例如配置超时、使用代理或验证 SSL 证书，请继续阅读 [高级用法](./advanced-usage.md) 指南。