# Session Objects

The Session object allows you to persist certain parameters across requests. It also persists cookies across all requests made from the Session instance and will use `urllib3`'s connection pooling. So if you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

A Session object has all the methods of the main Requests API.

Let's persist some cookies across requests:

```python Session Cookie Persistence icon=logos:python
import requests

s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# Expected output:
# {
#   "cookies": {
#     "sessioncookie": "123456789"
#   }
# }
```

## Persisting Parameters

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object. Any dictionaries that you pass to a request method will be merged with the session-level values that are set.

For example, headers set at the session level will be combined with any headers you pass to a specific request. However, the headers in the method call will take precedence.

```python Merging Session and Request Headers icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'session-value'})

# The session header is sent along with the request-specific header
r = s.get('https://httpbin.org/headers', headers={'x-test-header-2': 'request-value'})
print(r.json()["headers"])

# A header set in a request method call overrides the session-level header
r_override = s.get('https://httpbin.org/headers', headers={'x-test-header': 'request-override-value'})
print(r_override.json()["headers"])
```

Any object that is passed as a parameter to a request method (e.g., `auth`, `cert`) can also be set at the session level.

```python Session-level Authentication icon=logos:python
import requests

s = requests.Session()
s.auth = ('user', 'pass')

# The auth information is automatically used for this request
r = s.get('https://httpbin.org/basic-auth/user/pass')

print(f"Status Code: {r.status_code}")
print(r.json())
# Expected output:
# Status Code: 200
# {'authenticated': True, 'user': 'user'}
```

Note that method-level parameters are not persisted across requests. If you want to set a parameter for all future requests, you must set it on the session object. To remove a persistent parameter, you can set it to `None` on the session.

## Session as a Context Manager

All Sessions can be used as a context manager. This will ensure the session is closed automatically when the `with` block is exited, even if exceptions are raised. This is useful for cleaning up connections in the pool.

```python Session with Context Manager icon=logos:python
with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Request successful with status code: {response.status_code}")

# The session is now closed, and connections are cleaned up.
```

Using Session objects is a powerful way to manage state, handle authentication, and improve the performance of your application when interacting with web services. For securing your requests, the next step is to dive deeper into different authentication methods.

Now that you've seen how to manage state across requests, let's explore how to handle [Authentication](./user-guide-authentication.md).
