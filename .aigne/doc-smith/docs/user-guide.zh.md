# 用户指南

欢迎阅读 Requests 用户指南。本节旨在通过实践性的代码示例，帮助您探索该库的核心功能，从而充分利用它。无论您是发送第一个请求，还是管理复杂的会话，这些指南都将引导您逐步了解最常见的使用场景。

每个指南都独立成篇，您可以随时跳转到最符合您需求的主题。

<x-cards data-columns="2">
  <x-card data-title="发送请求" data-icon="lucide:send-horizontal" data-href="/user-guide/making-a-request">
    学习如何使用 GET、POST、PUT 等多种 HTTP 方法，以及如何传递 URL 参数、请求头和请求体。
  </x-card>
  <x-card data-title="处理响应" data-icon="lucide:arrow-down-left-square" data-href="/user-guide/handling-responses">
    了解如何访问响应内容（文本、JSON、二进制）、检查状态码以及读取响应头。
  </x-card>
  <x-card data-title="会话对象" data-icon="lucide:orbit" data-href="/user-guide/session-objects">
    使用会话对象在多个请求之间保持参数、Cookie 和请求头，以提升性能并进行状态管理。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round" data-href="/user-guide/authentication">
    实现包括基本和摘要式身份验证在内的多种身份验证方案，以确保请求安全。
  </x-card>
  <x-card data-title="错误处理" data-icon="lucide:shield-alert" data-href="/user-guide/error-handling">
    学习如何预判和处理潜在的请求错误，如连接问题、超时和错误的 HTTP 状态码。
  </x-card>
</x-cards>

### 后续步骤

首先，让我们深入了解[发送请求](./user-guide-making-a-request.md)。