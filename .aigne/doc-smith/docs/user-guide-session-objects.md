# Session Objects

The Session object is a powerful tool that allows you to persist parameters across multiple requests. It also reuses the underlying TCP connection for requests made to the same host, which can result in a significant performance increase. In essence, it manages cookies, headers, and connection pooling for you.

While you can make individual requests with `requests.get()`, `requests.post()`, etc., using a `Session` object is highly recommended when you need to make several requests to the same API or website.

## Basic Usage

The `Session` object has the same methods as the top-level `requests` module. Let's start by creating a session and making a simple GET request.

```python
import requests

s = requests.Session()

response = s.get('https://httpbin.org/get')
print(response.status_code)
```

For proper resource management, it's best to use the `with` statement, which ensures the session is closed automatically when you're done with it.

```python
import requests

with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(response.json())
```

## Cookie Persistence

A key feature of a Session is its ability to persist cookies. When you make a request, any cookies set by the server are stored in the Session's cookie jar and automatically sent with subsequent requests to the same domain. This is how a web browser maintains a logged-in state.

Here’s an example that demonstrates this behavior:

```python
import requests

with requests.Session() as s:
    # First, let's visit a URL that sets a cookie
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

    # Now, let's make another request to a different URL that can read cookies
    response = s.get('https://httpbin.org/cookies')

    # The response will show the cookie we received from the first request
    print(response.json())
```

**Example Response**
```json
{
  "cookies": {
    "sessioncookie": "123456789"
  }
}
```
As you can see, the `sessioncookie` set in the first request was automatically included in the second request.

## Persisting Parameters

Sessions are also useful for persisting other request data, such as headers, query parameters, and authentication details. Any dictionaries you assign to the session's properties will be merged with the request-specific parameters.

### Default Headers

If you need to send the same headers with every request, you can set them on the session's `headers` attribute.

```python
import requests

with requests.Session() as s:
    s.headers.update({'x-test-header': 'true'})

    # This request will have the 'x-test-header'
    response_1 = s.get('https://httpbin.org/headers')
    print('Response 1:', response_1.json()['headers']['X-Test-Header'])

    # This request will also have it, along with a new one
    response_2 = s.get('https://httpbin.org/headers', headers={'x-another-header': 'true'})
    print('Response 2:', response_2.json()['headers']['X-Test-Header'])
    print('Response 2:', response_2.json()['headers']['X-Another-Header'])
```

This example shows that the `x-test-header` is sent with both requests, and request-level headers are merged with session-level headers.

### Default Query Parameters

Similarly, you can set default query string parameters on the `params` attribute.

```python
import requests

with requests.Session() as s:
    s.params = {'api_key': 'shared_key'}

    # This will be sent to https://httpbin.org/get?api_key=shared_key
    response = s.get('https://httpbin.org/get')
    print(response.json()['args'])
```

**Example Response**
```json
{
  "api_key": "shared_key"
}
```

## Performance and Connection Pooling

When you make multiple requests to the same host with a `Session` object, it reuses the underlying TCP connection, which can lead to a substantial performance improvement. This process is known as connection pooling.

Here’s a conceptual diagram comparing individual requests with session-based requests:

```d2
direction: down

"App": {
  shape: rectangle
}

"Server": {
  shape: cylinder
}

"Individual Requests": {
  shape: package

  "Req 1": {
    label: "Request 1"
    shape: rectangle
  }
  "TCP 1": {
    label: "New TCP Connection"
  }

  "Req 2": {
    label: "Request 2"
    shape: rectangle
  }
  "TCP 2": {
    label: "New TCP Connection"
  }

  "App" -> "Req 1": "sends"
  "Req 1" -> "TCP 1": "opens"
  "TCP 1" -> "Server": "connects"
  "Server" -> "TCP 1": "responds"
  "TCP 1" -> "Req 1": "delivers"
  "Req 1" -> "App": "returns"

  "App" -> "Req 2": "sends"
  "Req 2" -> "TCP 2": "opens"
  "TCP 2" -> "Server": "connects"
  "Server" -> "TCP 2": "responds"
  "TCP 2" -> "Req 2": "delivers"
  "Req 2" -> "App": "returns"
}

"Session-based Requests": {
  shape: package

  "Session": {
    label: "Session Object"
    shape: rectangle
  }

  "Connection Pool": {
    shape: queue
  }

  "Req 3": {
    label: "Request 1"
    shape: rectangle
  }

  "Req 4": {
    label: "Request 2"
    shape: rectangle
  }

  "App" -> "Session": "creates"
  "Session" -> "Req 3": "sends"
  "Req 3" -> "Connection Pool": "opens new TCP conn"
  "Connection Pool" -> "Server": "connects"
  "Server" -> "Connection Pool": "responds"
  "Connection Pool" -> "Req 3": "delivers"
  "Req 3" -> "Session": "returns"
  
  "Session" -> "Req 4": "sends"
  "Req 4" -> "Connection Pool": "reuses TCP conn"
  "Connection Pool" -> "Server": "connects"
  "Server" -> "Connection Pool": "responds"
  "Connection Pool" -> "Req 4": "delivers"
  "Req 4" -> "Session": "returns"
}

```

By avoiding the overhead of establishing a new connection for every request, sessions can significantly reduce latency, especially when dealing with HTTPS, which requires a TLS handshake.

---

By using `Session` objects, you can write cleaner code, manage state like cookies effortlessly, and improve the performance of your application. The next section will cover how to handle different types of authentication, a task often simplified by using sessions.

Continue to the next section to learn about [Authentication](./user-guide-authentication.md).
