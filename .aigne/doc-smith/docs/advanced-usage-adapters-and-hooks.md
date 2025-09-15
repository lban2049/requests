# Custom Adapters and Hooks

While Requests provides a powerful and straightforward interface for most HTTP communication needs, it also offers a highly extensible architecture for advanced scenarios. You can gain fine-grained control over the request lifecycle by implementing custom Transport Adapters and leveraging the event hook system.

This guide will walk you through how to extend Requests' core functionality to handle custom transport logic, modify request/response objects on the fly, and integrate custom processing into your HTTP workflows. For a complete technical reference, please see the [API Reference](./api-reference.md).

## Transport Adapters

Transport Adapters are the core mechanism through which Requests handles sending requests. When you make a request, a `Session` object determines the appropriate adapter for the given URL scheme (e.g., `http://` or `https://`) and uses it to manage the connection and dispatch the request.

The base class for all adapters is `BaseAdapter`. Any custom adapter must inherit from it and implement the `send()` and `close()` methods. However, for most use cases, you'll want to subclass the built-in `HTTPAdapter`.

### HTTPAdapter

The `HTTPAdapter` provides a general-purpose interface for HTTP and HTTPS connections, built on top of the `urllib3` library. It manages connection pools, retries, and proxy configurations.

You can create an instance of `HTTPAdapter` and mount it to a `Session` object to configure its behavior for a specific protocol or domain. A common use case is to set up a custom retry strategy.

<x-field data-name="pool_connections" data-type="number" data-default="10" data-desc="The number of urllib3 connection pools to cache."></x-field>
<x-field data-name="pool_maxsize" data-type="number" data-default="10" data-desc="The maximum number of connections to save in the pool."></x-field>
<x-field data-name="max_retries" data-type="number or urllib3.util.retry.Retry" data-default="0" data-desc="The maximum number of retries each connection should attempt. You can pass an integer or a configured `Retry` object for more granular control."></x-field>
<x-field data-name="pool_block" data-type="boolean" data-default="false" data-desc="Whether the connection pool should block for connections when none are free."></x-field>

**Example: Configuring Connection Retries**

By default, Requests does not retry failed connections. You can easily change this behavior by mounting a pre-configured `HTTPAdapter`.

```python Configuring and Mounting an HTTPAdapter icon=logos:python
import requests

s = requests.Session()

# Configure an adapter with 3 retries for all requests to http:// and https://
a = requests.adapters.HTTPAdapter(max_retries=3)
s.mount('http://', a)
s.mount('https://', a)

# Make a request using the session with the custom adapter
try:
    response = s.get('http://a.bad.domain/will/fail')
except requests.exceptions.ConnectionError as e:
    print(f"Request failed after retries: {e}")

```
This example creates a `Session` and mounts an `HTTPAdapter` configured to retry failed connections up to three times. This applies only to specific errors like DNS failures, socket connection errors, and connection timeouts, not to requests where data has already been sent to the server.

### Creating a Custom Adapter

For truly custom behavior, such as implementing a non-HTTP transport protocol or modifying low-level connection logic, you can subclass `HTTPAdapter` and override its methods. Some key methods you might override include:

| Method | Description |
|---|---|
| `send()` | The main method that sends a `PreparedRequest` and returns a `Response` object. |
| `init_poolmanager()` | Initializes the `urllib3.PoolManager`. |
| `proxy_manager_for()` | Returns a `urllib3.ProxyManager` for a given proxy. |
| `cert_verify()` | Handles SSL certificate verification logic. |
| `build_response()` | Builds a `requests.Response` from a `urllib3` response. |


## Event Hooks

Requests provides a hook system that allows you to attach callable functions to specific parts of the request process. These hooks are useful for logging, modifying requests or responses, or triggering events.

The primary available hook is `response`, which is called after a response is received from the server but before it's returned to the calling code.

A hook function receives the data associated with the event (e.g., the `Response` object) as its first argument. It can perform actions with this data or even return a modified version of it, which will then be used by Requests.

### Using the `response` Hook

You can register hooks on a `Session` object to apply them to all requests made with that session, or on a per-request basis.

**Example: Logging Response Headers**

Here is an example of a simple hook that prints the status code and headers of every response.

```python Attaching a Response Hook icon=logos:python
import requests

def log_response_details(response, *args, **kwargs):
    """A hook function to log response status and headers."""
    print(f"Status Code: {response.status_code}")
    print("--- Headers ---")
    for key, value in response.headers.items():
        print(f"{key}: {value}")
    print("---------------")

# Create a session and attach the hook
session = requests.Session()
session.hooks['response'] = [log_response_details]

# All requests made with this session will trigger the hook
print("Making request to httpbin.org...")
session.get('https://httpbin.org/get')

print("\nMaking another request...")
session.get('https://httpbin.org/headers')
```

When you run this code, the `log_response_details` function will be executed for each request, printing its details to the console.

Hooks are dispatched via the `dispatch_hook` function. If a hook function returns a value, that value will replace the data that was passed to it. This allows you to modify the `Response` object before it is returned from the `request()` call.

By combining custom adapters and hooks, you can extend Requests to fit nearly any networking requirement, from simple retry logic to complex, custom transport protocols.