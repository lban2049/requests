# Session Object

The Session object allows you to persist certain parameters across requests. It also persists cookies across all requests made from the Session instance, and will use `urllib3`'s connection pooling. So if you're making several requests to the same host, the underlying TCP connection will be reused, which can result in a significant performance increase.

A Session object has all the methods of the main Requests API.

Let's persist some cookies across requests:

```python Session Usage icon=logos:python
s = requests.Session()

s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get('https://httpbin.org/cookies')

print(r.text)
# '{\n  "cookies": {\n    "sessioncookie": "123456789"\n  }\n}'
```

Sessions can also be used to provide default data to the request methods. This is done by providing data to the properties on a Session object:

```python Session Defaults icon=logos:python
s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})

# both 'x-test' and 'x-test2' are sent
s.get('https://httpbin.org/headers', headers={'x-test2': 'true'})
```

Sessions can also be used as context managers, which will ensure the session is closed even if unhandled exceptions occur.

```python Context Manager icon=logos:python
with requests.Session() as s:
    s.get('https://httpbin.org/get')
```

Any dictionaries that you pass to a request method will be merged with the session-level values that are set. The method-level parameters override session parameters.

## Class Definition

`class requests.sessions.Session`

### Attributes

<x-field-group>
  <x-field data-name="headers" data-type="CaseInsensitiveDict" data-desc="A case-insensitive dictionary of headers to be sent on each Request sent from this Session."></x-field>
  <x-field data-name="auth" data-type="tuple | object" data-desc="Default Authentication tuple or object to attach to every Request."></x-field>
  <x-field data-name="proxies" data-type="dict" data-desc="Dictionary mapping protocol or protocol and host to the URL of the proxy."></x-field>
  <x-field data-name="hooks" data-type="dict" data-desc="Event-handling hooks for hooking into the request process."></x-field>
  <x-field data-name="params" data-type="dict" data-desc="Dictionary of querystring data to attach to each Request."></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="False" data-desc="Stream response content default. When set to True, the response content is not immediately downloaded."></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="True">
    <x-field-desc markdown>Controls SSL certificate verification. Can be a boolean or a path to a CA bundle. Setting to `False` disables verification, which is insecure and not recommended for production.</x-field-desc>
  </x-field>
  <x-field data-name="cert" data-type="string | tuple" data-desc="SSL client certificate default. If a string, it's the path to the cert file (.pem). If a tuple, it's a ('cert', 'key') pair."></x-field>
  <x-field data-name="max_redirects" data-type="integer" data-default="30" data-desc="Maximum number of redirects allowed. Exceeding this limit raises a TooManyRedirects exception."></x-field>
  <x-field data-name="trust_env" data-type="boolean" data-default="True" data-desc="If True, trusts environment settings for proxy configuration, default authentication, and similar configurations."></x-field>
  <x-field data-name="cookies" data-type="RequestsCookieJar" data-desc="A CookieJar containing all cookies set on this session."></x-field>
  <x-field data-name="adapters" data-type="OrderedDict" data-desc="A dictionary of registered connection adapters, mapping URL prefixes to adapter instances."></x-field>
</x-field-group>

### Methods

#### request()

Constructs a `Request`, prepares it, and sends it. Returns a `Response` object.

