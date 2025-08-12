# API Reference

This section serves as the comprehensive guide to all public APIs, classes, and methods available within the Requests library. It details their parameters, return values, and provides practical usage examples, enabling you to fully leverage the library's capabilities. For a foundational understanding of how Requests operates, refer to the [Core Concepts](./core-concepts.md) section.

## Understanding the API Structure

The Requests library provides a clean and intuitive API designed for human beings. The API reference is organized into several key areas, allowing you to quickly find information on specific components:

### Top-Level Functions

These functions offer a simplified interface for common HTTP operations like `GET`, `POST`, `PUT`, `DELETE`, and more. They are the quickest way to send a request without needing to manage session state. Learn more about these convenient functions and their usage in [Top-Level Functions](./api-reference-top-level-functions.md).

### Session Object

For persistent parameters across multiple requests, such as cookies, authentication, or proxy settings, the `Session` object is essential. This dedicated section provides an in-depth look at its public methods and attributes, guiding you on how to manage persistent connections and settings effectively. Explore the full capabilities of the `Session` object in the [Session Object](./api-reference-session-object.md) documentation.

### Request & Response Objects

Central to all HTTP communications in Requests are the `Request`, `PreparedRequest`, and `Response` objects. This part of the reference details their properties and methods, explaining how they are constructed, modified, and used to handle incoming data. Understand the lifecycle and attributes of these core objects by visiting [Request & Response Objects](./api-reference-request-response-objects.md).

### Exceptions

Robust error handling is critical for any application. Requests defines a set of custom exception types that can be raised during HTTP requests, such as connection errors, timeouts, and HTTP status code errors. This section lists and describes all custom exception types, enabling you to implement precise error handling in your applications. See a full list and explanation of all custom exceptions in [Exceptions](./api-reference-exceptions.md).

---

With a clear understanding of the Requests API, you are well-equipped to build powerful and reliable HTTP-based applications. To learn how you can contribute to the Requests project or engage with the community, proceed to the [Community & Contribution](./community-contribution.md) section.