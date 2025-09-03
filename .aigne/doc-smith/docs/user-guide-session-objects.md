# Session Objects

The Session object allows you to persist certain parameters across requests. It also persists cookies over all requests made from the Session instance, and will use `urllib3`'s connection pooling. So if you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

A Session object has all the methods of the main Requests API.

Let's persist some cookies across requests:

```python
import requests

s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object:

```python
import requests

s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# both 'x-test' and 'x-test2' are sent
r = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})

print(r.text)
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Basic dXNlcjpwYXNz", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.32.3", 
#     "X-Amzn-Trace-Id": "Root=1-66a7b732-2d85835b446108130386811a", 
#     "X-Test": "true", 
#     "X-Test2": "true"
#   }
# }
```

Any dictionaries that you pass to a request method will be merged with the session-level values that are set. The method-level parameters override session parameters.

### Session Workflow Diagram

The following diagram illustrates how a Session object maintains state, like cookies and headers, across multiple requests.

```d2
direction: down

"Your App": {
  shape: rectangle
}

"requests.Session()": {
  shape: package
  "Cookies": { shape: stored_data }
  "Headers": { shape: document }
  "Auth": { shape: document }
}

"Remote Server": {
  shape: cylinder
}

"Your App" -> "requests.Session()": "1. Create Session"
"requests.Session()" -> "Remote Server": "2. Make Request 1 (e.g., Login)"
"Remote Server" -> "requests.Session()": "3. Receives Response + Cookies"
"requests.Session()" -> "Remote Server": "4. Make Request 2 (sends stored cookies)"
"Remote Server" -> "requests.Session()": "5. Receives authenticated response"
"requests.Session()" -> "Your App": "Returns final response"

```

Note, however, that method-level parameters will *not* be persisted across requests, even if using a session. This example will only send the cookies with the first request, but not the second:

```python
import requests

s = requests.Session()

r = s.get('https://httpbin.org/cookies', cookies={'from-my': 'browser'})
print(r.text)
# '{\n  "cookies": {\n    "from-my": "browser"\n  }\n}'

r = s.get('https://httpbin.org/cookies')
print(r.text)
# '{\n  "cookies": {}\n}'
```

If you want to remove a property from the Session, you can set its value to `None`. For instance, to remove session-level headers:

```python
s.headers = None
```

### Context Manager

Sessions can also be used as a context manager, which will ensure the session is closed even if an exception is raised. This is the recommended way to use a Session.

```python
with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

Closing a session cleans up all the adapters, which in turn closes any pooled connections.

All of the values that are contained within a session are directly available to you. See the [Session API Docs](https://requests.readthedocs.io/en/latest/api/#requests.Session) for more information.

Now that you understand how to manage state with sessions, let's explore how to implement different types of [Authentication](./user-guide-authentication.md).
