# SSL Certificate Verification

Requests validates SSL certificates for HTTPS requests by default to ensure secure communication, much like a web browser. If a certificate cannot be verified, Requests will raise an `SSLError`. This is a critical security feature that protects your application from man-in-the-middle attacks.

By default, Requests uses a set of trusted root certificates from the `certifi` package. This section details how you can customize this behavior, such as by using a private Certificate Authority (CA) or providing a client-side certificate for authentication.

## Custom CA Certificates

You can override the default trusted CA bundle by passing the path to your own CA bundle file or a directory of certificates to the `verify` parameter.

This is particularly useful when interacting with internal services that use self-signed or privately issued certificates.

```python Using a custom CA bundle file icon=logos:python
import requests

response = requests.get('https://some-internal-site.com', verify='/path/to/your/ca.pem')
```

If you have a directory of certificates, you can pass the path to that directory instead:

```python Using a directory of CA certificates icon=logos:python
import requests

response = requests.get('https://some-internal-site.com', verify='/path/to/certs/')
```

### Using Environment Variables

For a more persistent configuration, you can set the `REQUESTS_CA_BUNDLE` or `CURL_CA_BUNDLE` environment variables. Requests will automatically use the specified CA bundle for all requests, so you don't need to pass the `verify` parameter in your code.

```bash Setting the environment variable icon=mdi:bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## Client-Side Certificates

Some servers require clients to present a certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

The `cert` parameter can be a path to a single file containing both the private key and the certificate, or a tuple containing the paths to the certificate file and the key file respectively.

```python Certificate and key in one file icon=logos:python
import requests

# If your private key is included in the certificate file
cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.some-secure-service.com', cert=cert_file_path)
```

```python Certificate and key in separate files icon=logos:python
import requests

# If your certificate and private key are in separate files
cert_file_path = '/path/to/client.crt'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.some-secure-service.com', cert=(cert_file_path, key_file_path))
```

If the specified certificate or key files do not exist at the given path, Requests will raise an `OSError`.

## Disable Verification

In certain scenarios, like local development or testing against a server with a temporary self-signed certificate, you might need to disable SSL verification. You can achieve this by setting `verify=False`.

> **Warning:** Disabling SSL certificate verification exposes your application to severe security risks, including man-in-the-middle (MitM) attacks. It bypasses the validation of the server's identity, meaning any data you send could be intercepted. This should only be used in controlled, non-production environments.

```python Disabling SSL verification icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# Suppress only the single warning from urllib3 about insecure requests.
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

# This will disable certificate verification.
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

When `verify=False`, Requests will accept any TLS certificate presented by the server and will ignore hostname mismatches or expired certificates.

## SSL Verification in Sessions

If you need to apply the same SSL configuration across multiple requests, you can set the `verify` and `cert` properties on a `Session` object. This approach avoids passing the same parameters to every request call and can improve performance through connection reuse.

```python Configuring a Session with SSL settings icon=logos:python
import requests

s = requests.Session()

# Set a custom CA bundle for all requests in this session
s.verify = '/path/to/ca.pem'

# Set a client-side certificate for all requests in this session
s.cert = ('/path/to/client.crt', '/path/to/client.key')

# These requests will use the session's SSL configuration
response1 = s.get('https://api.example.com/endpoint1')
response2 = s.get('https://api.example.com/endpoint2')
```

Any parameters passed directly to a request method (e.g., `s.get(url, verify=False)`) will override the session's settings for that specific request.

---

For more advanced customization of network behavior, such as creating custom connection logic or handling specific authentication schemes, proceed to the next section.

<x-card data-title="Custom Adapters and Hooks" data-icon="lucide:git-merge" data-href="/advanced-usage/adapters-and-hooks" data-cta="Read More">
  Learn how to extend the functionality of Requests by creating custom Transport Adapters and using the event hook system.
</x-card>
