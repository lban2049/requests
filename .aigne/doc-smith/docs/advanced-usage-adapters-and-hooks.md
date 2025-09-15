# Custom Adapters and Hooks

Requests is designed to be highly customizable. For advanced scenarios that go beyond standard HTTP interactions, you can extend its functionality using Transport Adapters and the event hook system. This allows you to integrate custom transport logic, modify request handling, and process responses in sophisticated ways.

## Transport Adapters

At its core, Requests uses Transport Adapters to handle the actual sending of requests over different protocols. The library includes a default `HTTPAdapter` which is used for all `http://` and `https://` requests. You can modify its behavior or even create your own adapter for custom transport mechanisms.

### Customizing the HTTP Adapter

The built-in `HTTPAdapter` can be configured to change its default behavior, such as connection pooling and retry logic. To use a customized adapter, you must mount it to a `Session` object for a specific URL prefix.

The `HTTPAdapter` can be initialized with several parameters to control its behavior:

<x-field data-name="pool_connections" data-type="number" data-default="10" data-desc="The number of urllib3 connection pools to cache."></x-field>
<x-field data-name="pool_maxsize" data-type="number" data-default="10" data-desc="The maximum number of connections to save in the pool."></x-field>
<x-field data-name="max_retries" data-type="number or urllib3.util.retry.Retry" data-default="0" data-desc="The maximum number of retries each connection should attempt for failed connections. By default, retries are disabled."></x-field>
<x-field data-name="pool_block" data-type="boolean" data-default="False" data-desc="Whether the connection pool should block for connections when the pool is full."></x-field>

For example, you can configure a session to automatically retry failed requests up to 3 times:

```python Configuring Retries with HTTPAdapter icon=logos:python
import requests
from requests.adapters import HTTPAdapter

s = requests.Session()

# Configure an adapter with 3 retries
a = HTTPAdapter(max_retries=3)

# Mount the adapter to handle all HTTP requests
s.mount('http://', a)

# Any request made with this session will now retry on failure
try:
    response = s.get('http://a.bad.url/endpoint')
    print(response.status_code)
except requests.exceptions.ConnectionError as e:
    print(f"Request failed after retries: {e}")

```

### Creating a Custom Adapter

For truly custom behavior, you can create your own Transport Adapter by subclassing `requests.adapters.BaseAdapter`. A custom adapter must implement the `send()` method.

However, a more common approach is to subclass `HTTPAdapter` and override specific methods to add functionality without re-implementing the entire HTTP/HTTPS logic.

Here's an example of a custom adapter that adds a specific header to every outgoing request:

```python Custom Header Adapter icon=logos:python
from requests.adapters import HTTPAdapter

class CustomHeaderAdapter(HTTPAdapter):
    def __init__(self, custom_header, *args, **kwargs):
        self.custom_header = custom_header
        super().__init__(*args, **kwargs)

    def add_headers(self, request, **kwargs):
        # This method is called before the request is sent
        request.headers['X-Custom-Header'] = self.custom_header

# Usage
import requests

session = requests.Session()
adapter = CustomHeaderAdapter('MyCustomValue')
session.mount('https://', adapter)

response = session.get('https://httpbin.org/headers')
print(response.json())

```

When you run this code, the response from httpbin.org will show that the `X-Custom-Header` was successfully added to the request.

## Event Hooks

Requests also provides a hook system that allows you to attach callbacks to certain parts of the request process. This is useful for event handling, logging, or modifying the response before it's returned to your application code.

The only hook currently available is `response`, which is triggered after a response is received from the server but before it is returned from the initial request method.

### Using the `response` Hook

A hook is a function that takes the response object as its first argument, along with any other keyword arguments passed to the request method (`get`, `post`, etc.).

You can attach a hook to a `Session` object or to an individual request.

```python Response Hook Example icon=logos:python
import requests

def log_response_status(response, *args, **kwargs):
    print(f"Request to {response.url} completed with status: {response.status_code}")

# Attach hook to a session
session = requests.Session()
session.hooks['response'] = [log_response_status]

print("Making request with a session hook...")
session.get('https://httpbin.org/get')

print("\nMaking request with a per-request hook...")
requests.get('https://httpbin.org/get', hooks={'response': log_response_status})
```

### Modifying the Response

A hook can also return a modified response object. If a hook function returns a value other than `None`, that value will replace the original response. This allows for powerful response manipulation on the fly.

```python Modifying Response with a Hook icon=logos:python
import requests

def add_custom_attribute(response, *args, **kwargs):
    """A hook that adds a custom attribute to the response object."""
    response.custom_message = "Response processed by hook!"
    # Since we modified the response in-place, we don't need to return it.
    # However, you could return a completely different object if needed.
    return None

session = requests.Session()
session.hooks['response'] = [add_custom_attribute]

response = session.get('https://httpbin.org/get')

# Access the custom attribute added by the hook
if hasattr(response, 'custom_message'):
    print(response.custom_message)

```

By leveraging custom adapters and hooks, you can tailor Requests to fit nearly any workflow, from simple retry logic to complex, protocol-specific integrations.

---

For a complete reference of all classes and methods, proceed to the [API Reference](./api-reference.md).