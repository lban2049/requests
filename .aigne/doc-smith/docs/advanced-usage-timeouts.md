# Timeouts

Most requests to external servers should have a timeout attached, in case the server is not responding in a timely manner. Without a timeout, your code might hang for minutes or more. Setting a timeout ensures that your application remains responsive and can handle network issues gracefully.

## Basic Timeout

You can tell Requests to stop waiting for a response after a given number of seconds with the `timeout` parameter. This single value will be applied to both the `connect` and the `read` timeouts.

```python Timeout Example icon=logos:python
import requests

try:
    # Wait for a maximum of 2.5 seconds for the server to respond
    response = requests.get('https://httpbin.org/delay/5', timeout=2.5)
    print("Request successful!")
except requests.exceptions.Timeout:
    print("The request timed out.")

```

In this example, the request will time out because the server is configured to wait 5 seconds before responding, but our timeout is set to 2.5 seconds. When a timeout occurs, Requests raises a `requests.exceptions.Timeout` exception.

## Fine-Grained Timeouts

For more granular control, you can specify the `connect` and `read` timeouts separately by passing a tuple to the `timeout` parameter.

- **Connect Timeout**: The time allowed for the client to establish a connection to the server.
- **Read Timeout**: The time allowed for the client to wait for the server to send a response once the connection has been established.

```python Connect and Read Timeouts icon=logos:python
import requests
from requests.exceptions import ConnectTimeout, ReadTimeout

try:
    # 3.05 seconds to connect, 5 seconds to wait for a response
    response = requests.get('https://httpbin.org/delay/10', timeout=(3.05, 5))
    print("Request successful!")
except ConnectTimeout:
    print("Connection timed out. The server did not respond to the connection request in time.")
except ReadTimeout:
    print("Read timed out. The server did not send any data in the allotted time.")

```

If the remote server is very slow to start sending data, you can set a long `read` timeout while keeping the `connect` timeout low to quickly detect if the server is down.

## Wait Forever

If you want to wait indefinitely for a response, you can set `timeout` to `None`. This is the default behavior, but it is strongly discouraged in production environments as it can lead to your application hanging.

```python No Timeout (Wait Forever) icon=logos:python
# This request will hang until the server responds, which could be never.
# Use with caution.
response = requests.get('https://httpbin.org/delay/10', timeout=None)
```

---

Properly configuring timeouts is a critical step in building resilient applications. To learn more about handling these and other potential issues, continue to the next section on [Error Handling](./advanced-usage-error-handling.md).