# API 参考

本节是 Requests 库中所有公共 API、类和方法的综合指南。它详细介绍了它们的参数、返回值，并提供了实用的使用示例，使您能够充分利用该库的功能。要深入了解 Requests 的运作方式，请参阅[核心概念](./core-concepts.md)部分。

## 理解 API 结构

Requests 库提供了一个简洁直观、专为开发者设计的 API。API 参考分为几个主要部分，以便您快速查找特定组件的信息：

### 顶层函数

这些函数为常见的 HTTP 操作（如 `GET`、`POST`、`PUT`、`DELETE` 等）提供了简化的接口。它们是发送请求而无需管理会话状态的最快捷方式。在[顶层函数](./api-reference-top-level-functions.md)中了解这些便捷函数及其用法的更多信息。

### Session 对象

对于跨多个请求的持久参数，例如 Cookie、身份验证或代理设置，`Session` 对象至关重要。这个专门部分深入探讨了其公共方法和属性，指导您如何有效地管理持久连接和设置。在[Session 对象](./api-reference-session-object.md)文档中探索 `Session` 对象的全部功能。

### Request 和 Response 对象

Requests 中所有 HTTP 通信的核心是 `Request`、`PreparedRequest` 和 `Response` 对象。参考的这部分详细介绍了它们的属性和方法，解释了它们如何构建、修改和用于处理传入数据。通过访问[Request 和 Response 对象](./api-reference-request-response-objects.md)了解这些核心对象的生命周期和属性。

### 异常

健壮的错误处理对于任何应用程序都至关重要。Requests 定义了一组自定义异常类型，这些异常可能在 HTTP 请求期间引发，例如连接错误、超时和 HTTP 状态码错误。本节列出并描述了所有自定义异常类型，使您能够在应用程序中实现精确的错误处理。在[异常](./api-reference-exceptions.md)中查看所有自定义异常的完整列表和解释。

---

通过清晰理解 Requests API，您将能够很好地构建强大而可靠的基于 HTTP 的应用程序。要了解如何为 Requests 项目做出贡献或与社区互动，请继续阅读[社区与贡献](./community-contribution.md)部分。