# Custom Adapters and Hooks

Requests is designed to be extensible, allowing you to alter its core behavior for advanced scenarios. The two primary mechanisms for this are Transport Adapters, which control how requests are sent over the network, and Hooks, which let you intercept and modify parts of the request-response cycle.

This guide explores how to create and use your own custom adapters and hooks to tailor Requests to your specific needs.

## Transport Adapters

A Transport Adapter is a class that takes a `PreparedRequest` object and handles the logic for sending it to a server. It manages connection pooling, retry logic, and protocol-specific behavior. By default, Requests uses a single `HTTPAdapter` for all `http://` and `https://` requests.

By creating a custom adapter, you can implement unique transport behaviors, such as adding custom authentication headers, logging requests in a specific format, or even using a different transport protocol.

### Creating a Custom Adapter

The most direct way to create a custom adapter is to inherit from `requests.adapters.HTTPAdapter` and override its methods. The `send()` method is the core of the adapter, as it's responsible for the entire request-sending process.

Here is an example of a custom adapter that adds a `X-Request-ID` header to every request it sends.

```python Add Custom Header Adapter icon=logos:python
import requests
import uuid
from requests.adapters import HTTPAdapter

class RequestIdAdapter(HTTPAdapter):
    def send(self, request, stream=False, timeout=None, verify=True, cert=None, proxies=None):
        # Generate a unique ID for the request
        request_id = str(uuid.uuid4())
        request.headers['X-Request-ID'] = request_id

        print(f"Sending request with ID: {request_id}")
        
        # Call the parent class's send method to execute the request
        return super().send(request, stream, timeout, verify, cert, proxies)

```

### Mounting a Custom Adapter

Once you have defined your adapter, you must instruct a `Session` object to use it for specific URL prefixes. This is done using the `session.mount()` method.

```python Mount and Use Custom Adapter icon=logos:python
# Create a session object
s = requests.Session()

# Create an instance of our custom adapter
adapter = RequestIdAdapter()

# Mount the adapter to handle all HTTP and HTTPS requests
s.mount('http://', adapter)
s.mount('https://', adapter)

# Make a request using the session
try:
    response = s.get('https://httpbin.org/headers')
    response.raise_for_status()
    print("\nResponse Headers:")
    # The response will contain the X-Request-ID header
    print(response.json()['headers'])
except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")

```

When this code is executed, the output from httpbin.org will include the `X-Request-ID` header, confirming that your custom adapter was used for the request.

## Event Hooks

Requests also provides a hook system that allows you to register callback functions that are executed at specific points in the request lifecycle. This is useful for implementing event handling, custom logging, or modifying responses on the fly.

The primary available hook is `response`, which is triggered after a response is received from the server but before it is returned to your code.

### Using the `response` Hook

A hook is a function that receives the `response` object as its first argument. Any additional arguments passed to the original request method (e.g., `timeout`) are also passed to the hook as keyword arguments.

The hook function can inspect or modify the response. If it returns a value, that value will replace the original response. If it returns `None`, the original response is used.

#### Example: Logging Responses

Here is a simple hook that logs the status code and URL of every response.

```python Response Logging Hook icon=logos:python
import requests

def log_response(response, *args, **kwargs):
    print(f"Request to {response.url} completed with status {response.status_code}")
    # This hook does not modify the response, so it returns None implicitly.

# Attach the hook to a session
session = requests.Session()
session.hooks['response'] = log_response

session.get('https://httpbin.org/get')
session.get('https://httpbin.org/status/404')

```

#### Example: Centralized Error Checking

This example shows a hook that automatically calls `response.raise_for_status()`, centralizing HTTP error handling for all requests made with the session.

```python Automatic Error Checking Hook icon=logos:python
import requests

def check_for_error(response, *args, **kwargs):
    try:
        response.raise_for_status()
        print(f"Request to {response.url} was successful.")
    except requests.exceptions.HTTPError as e:
        # You could add more robust error handling here, like logging to a file
        print(f"HTTP Error for {response.url}: {e}")
    # No need to return anything, we are just performing an action.

session = requests.Session()
session.hooks['response'] = check_for_error

print("Making a request that will succeed...")
session.get('https://httpbin.org/status/200')

print("\nMaking a request that will fail...")
session.get('https://httpbin.org/status/500')

```

### Adapters and Hooks Interaction Flow

The following diagram illustrates the data flow when a request is made using a `Session` with both a custom adapter and a response hook registered.

```d2
direction: down

UserCode: {
  label: "User Code"
  shape: rectangle
}

Session: {
  label: "requests.Session"
  shape: class
}

Adapter: {
  label: "CustomAdapter"
  shape: class
}

Network: {
  label: "Network / Server"
  shape: cylinder
}

HookFunction: {
  label: "Response Hook Function"
  shape: rectangle
}

UserCode -> Session: "1. session.get(url)"
Session -> Adapter: "2. Selects & calls adapter.send(request)"
Adapter -> Network: "3. Sends HTTP Request"
Network -> Adapter: "4. Receives HTTP Response"
Adapter -> Session: "5. Returns requests.Response object"
Session -> HookFunction: "6. Dispatches 'response' hook"
HookFunction -> Session: "7. Hook executes and may return modified response"
Session -> UserCode: "8. Returns final Response"

```

By combining custom adapters and hooks, you can build sophisticated and resilient HTTP clients that perfectly match the requirements of your application.

---

For more detailed information on the classes and methods discussed, you can explore the [API Reference](./api-reference.md).
