# Session Objects

The Session object allows you to persist certain parameters across requests. It also persists cookies across all requests made from the Session instance and leverages `urllib3`'s connection pooling. So if you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

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

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object:

```python Session with Default Headers icon=logos:python
import requests

s = requests.Session()
s.headers.update({'x-test-header': 'true'})

# The 'x-test-header' is sent on both requests
r_one = s.get('https://httpbin.org/headers')
print(r_one.json())

r_two = s.get('https://httpbin.org/headers', headers={'x-another-header': 'true'})
print(r_two.json())
```

### Parameter Precedence

Any dictionaries that you pass to a request method will be merged with the session-level values. However, the method-level parameters will override any duplicate keys in the session parameters for that single request. 

For example:

```python Overriding Session Headers icon=logos:python
import requests

s = requests.Session()

# Set a default header for the session
s.headers.update({'Accept': 'application/json'})

# This request will use the session's 'Accept' header
res_json = s.get('https://httpbin.org/headers')
print(f"Request 1 Accept header: {res_json.json()['headers']['Accept']}")

# This request will override the session's 'Accept' header for this call only
res_html = s.get('https://httpbin.org/headers', headers={'Accept': 'text/html'})
print(f"Request 2 Accept header: {res_html.json()['headers']['Accept']}")

# A third request will revert to using the session's default header
res_json_again = s.get('https://httpbin.org/headers')
print(f"Request 3 Accept header: {res_json_again.json()['headers']['Accept']}")
```

This applies to other session-level settings as well, such as `auth`, `params`, `proxies`, `verify`, and `cert`.

### Session as a Context Manager

All sessions can be used as a context manager. This will ensure the session is closed automatically, even if an exception is raised. This is useful for cleaning up connections.

```python Session as Context Manager icon=logos:python
with requests.Session() as s:
    response = s.get('https://httpbin.org/get')
    print(f"Status Code: {response.status_code}")
# The session is automatically closed here
```

Using a Session object is a great way to make your requests more efficient and your code cleaner, especially when interacting with the same API endpoint multiple times.

To learn how to manage credentials across requests, continue to the [Authentication](./user-guide-authentication.md) guide.