**Parameters**
<x-field-group>
  <x-field data-name="method" data-type="string" data-required="true" data-desc="Method for the new Request object: GET, OPTIONS, HEAD, POST, PUT, PATCH, DELETE."></x-field>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
  <x-field data-name="params" data-type="dict | bytes" data-required="false" data-desc="Dictionary or bytes to be sent in the query string for the Request."></x-field>
  <x-field data-name="data" data-type="dict | list[tuple] | bytes | file" data-required="false" data-desc="Object to send in the body of the Request."></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="A JSON serializable Python object to send in the body of the Request."></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="Dictionary of HTTP Headers to send with the Request."></x-field>
  <x-field data-name="cookies" data-type="dict | CookieJar" data-required="false" data-desc="Dict or CookieJar object to send with the Request."></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="Dictionary of 'name': file-like-objects for multipart encoding upload."></x-field>
  <x-field data-name="auth" data-type="tuple | callable" data-required="false" data-desc="Auth tuple or callable to enable Basic/Digest/Custom HTTP Auth."></x-field>
  <x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="How many seconds to wait for the server to send data before giving up. Can be a single float or a (connect, read) tuple."></x-field>
  <x-field data-name="allow_redirects" data-type="boolean" data-default="True" data-required="false" data-desc="Set to True by default, can be set to False to disable redirection."></x-field>
  <x-field data-name="proxies" data-type="dict" data-required="false" data-desc="Dictionary mapping protocol or protocol and hostname to the URL of the proxy."></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-required="false" data-desc="Controls whether to verify the server’s TLS certificate. Defaults to the session's verify value."></x-field>
  <x-field data-name="stream" data-type="boolean" data-required="false" data-desc="Whether to immediately download the response content. Defaults to the session's stream value."></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-required="false" data-desc="Path to SSL client cert file (.pem) or a ('cert', 'key') tuple. Defaults to the session's cert value."></x-field>
</x-field-group>

**Returns**
<x-field data-name="response" data-type="requests.Response" data-desc="A Response object."></x-field>

#### get()

Sends a GET request.

**Parameters**
<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="Optional arguments that request takes."></x-field>
</x-field-group>

**Returns**
<x-field data-name="response" data-type="requests.Response" data-desc="A Response object."></x-field>

#### post()

Sends a POST request.

**Parameters**
<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
  <x-field data-name="data" data-type="dict | list[tuple] | bytes | file" data-required="false" data-desc="Object to send in the body of the Request."></x-field>
  <x-field data-name="json" data-type="any" data-required="false" data-desc="A JSON serializable Python object to send in the body of the Request."></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="Optional arguments that request takes."></x-field>
</x-field-group>

**Returns**
<x-field data-name="response" data-type="requests.Response" data-desc="A Response object."></x-field>

#### Other HTTP Methods

The `Session` object also provides convenience methods for all other HTTP verbs:

*   `options(url, **kwargs)`
*   `head(url, **kwargs)`
*   `put(url, data=None, **kwargs)`
*   `patch(url, data=None, **kwargs)`
*   `delete(url, **kwargs)`

These methods function similarly to `get()` and `post()`, accepting a URL and optional keyword arguments that are passed to the underlying `request()` method.

#### prepare_request()

Constructs a `PreparedRequest` for transmission and returns it. The `PreparedRequest` has settings merged from the `Request` instance and those of the `Session`.

**Parameters**
<x-field data-name="request" data-type="requests.Request" data-required="true" data-desc="Request instance to prepare with this session's settings."></x-field>

**Returns**
<x-field data-name="prepared_request" data-type="requests.PreparedRequest" data-desc="The prepared request object."></x-field>

#### send()

Sends a given `PreparedRequest`.

**Parameters**
<x-field-group>
  <x-field data-name="request" data-type="requests.PreparedRequest" data-required="true" data-desc="The PreparedRequest object to send."></x-field>
  <x-field data-name="**kwargs" data-type="any" data-required="false" data-desc="Optional arguments that request takes (e.g., stream, timeout, verify)."></x-field>
</x-field-group>

**Returns**
<x-field data-name="response" data-type="requests.Response" data-desc="A Response object."></x-field>

#### mount()

Registers a connection adapter to a prefix. Adapters are sorted in descending order by prefix length.

**Parameters**
<x-field-group>
  <x-field data-name="prefix" data-type="string" data-required="true" data-desc="The URL prefix to mount the adapter to (e.g., 'https://' or 'http://my-api.com')."></x-field>
  <x-field data-name="adapter" data-type="requests.adapters.BaseAdapter" data-required="true" data-desc="The connection adapter instance."></x-field>
</x-field-group>

#### close()

Closes all adapters and as such the session. This cleans up any open connections.

### Deprecated Functions

#### session()

`requests.sessions.session()`

Returns a `Session` for context-management.

> **Deprecated since version 1.0.0:** This method is only kept for backwards compatibility. New code should use `requests.Session()` to create a session.