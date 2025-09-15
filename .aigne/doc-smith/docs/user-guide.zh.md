# 用户指南

欢迎阅读 Requests 用户指南！本指南旨在帮助您熟悉该库的核心功能。我们将通过实用的、代码优先的示例，涵盖从发送您的第一个请求到处理复杂身份验证方案等最常见的用例。阅读完本指南后，您将能够自信地将 Requests 集成到您的项目中。

浏览以下各节，掌握在 Python 中使用 HTTP 的基础知识。

<x-cards data-columns="2">
  <x-card data-title="发起请求" data-href="/user-guide/making-a-request" data-icon="lucide:send">
    学习如何使用 GET、POST、PUT 等各种 HTTP 方法，以及如何传递 URL 参数、请求头和请求体。
  </x-card>
  <x-card data-title="处理响应" data-href="/user-guide/handling-responses" data-icon="lucide:arrow-down-left-square">
    了解如何访问响应内容（文本、JSON、二进制）、检查状态码以及读取响应头。
  </x-card>
  <x-card data-title="会话对象" data-href="/user-guide/session-objects" data-icon="lucide:repeat">
    利用会话对象在多个请求之间保持参数、Cookie 和请求头，以提升性能和进行状态管理。
  </x-card>
  <x-card data-title="身份验证" data-href="/user-guide/authentication" data-icon="lucide:key-round">
    实施包括基本认证和摘要认证在内的各种身份验证方案，以确保请求安全。
  </x-card>
  <x-card data-title="错误处理" data-href="/user-guide/error-handling" data-icon="lucide:shield-alert">
    学习预测和处理潜在的请求错误，例如连接问题、超时和错误的 HTTP 状态码。
  </x-card>
</x-cards>

## 后续步骤

本指南内容按顺序组织。我们建议从[发起请求](./user-guide-making-a-request.md)开始，以打下坚实的基础。在您熟悉此处涵盖的主题后，可以在我们的[高级用法](./advanced-usage.md)部分探索更复杂的场景。