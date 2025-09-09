# Custom Adapters and Hooks

Requests is designed to be highly extensible, allowing you to modify its core behavior to fit specialized use cases. The two primary mechanisms for this are Transport Adapters and event hooks. Transport Adapters let you define how Requests makes HTTP and HTTPS calls, while hooks provide a way to trigger custom actions at specific points in the request-response cycle.

## Transport Adapters

Whenever a `Session` handles a request, it looks for a registered Transport Adapter for the URL's scheme (e.g., `http://` or `https://`). This adapter is responsible for the entire transport logic, from connection management to sending the request and returning a response.

By default, Requests uses the `HTTPAdapter` for both `http://` and `https://`. You can create your own adapter to implement custom transport behavior.

### Creating a Custom Adapter

A custom adapter should inherit from `requests.adapters.BaseAdapter` or, more commonly, `requests.adapters.HTTPAdapter` if you only want to modify the existing HTTP/HTTPS logic. The most important method to override is `send()`.

Here is a simple example of a custom adapter that adds basic logging to each request and response.

```python Transport Adapter Example icon=logos:python
import requests
from requests.adapters import HTTPAdapter

class LoggingAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        print(f'-> Sending request to {request.method} {request.url}')
        
        # Call the parent's send method to perform the actual request
        response = super().send(request, stream, timeout, verify, cert, proxies)
        
        print(f'<- Received response {response.status_code} {response.reason}')
        return response
```

### Using a Custom Adapter

To use a custom adapter, you must mount it to a `Session` object for a specific URL prefix. The session will then use your adapter for any request whose URL starts with that prefix.

```python Mounting a Custom Adapter icon=logos:python
# Create a session and an instance of our custom adapter
session = requests.Session()
adapter = LoggingAdapter()

# Mount the adapter to handle all HTTPS traffic
session.mount('https://', adapter)

# All requests to https://... will now go through our LoggingAdapter
try:
    session.get('https://httpbin.org/get')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')

# Output:
# -> Sending request to GET https://httpbin.org/get
# <- Received response 200 OK
```

By subclassing `HTTPAdapter`, you can also override other methods to gain fine-grained control over aspects like connection pooling (`init_poolmanager`), proxy management (`proxy_manager_for`), and response building (`build_response`).

## Event Hooks

Requests provides a hook system that allows you to attach callable actions to different parts of the request lifecycle. When you register a hook, Requests will call your function with specific data, allowing you to inspect or modify it.

The primary available hook is `response`, which is triggered after a response is received from the server but before it is returned to the caller.

### How Hooks Work

A hook is a function that receives the object it's acting on as its first argument. For the `response` hook, this is the `Response` object. Your function can perform actions based on the response and can even modify it. If the hook function returns a value, it will replace the original object.

Here is an example of a hook that automatically checks for HTTP errors:

```python Hook Example icon=logos:python
import requests

def raise_for_status_hook(response, *args, **kwargs):
    """A hook function that calls raise_for_status() on every response."""
    print(f'Hook is checking response for URL: {response.url}')
    response.raise_for_status()
    # No return value is needed if we are not modifying the response

# Create a session and attach the hook
session = requests.Session()
session.hooks['response'] = [raise_for_status_hook]

# This request will succeed and the hook will run
print('--- Making a successful request ---')
response = session.get('https://httpbin.org/get')
print(f'Request successful with status code: {response.status_code}')

print('\n--- Making a failing request ---')
try:
    session.get('https://httpbin.org/status/404')
except requests.exceptions.HTTPError as e:
    print(f'Caught expected error via hook: {e}')

# Output:
# --- Making a successful request ---
# Hook is checking response for URL: https://httpbin.org/get
# Request successful with status code: 200
#
# --- Making a failing request ---
# Hook is checking response for URL: https://httpbin.org/status/404
# Caught expected error via hook: 404 Client Error: NOT FOUND for url: https://httpbin.org/status/404
```

You can also attach hooks on a per-request basis by passing a `hooks` dictionary to the request method:

```python Per-Request Hook icon=logos:python
requests.get('https://httpbin.org/status/500', hooks={'response': raise_for_status_hook})
```

By leveraging custom adapters and hooks, you can extend Requests to handle complex authentication schemes, custom logging requirements, and unique network transports, making it a powerful tool for any HTTP-related task.

For a detailed look at the classes and methods available for extension, consult the full [API Reference](./api-reference.md).