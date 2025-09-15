# Session Objects

The Session object is one of the most powerful features of Requests. It allows you to persist certain parameters across requests. It also persists cookies over all requests made from the Session instance, and will use `urllib3`'s connection pooling. This means that if you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

A Session object has all the methods of the main Requests API.

Let's persist some cookies across requests:

```python Session Cookie Persistence icon=logos:python
import requests

s = requests.Session()

# The first request to set a cookie
s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')

# A second request to the same domain will automatically include the cookie
r = s.get('https://httpbin.org/cookies')

print(r.text)
# {
#   "cookies": { 
#     "sessioncookie": "123456789"
#   }
# }
```

## Persisting Parameters

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object:

```python Persisting Session-Level Headers icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# Both 'x-test' and 'x-test2' are sent
r_with_both = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
print(r_with_both.json()['headers'])

# The session-level header is still present in a subsequent request
r_with_session_header = s.get('https://httpbin.org/headers')
print(r_with_session_header.json()['headers'])
```

Any dictionaries that you pass to a request method will be merged with the session-level values that are set. The method-level parameters override session parameters.

Let's see what happens when a `None` value is passed. This is useful for removing a header from the session for a specific request:

```python Overriding Session Parameters icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test': 'true'})

# This request will not have the 'x-test' header
r = s.get('https://httpbin.org/headers', headers={'x-test': None})

print(r.json()['headers'])
# {
#   "Accept": "*/*", 
#   "Accept-Encoding": "gzip, deflate", 
#   "Host": "httpbin.org", 
#   "User-Agent": "python-requests/2.28.1", 
#   "X-Amzn-Trace-Id": "..."
# }
```

## Using a Session as a Context Manager

All sessions can also be used as a context manager. This will ensure the session is closed automatically, even if an exception is raised. This is the recommended way to use a Session.

```python Session as a Context Manager icon=logos:python
import requests

with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get('https://httpbin.org/cookies')
    print(r.json())
```

Using a session is essential for making efficient and stateful HTTP requests. Now that you understand how to persist data across multiple requests, you can explore how to handle different types of authentication.

Next, let's dive into [Authentication](./user-guide-authentication.md).