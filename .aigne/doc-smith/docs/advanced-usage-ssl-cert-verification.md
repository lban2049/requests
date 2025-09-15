# SSL Certificate Verification

When you make a request to an `https` URL, Requests plays a crucial role in ensuring your communication is secure. It does this by verifying the server's SSL/TLS certificate, which helps prevent man-in-the-middle attacks. This guide covers how Requests handles SSL verification and how you can customize its behavior for different scenarios.

## Default Verification Behavior

By default, Requests verifies SSL certificates for all HTTPS requests. To do this, it needs a set of trusted Certificate Authorities (CAs). Requests uses the CA bundle provided by the `certifi` package, which is a carefully curated collection of root certificates from trusted CAs.

When you make a simple HTTPS request, this verification happens automatically:

```python Sending a Verified HTTPS Request icon=logos:python
import requests

try:
    response = requests.get('https://api.github.com')
    print('Request was successful!')
except requests.exceptions.SSLError as e:
    print(f'An SSL error occurred: {e}')
```

If the server's certificate is valid and signed by a trusted CA, the request will succeed. If not, Requests will raise an `SSLError`.

## Disabling Certificate Verification

In certain situations, like when you're working with a local development server or an internal service that uses a self-signed certificate, you might need to bypass SSL verification. You can do this by setting the `verify` parameter to `False`.

<x-card data-title="Security Warning" data-icon="lucide:shield-alert">
Disabling SSL verification will make your application vulnerable to man-in-the-middle (MitM) attacks. It means your connection is not secure, and any data exchanged could be intercepted and tampered with. Only use `verify=False` for testing on trusted networks.
</x-card>

```python Disabling Verification icon=logos:python
import requests
from requests.packages.urllib3.exceptions import InsecureRequestWarning

# Optional: Suppress the insecure request warning
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

try:
    response = requests.get('https://self-signed.badssl.com/', verify=False)
    print('Request completed without SSL verification.')
    print(f'Status Code: {response.status_code}')
except Exception as e:
    print(f'An error occurred: {e}')
```

When you set `verify=False`, Requests will issue a warning for each insecure request. It's common practice to suppress these warnings using `requests.packages.urllib3.disable_warnings()` once you've acknowledged the risks.

## Using a Custom CA Bundle

Instead of disabling verification, a more secure approach for internal or private systems is to tell Requests to trust a specific CA bundle. You can do this by passing the path to your CA bundle file (`.pem` format) to the `verify` parameter.

This is useful if you're connecting to a server that uses a certificate issued by your organization's internal CA.

```python Specifying a Local CA Bundle icon=logos:python
import requests

ca_bundle_path = '/path/to/your/corporate-ca.pem'

try:
    response = requests.get('https://internal.mycompany.com', verify=ca_bundle_path)
    print('Successfully verified the server certificate using a custom CA.')
except requests.exceptions.SSLError as e:
    print(f'SSL verification failed: {e}')

```

If your CA bundle is structured as a directory of individual certificate files, you can also provide the path to the directory.

For persistent configuration, Requests also respects the `REQUESTS_CA_BUNDLE` and `CURL_CA_BUNDLE` environment variables.

```bash Configuring via Environment Variable icon=mdi:bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## Client-Side Certificates

Some servers require clients to present their own certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

You can provide the certificate and the private key in a single file:

```python Client Cert and Key in One File icon=logos:python
import requests

cert_file_path = '/path/to/your/client.pem'

response = requests.get(
    'https://api.service.com/secure-data',
    cert=cert_file_path
)
```

Alternatively, if your certificate and private key are in separate files, you can pass a tuple of paths:

```python Client Cert and Key in Separate Files icon=logos:python
import requests

cert_path = '/path/to/your/client.crt'
key_path = '/path/to/your/client.key'

response = requests.get(
    'https://api.service.com/secure-data',
    cert=(cert_path, key_path)
)
```

If the private key is encrypted, you will be prompted for the password at runtime.

By managing SSL verification correctly, you can ensure your application communicates securely while retaining the flexibility to connect to a wide range of HTTPS services.

---

Now that you understand how to manage SSL certificates, you might want to learn how to persist these settings across multiple requests. Check out the [Session Objects](./user-guide-session-objects.md) guide to learn more.