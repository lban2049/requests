# SSL Certificate Verification

Requests validates SSL certificates for HTTPS requests by default to ensure secure communication, similar to how a web browser operates. If the certificate cannot be verified, Requests will raise an `SSLError`. This behavior is a critical security feature that protects against man-in-the-middle attacks.

By default, Requests uses the CA bundle provided by the `certifi` package. This section details how to customize this behavior for different scenarios, such as using a private CA or providing a client-side certificate.

## Custom CA Certificates

You can override the default trusted CA bundle by passing the path to your own CA bundle file or a directory of certificates to the `verify` parameter.

This is particularly useful when interacting with internal servers or services that use self-signed certificates.

```python
import requests

# Using a custom CA bundle file
response = requests.get('https://some-internal-site.com', verify='/path/to/your/ca.pem')

# Using a directory of CA certificates
response = requests.get('https://some-internal-site.com', verify='/path/to/certs/')
```

If `verify` is set to a path to a directory, Requests will load the certificates from that directory.

### Using Environment Variables

Alternatively, you can configure a custom CA bundle for all requests by setting the `REQUESTS_CA_BUNDLE` or `CURL_CA_BUNDLE` environment variables:

```bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

When this environment variable is set, Requests will use it as the default CA bundle, so you don't need to specify the `verify` parameter in your code.

## Client-Side Certificates

Some servers require clients to present a certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

The `cert` parameter can be a path to a single file containing both the private key and the certificate, or a tuple containing the paths to the certificate file and the key file.

```python
import requests

# If your private key is included in the certificate file
cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.some-secure-service.com', cert=cert_file_path)

# If your certificate and private key are in separate files
cert_file_path = '/path/to/client.crt'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.some-secure-service.com', cert=(cert_file_path, key_file_path))
```

If the specified certificate or key files do not exist, Requests will raise an `OSError`.

## Disable Verification

In certain situations, such as local development or testing against a server with a temporary self-signed certificate, you may need to disable SSL verification. You can do this by setting `verify=False`.

> **Warning:** Disabling SSL certificate verification will make your application vulnerable to man-in-the-middle (MitM) attacks. It bypasses the validation of the server's identity, meaning any data you send could be intercepted. This should only be used in controlled, non-production environments.

```python
import requests

# This will disable certificate verification and may result in a security warning.
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

When `verify=False`, Requests will accept any TLS certificate presented by the server and will ignore hostname mismatches and expired certificates.

## SSL Verification in Sessions

If you need to apply the same SSL configuration across multiple requests, you can set the `verify` and `cert` properties on a `Session` object. This avoids passing the same parameters to every request call.

```python
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
