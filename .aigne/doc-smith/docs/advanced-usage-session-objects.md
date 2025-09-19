# Session Objects

The Session object allows you to persist certain parameters across requests. It also persists cookies across all requests made from the Session instance and leverages `urllib3`'s connection pooling. If you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

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

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object. These properties will be applied to all subsequent requests made with that session.

### Session Attributes

The following attributes can be set on a `Session` object to configure default request values:

<x-field-group>
  <x-field data-name="headers" data-type="dict" data-desc="A case-insensitive dictionary of headers to be sent on each request."></x-field>
  <x-field data-name="auth" data-type="tuple | object" data-desc="Default authentication tuple or object to attach to each request."></x-field>
  <x-field data-name="params" data-type="dict" data-desc="Dictionary of querystring data to attach to each request."></x-field>
  <x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar containing all cookies set on this session."></x-field>
  <x-field data-name="proxies" data-type="dict" data-desc="Dictionary mapping protocol or hostname to the URL of the proxy."></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="true" data-desc="Default SSL verification setting. Can be a boolean or a path to a CA bundle."></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-desc="Default SSL client certificate. Can be a path to a .pem file or a ('cert', 'key') tuple."></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="false" data-desc="Default for whether to immediately download response content."></x-field>
  <x-field data-name="max_redirects" data-type="number" data-default="30" data-desc="Maximum number of redirects allowed for a request."></x-field>
  <x-field data-name="hooks" data-type="dict" data-desc="Event-handling hooks for responses."></x-field>
</x-field-group>

### Example: Default Headers and Auth

Here's an example of setting default headers and authentication on a session, which will be present in all subsequent requests.

```python Setting Default Parameters icon=logos:python
import requests

s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# Both 'x-test' and 'x-test2' are sent in this request
response = s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})

print(response.json())
# Expected output might look like this:
# {
#   "headers": {
#     "Accept": "*/*", 
#     "Accept-Encoding": "gzip, deflate", 
#     "Authorization": "Basic dXNlcjpwYXNz", 
#     "Host": "httpbin.org", 
#     "User-Agent": "python-requests/2.31.0", 
#     "X-Amzn-Trace-Id": "Root=...", 
#     "X-Test": "true", 
#     "X-Test2": "true"
#   }
# }
```

## Overriding Session Parameters

While sessions provide sensible defaults, you can override these settings on a per-request basis. Any parameters passed directly to a request method (`get`, `post`, etc.) will be merged with the session-level parameters. The method-level parameters will take precedence in case of a conflict.

For example, if you want to omit session-level headers for a specific request, you can set the header's value to `None` in the method call.

```python Overriding Session Headers icon=logos:python
import requests

with requests.Session() as s:
    s.headers.update({'x-test': 'true', 'x-common': 'default-value'})

    # This request includes all session headers
    # and overrides 'x-test' while adding 'x-test2'
    response1 = s.get('https://httpbin.org/headers', headers={'x-test': 'false', 'x-test2': 'true'})
    print('Response 1 Headers:', response1.json()['headers']['X-Test'], response1.json()['headers']['X-Test2'])
    # Expected: Response 1 Headers: false true

    # This request removes the 'x-test' header for this call only
    response2 = s.get('https://httpbin.org/headers', headers={'x-test': None})
    print('Does Response 2 have X-Test?:', 'X-Test' in response2.json()['headers'])
    # Expected: Does Response 2 have X-Test?: False
```


## Sessions as Context Managers

All sessions can be used as context managers. This is the recommended approach as it ensures that the session is properly closed using `Session.close()` even if an unhandled exception occurs. Closing the session cleans up all underlying connections in the connection pool.

```python Session as Context Manager icon=logos:python
with requests.Session() as s:
    r = s.get('https://httpbin.org/get')
    print(f'Status Code: {r.status_code}')

# The session is automatically closed here
```

By using session objects, you can significantly improve the performance and clarity of your code when dealing with multiple API calls to the same service. For more complex scenarios, you might want to explore how sessions interact with [Authentication](./advanced-usage-authentication.md) mechanisms.