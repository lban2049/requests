# 高级用法

掌握 [用户指南](./user-guide.md) 中介绍的基础知识后，你可能会发现需要更精细地控制 HTTP 请求。本节将深入探讨 Requests 的高级功能，帮助你处理涉及网络行为、安全和自定义功能的复杂场景。

在本节中，我们将介绍一些概念，帮助你精细控制请求的生命周期。你将学习如何管理连接超时、自动重试失败的请求、通过代理路由流量、精确处理 SSL 证书验证，甚至使用传输适配器和事件钩子来扩展 Requests 的核心功能。

```d2
direction: down

"请求已发起" -> "会话对象"
"会话对象" -> "选择 HTTPAdapter"

"选择 HTTPAdapter" -> "默认 HTTPAdapter": "默认"
"选择 HTTPAdapter" -> "自定义 HTTPAdapter": "自定义"

"默认 HTTPAdapter" -> "代理配置？"
"自定义 HTTPAdapter" -> "代理配置？"

"代理配置？" -> "通过代理路由": "是"
"代理配置？" -> "直接连接": "否"

"通过代理路由" -> "建立连接"
"直接连接" -> "建立连接"

"建立连接" -> "SSL 证书验证": "如果是 HTTPS"
"建立连接" -> "发送请求（含超时和重试）": "如果是 HTTP"

"SSL 证书验证" -> "发送请求（含超时和重试）"
"发送请求（含超时和重试）" -> "接收响应"
"接收响应" -> "响应钩子"
"响应钩子" -> "最终响应对象"
```

<x-cards data-columns="3">
  <x-card data-title="超时、重试和代理" data-href="/advanced-usage/timeouts-retries-proxies" data-icon="lucide:timer">
    网络状况可能无法预测。通过配置超时以防止请求挂起、为瞬时故障设置自动重试，以及通过代理路由请求以保障安全或绕过网络限制，Requests 能帮助你构建更具弹性的应用程序。
  </x-card>
  <x-card data-title="SSL 证书验证" data-href="/advanced-usage/ssl-cert-verification" data-icon="lucide:shield-check">
    通过 HTTPS 进行安全通信已成为标准实践。虽然 Requests 默认处理证书验证，但在某些情况下，你可能需要指定自己的 CA 证书包、为双向 TLS 提供客户端证书，或禁用验证。本节将介绍如何安全地管理这些 SSL/TLS 设置。
  </x-card>
  <x-card data-title="自定义适配器和钩子" data-href="/advanced-usage/adapters-and-hooks" data-icon="lucide:puzzle">
    针对特殊需求，Requests 提供了强大的扩展机制。你可以创建自定义传输适配器来实现独特的传输协议或连接逻辑。此外，钩子系统允许你注册回调函数来检查或修改响应，从而实现日志记录或自定义解析等任务。
  </x-card>
</x-cards>

---

通过利用这些高级功能，你可以量身定制 Requests，以满足应用程序的特定需求。如需了解所有可用类和方法的完整详细说明，请参阅 [API 参考](./api-reference.md)。