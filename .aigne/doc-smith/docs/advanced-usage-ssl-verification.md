# SSL Certificate Verification

Requests verifies SSL certificates for HTTPS requests by default, functioning much like a web browser. This is a critical security feature that helps prevent man-in-the-middle attacks. By default, Requests uses a set of trusted Certificate Authorities (CAs) from the `certifi` package, which provides Mozilla's carefully curated collection.

This section covers how to manage SSL verification, including disabling it (with caution), using custom CA bundles, and providing client-side certificates for mutual TLS authentication.

## Default Behavior

When you make a request to an `https://` URL, Requests will automatically perform certificate verification. You don't need to do anything special for this to happen.

```python Default Verification
import requests

try:
    response = requests.get('https://api.github.com')
    print('Request successful!')
except requests.exceptions.SSLError as e:
    print(f'SSL Error: {e}')
```

If the server's certificate is valid and trusted by one of the CAs in the `certifi` bundle, the request will succeed. If not, a `requests.exceptions.SSLError` will be raised.

## Disabling SSL Verification

In certain situations, such as when dealing with a development server using a self-signed certificate, you might need to disable verification. You can do this by setting the `verify` parameter to `False`.

<x-card data-title="Security Warning" data-icon="lucide:alert-triangle">
Disabling certificate verification is **not recommended** for production environments. It exposes your application to security vulnerabilities, including man-in-the-middle attacks, as it allows communication with servers whose identity cannot be confirmed. Proceed with caution and only when you fully understand the risks.
</x-card>

```python Disabling Verification icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# Suppress only the single warning from urllib3 about insecure requests
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

try:
    response = requests.get('https://self-signed.badssl.com/', verify=False)
    print('Request successful, but without SSL verification.')
    print(f'Status Code: {response.status_code}')
except requests.exceptions.RequestException as e:
    print(f'An error occurred: {e}')
```

When you set `verify=False`, Requests will still perform the TLS handshake but will not verify the server's certificate, ignoring any SSL errors.

## Custom CA Bundles

For corporate or private networks that use their own Certificate Authority, you can instruct Requests to trust a specific CA bundle by passing the path to the certificate file to the `verify` parameter.

The certificate file should be in PEM format and can contain multiple CA certificates.

```python Using a Custom CA Bundle icon=logos:python
import requests

ca_bundle_path = '/path/to/your/custom-ca.pem'

try:
    response = requests.get('https://internal.mycompany.com', verify=ca_bundle_path)
    print('Successfully verified with custom CA.')
except requests.exceptions.SSLError as e:
    print(f'Failed to verify with custom CA: {e}')
```

If you have a directory of CA certificates instead of a single file, you can also provide the path to the directory.

## Client-Side Certificates

Some services require clients to present their own certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

The `cert` parameter can be one of two things:
1.  A string containing the path to a single file that has both the client certificate and the private key.
2.  A tuple containing the paths to the certificate file and the key file, respectively.

### Certificate and Key in a Single File

```python Client Cert (Single File) icon=logos:python
import requests

cert_file_path = '/path/to/client.pem'

response = requests.get(
    'https://api.secure.service/resource',
    cert=cert_file_path
)
```

### Certificate and Key in Separate Files

```python Client Cert (Separate Files) icon=logos:python
import requests

cert_path = '/path/to/client.crt'
key_path = '/path/to/client.key'

response = requests.get(
    'https://api.secure.service/resource',
    cert=(cert_path, key_path)
)
```

Note that the private key must not be encrypted.

## Configuring Verification for a Session

If you need to apply the same verification settings across multiple requests, it's more efficient to configure a `Session` object. This is the recommended approach for any non-trivial application.

```python Session with Custom Verification icon=logos:python
import requests

# Configure a session with a custom CA and client certificate
s = requests.Session()
s.verify = '/path/to/custom-ca.pem'
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# All requests made with this session will use these settings
response1 = s.get('https://api.secure.service/resource1')
response2 = s.get('https://api.secure.service/resource2')
```

By setting `verify` and `cert` on the `Session` object, you avoid repeating the configuration for every call and benefit from connection pooling. For more details, see the documentation on [Session Objects](./advanced-usage-session-objects.md).

Up next, learn how to prevent requests from hanging indefinitely by configuring [Timeouts](./advanced-usage-timeouts.md).