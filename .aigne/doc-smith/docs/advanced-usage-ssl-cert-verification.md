# SSL Certificate Verification

When you make requests to HTTPS URLs, `requests` plays a crucial role in ensuring your communication is secure. A key part of this is verifying the server's SSL/TLS certificate. This process confirms that you are communicating with the server you think you are, protecting you from man-in-the-middle attacks.

This guide covers how `requests` handles SSL verification, how to customize this behavior with your own certificates, and how to use client-side certificates for mutual authentication.

## Default Verification Behavior

By default, `requests` verifies SSL certificates for all HTTPS requests. To do this, it uses a bundle of trusted Certificate Authorities (CAs) provided by the `certifi` package. This is the same set of CAs that major web browsers trust.

When you make a request, the `verify` parameter is implicitly set to `True`.

```python SSL Verification is on by default icon=logos:python
import requests

try:
    response = requests.get('https://example.com')
    print('Request was successful!')
except requests.exceptions.SSLError as e:
    print(f'An SSL error occurred: {e}')
```

If the server's certificate is valid and signed by a trusted CA, the request proceeds. If not, `requests` will raise an `SSLError`.

## Disabling SSL Verification

In some cases, like during local development or when dealing with a server using a self-signed certificate, you might need to disable verification. You can do this by setting the `verify` parameter to `False`.

<x-card data-title="Security Warning" data-icon="lucide:shield-alert">
Disabling SSL certificate verification will make your application vulnerable to man-in-the-middle (MitM) attacks. Any data exchanged, including sensitive credentials, can be intercepted and tampered with. Only disable verification in controlled testing environments and never in production.
</x-card>

```python Disabling SSL Verification icon=logos:python
import requests
from urllib3.exceptions import InsecureRequestWarning

# Suppress only the single warning from urllib3 about insecure requests
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

response = requests.get('https://self-signed.badssl.com/', verify=False)
print(response.status_code)
```

When `verify=False` is used, `requests` will issue a warning. It's recommended to suppress this warning only if you are fully aware of the security implications.

## Custom CA Bundles

Instead of completely disabling verification, a more secure approach for servers with private or custom certificates is to tell `requests` to trust a specific CA bundle. You can do this by passing the path to a CA bundle file (`.pem`) to the `verify` parameter.

<x-field data-name="verify" data-type="boolean | string" data-default="True" data-desc="Controls SSL/TLS certificate verification. If `True`, uses the default CA bundle. If `False`, disables verification. If a string, it must be a path to a CA bundle file or a directory of CA certificates."></x-field>

```python Using a Custom CA Bundle file icon=logos:python
import requests

try:
    response = requests.get('https://example.com', verify='/path/to/your/ca.pem')
    print('Request successful with custom CA!')
except requests.exceptions.SSLError as e:
    print(f'SSL verification failed: {e}')
```

If the `verify` path points to a directory, `requests` will use that directory of CA certificates.

Additionally, `requests` respects the `REQUESTS_CA_BUNDLE` and `CURL_CA_BUNDLE` environment variables. If set, `requests` will use the specified CA bundle by default for all requests where `verify` is `True`.

## Client-Side Certificates

Some servers require clients to present their own certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

<x-field data-name="cert" data-type="string | tuple" data-desc="Path to a client-side SSL certificate. Can be a single file (containing the private key and certificate) or a tuple of ('/path/to/cert.pem', '/path/to/key.pem')."></x-field>

### Certificate and Key in a Single File

If your certificate and private key are in the same `.pem` file, you can pass the path as a single string.

```python Single File Client Certificate icon=logos:python
import requests

response = requests.get('https://api.example.com/data', cert='/path/to/client.pem')
```

### Certificate and Key in Separate Files

If your certificate and private key are in separate files, pass a tuple containing the path to the certificate file followed by the path to the key file.

```python Separate Files Client Certificate icon=logos:python
import requests

cert_path = '/path/to/client.crt'
key_path = '/path/to/client.key'

response = requests.get('https://api.example.com/data', cert=(cert_path, key_path))
```

By correctly configuring SSL verification and client certificates, you can ensure that your application's communications are secure and properly authenticated.

---

Now that you have a handle on SSL verification, you may want to explore other advanced networking features. For more information, see the [Timeouts, Retries, and Proxies](./advanced-usage-timeouts-retries-proxies.md) guide.