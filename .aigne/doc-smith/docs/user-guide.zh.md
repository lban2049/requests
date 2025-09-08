# 用户指南

欢迎阅读 Requests 用户指南。本部分旨在通过实际的、代码优先的示例来探索其核心功能，帮助您充分利用该库。无论您是发送第一个请求还是管理复杂的会话，这些指南都将引导您了解最常见的用例。

每个指南都是独立的，您可以随时跳转到与您的需求最相关的主题。

<x-cards data-columns="2">
  <x-card data-title="发送请求" data-icon="lucide:send-horizontal" data-href="/user-guide/making-a-request">
    学习如何使用 GET、POST、PUT 等各种 HTTP 方法，以及如何传递 URL 参数、标头和请求正文。
  </x-card>
  <x-card data-title="处理响应" data-icon="lucide:arrow-down-left-square" data-href="/user-guide/handling-responses">
    了解如何访问响应内容（文本、JSON、二进制）、检查状态码以及读取响应标头。
  </x-card>
  <x-card data-title="会话对象" data-icon="lucide:orbit" data-href="/user-guide/session-objects">
    利用会话对象在多个请求之间持久化参数、Cookie 和标头，以提高性能和进行状态管理。
  </x-card>
  <x-card data-title="身份验证" data-icon="lucide:key-round" data-href="/user-guide/authentication">
    实现各种身份验证方案，包括基本认证和摘要认证，以保护您的请求。
  </x-card>
  <x-card data-title="错误处理" data-icon="lucide:shield-alert" data-href="/user-guide/error-handling">
    学习预测和处理潜在的请求错误，例如连接问题、超时和错误的 HTTP 状态。
  </x-card>
</x-cards>

### 后续步骤

首先，让我们深入了解[发送请求](./user-guide-making-a-request.md)。