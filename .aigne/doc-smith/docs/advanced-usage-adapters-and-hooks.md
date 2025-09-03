# Custom Adapters and Hooks

Requests is designed to be extensible, allowing you to modify or replace core components to suit specific needs. The two primary mechanisms for this are Transport Adapters, which handle the logic of sending requests, and Event Hooks, which allow you to intercept and process responses.

This guide will demonstrate how to create and use both to extend the functionality of your HTTP requests.

## Transport Adapters

A Transport Adapter provides a low-level interface for handling requests for a specific URL scheme, like `http://` or `https://`. When you make a request, a `Session` object selects the appropriate adapter based on the request's URL prefix and delegates the actual network communication to it.

By creating a custom adapter, you can implement unique transport behaviors, such as custom authentication, specialized logging, or non-standard retry logic.

### Creating a Custom Adapter

The simplest way to create a custom adapter is to subclass `requests.adapters.HTTPAdapter` and override its methods. The most common method to override is `send()`, which is responsible for sending a `PreparedRequest`.

Here is an example of a simple adapter that logs the time taken for each request and its status code.

```python
import requests
import time
from requests.adapters import HTTPAdapter

class TimingAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        start = time.time()
        print(f'Starting request to {request.url}')
        
        # Call the parent class's send method to perform the actual request
        response = super().send(request, stream, timeout, verify, cert, proxies)
        
        end = time.time()
        total_time = round(end - start, 2)
        print(f'Request to {request.url} finished in {total_time}s with status {response.status_code}')
        
        return response
```

### Mounting an Adapter

Once you have a custom adapter, you need to instruct a `Session` object to use it for certain requests. This is done using the `mount()` method, which associates a URL prefix with your adapter.

```python
# Create a session object
session = requests.Session()

# Create an instance of our custom adapter
timing_adapter = TimingAdapter()

# Mount the adapter to handle all HTTPS requests
session.mount('https://', timing_adapter)

# Any request made with this session to an https:// URL will use our adapter
try:
    session.get('https://httpbin.org/get')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')

# Expected output:
# Starting request to https://httpbin.org/get
# Request to https://httpbin.org/get finished in Xs with status 200
```

Requests will use the most specific prefix when selecting an adapter. For example, an adapter mounted on `'https://api.example.com'` would be chosen for requests to that host over one mounted on `'https://'`.

### Request Lifecycle with Adapters

The following diagram illustrates where Transport Adapters fit into the request lifecycle.

```d2
direction: down

"Request Initiated": {
  shape: oval
}

"Session Object": {
  shape: rectangle
  "1. get_adapter(url)": {
    shape: rectangle
  }
}

"Transport Adapter": {
  shape: package
  "2. send(request)": {
    shape: rectangle
  }
  "4. build_response(raw_resp)": {
    shape: rectangle
  }
}

"Network Communication": {
  shape: cylinder
  label: "HTTP/HTTPS"
}

"Response Processing": {
  shape: rectangle
  "5. dispatch_hook('response', ...)"
}

"Final Response": {
  shape: oval
}

"Request Initiated" -> "Session Object"
"Session Object" -> "Transport Adapter": "Selects appropriate adapter"
"Transport Adapter" -> "Network Communication": "3. Sends request"
"Network Communication" -> "Transport Adapter": "Receives raw response"
"Transport Adapter" -> "Response Processing": "Returns requests.Response object"
"Response Processing" -> "Final Response": "Returns to user"
```

## Event Hooks

Requests also provides a hook system for developers to attach callbacks to certain parts of the request process. The primary available hook is `response`, which is triggered after a response has been received but before it is returned to the caller.

Hooks are useful for inspecting or modifying response objects globally without needing to wrap every request call.

### Using the `response` Hook

A hook is simply a function that accepts the `response` object as its first argument, along with any other keyword arguments passed to the original request method.

Here's an example of a hook that prints a response header:

```python
import requests

def print_server_header(response, **kwargs):
    """This hook prints the value of the Server header."""
    if 'Server' in response.headers:
        print(f"Response was served by: {response.headers['Server']}")
    return response

# Attach the hook to a single request
requests.get('https://httpbin.org/get', hooks={'response': print_server_header})

# Expected output:
# Response was served by: gunicorn/19.9.0
```

You can also attach a hook to a `Session` object to have it run for every request made through that session.

```python
session = requests.Session()
# Note: session hooks should be a list of callables
session.hooks['response'] = [print_server_header]

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/ip')
```

### Modifying the Response

A hook can also modify the response object. If a hook function returns a value, that value will replace the original response object for any subsequent processing and for the final return to the user.

This example shows a hook that attaches a custom attribute to the response.

```python
import requests

def add_custom_attribute(response, **kwargs):
    response.hook_was_here = True
    return response

response = requests.get('https://httpbin.org/get', hooks={'response': add_custom_attribute})

if hasattr(response, 'hook_was_here') and response.hook_was_here:
    print("Custom attribute added by hook successfully.")

# Expected output:
# Custom attribute added by hook successfully.
```
