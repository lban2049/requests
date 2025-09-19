# API 参考

欢迎使用 Requests 库的官方 API 参考。本节提供了所有公共类、方法、函数和异常的详细文档。它旨在帮助需要深入了解特定组件以构建稳健 HTTP 应用程序的开发者。

有关实际示例和常见用例，请参阅[核心用法](./core-usage.md)和[高级用法](./advanced-usage.md)指南。

<x-cards data-columns="3">
  <x-card data-title="请求和响应对象" data-icon="lucide:arrow-right-left" data-href="/api-reference/request-response-objects">
    探索驱动每次交互的核心对象：`Request`、`PreparedRequest` 和 `Response`。了解它们的属性和方法，以精细控制出站请求并处理入站数据。
  </x-card>
  <x-card data-title="Session 对象" data-icon="lucide:book-copy" data-href="/api-reference/session-object">
    了解如何使用 `Session` 对象持久化 Cookie、利用连接池，并在多个请求中应用默认设置，以提高性能并使代码更简洁。
  </x-card>
  <x-card data-title="异常" data-icon="lucide:shield-alert" data-href="/api-reference/exceptions">
    Requests 异常层次结构的完整指南。学习如何预见并妥善处理网络问题、HTTP 错误和其他潜在问题。
  </x-card>
</x-cards>

## 顶层 API

使用 Requests 的最简单方式是通过顶层函数式 API，它为常见的 HTTP 方法提供了便捷的包装器。这些函数是大多数用户的主要入口点。

| 函数 | 描述 |
|---|---|
| `requests.request(method, url, **kwargs)` | 所有其他方法函数都调用的主函数。构建并发送一个 `Request`。 |
| `requests.get(url, params=None, **kwargs)` | 发送一个 GET 请求。 |
| `requests.post(url, data=None, json=None, **kwargs)` | 发送一个 POST 请求。 |
| `requests.put(url, data=None, **kwargs)` | 发送一个 PUT 请求。 |
| `requests.patch(url, data=None, **kwargs)` | 发送一个 PATCH 请求。 |
| `requests.delete(url, **kwargs)` | 发送一个 DELETE 请求。 |
| `requests.head(url, **kwargs)` | 发送一个 HEAD 请求。 |
| `requests.options(url, **kwargs)` | 发送一个 OPTIONS 请求。 |

虽然这些函数功能强大，但对于在多个请求之间保持 Cookie 等高级场景，您应该使用 [Session 对象](./api-reference-session-object.md)。