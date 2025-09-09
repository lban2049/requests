# SSL Certificate Verification

Requests verifies SSL certificates for HTTPS requests by default, just like a web browser. This is a critical security feature that ensures you are connecting to the correct server and that your data is encrypted during transit. By default, Requests uses the CA bundle provided by the `certifi` package.

This guide covers how to manage SSL/TLS verification for different scenarios, from using custom CAs to providing client-side certificates.

## Default Verification

By default, Requests will perform SSL verification for all HTTPS requests. If the server's certificate cannot be verified, a `requests.exceptions.SSLError` will be raised.

```python Request with Default Verification
import requests

try:
    response = requests.get('https://httpbin.org/get')
    print('Successfully connected with SSL verification.')
except requests.exceptions.SSLError as e:
    print(f'SSL Error: {e}')
```

In the code above, the `verify` parameter is implicitly `True`.

## Custom CA Bundle

In enterprise environments or when interacting with services that use a private Certificate Authority (CA), you may need to use a custom CA bundle. You can specify the path to a CA bundle file (`.pem`) for the `verify` parameter.

```python Using a Custom CA Bundle
import requests

ca_bundle_path = '/path/to/your/ca.pem'

try:
    response = requests.get('https://your-internal-service.com', verify=ca_bundle_path)
    print('Successfully connected using a custom CA bundle.')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')
```

Alternatively, Requests can be configured to use a custom CA bundle via environment variables by setting `REQUESTS_CA_BUNDLE` or `CURL_CA_BUNDLE` to the path of the certificate file.

## Client-Side Certificates

Some servers require clients to present their own certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

If your certificate and private key are in the same file, you can pass the file path as a string:

```python Client Certificate in a Single File
import requests

cert_file_path = '/path/to/your/client.pem'

response = requests.get(
    'https://api.secure-service.com/data',
    cert=cert_file_path
)

print(response.status_code)
```

If your certificate and private key are in separate files, pass them as a tuple:

```python Client Certificate and Key as a Tuple
import requests

cert_and_key = ('/path/to/your/client.crt', '/path/to/your/client.key')

response = requests.get(
    'https://api.secure-service.com/data',
    cert=cert_and_key
)

print(response.status_code)
```

## Disabling SSL Verification

While highly discouraged for production use, you may need to disable SSL verification for local development or when testing against a server with a self-signed certificate. To do this, set the `verify` parameter to `False`.

**Warning:** Disabling SSL verification exposes your application to man-in-the-middle (MitM) attacks. Only use this option in controlled, trusted environments.

```python Disabling SSL Verification (Insecure)
import requests

# Note: This will likely produce an InsecureRequestWarning
response = requests.get('https://self-signed.badssl.com/', verify=False)

print(f'Connected with status code: {response.status_code}')
```

## Persisting Verification Settings with Sessions

For applications making multiple requests to the same host, it is more efficient to use a `Session` object. You can configure the SSL verification settings on the session, and they will be applied to all subsequent requests made with that session.

```python Configuring SSL on a Session Object
import requests

s = requests.Session()

# Set the CA bundle for all requests in this session
s.verify = '/path/to/your/ca.pem'

# Set the client certificate for all requests in this session
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# This request will use the configured SSL settings
response = s.get('https://api.your-internal-service.com/status')

print(response.json())
```

This approach avoids redundant setup for each request and leverages connection pooling for better performance.

---

Now that you understand how to manage SSL certificate verification, you can explore how to further extend Requests' functionality by creating [Custom Adapters and Hooks](./advanced-usage-adapters-and-hooks.md).