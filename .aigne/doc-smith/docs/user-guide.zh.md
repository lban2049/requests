# 用户指南

欢迎阅读 Requests 用户指南！本指南将深入介绍该库的核心功能，帮助你掌握常见的工作流，并处理各种 Web 交互场景。我们假设你已经按照 [入门指南](./getting-started.md) 的说明完成了安装和首次请求。

本指南的结构旨在引导你逐步了解 HTTP 请求的生命周期，从创建到响应处理等。每个章节都聚焦于该库的特定方面，并提供以代码为先的实用示例。

<x-cards>
  <x-card data-title="发起请求" data-icon="lucide:send" data-href="/user-guide/making-a-request" data-cta="Learn More">
    学习如何使用 GET、POST 和 PUT 等多种 HTTP 方法，以及如何传递 URL 参数、标头和请求正文。
  </x-card>
  <x-card data-title="处理响应" data-icon="lucide:download" data-href="/user-guide/handling-responses" data-cta="Learn More">
    了解如何以不同格式（文本、JSON、二进制）访问响应内容、检查状态码以及读取响应标头。
  </x-card>
  <x-card data-title="会话对象" data-icon="lucide:orbit" data-href="/user-guide/session-objects" data-cta="Learn More">
    利用会话对象在多个请求之间持久化参数、Cookie 和标头，以提高性能和进行状态管理。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round" data-href="/user-guide/authentication" data-cta="Learn More">
    实现包括基本和摘要身份验证在内的多种身份验证方案，以保护你的请求并访问受保护的资源。
  </x-card>
  <x-card data-title="错误处理" data-icon="lucide:shield-alert" data-href="/user-guide/error-handling" data-cta="Learn More">
    学习预测和处理潜在的请求错误，例如连接问题、超时和不成功的 HTTP 状态。
  </x-card>
</x-cards>

## 后续步骤

学习完这些章节后，你将为在项目中使用 Requests 打下坚实的基础。当你准备好应对更复杂的场景时，可以深入阅读我们的 [高级用法](./advanced-usage.md) 指南，探索自定义超时、代理和 SSL 证书验证等功能。