# SSL Certificate Verification

Requests verifies SSL certificates for HTTPS requests by default, which is a critical security feature to ensure you are connecting to the server you intend to. This verification relies on a set of trusted Certificate Authorities (CAs) to validate the server's certificate.

This section explains how this verification works and how you can manage it for specific scenarios, such as using custom CAs, providing client-side certificates for authentication, or disabling verification for trusted environments.

## Default Verification

By default, Requests uses the CA bundle provided by the `certifi` package. When you make an HTTPS request, this behavior is enabled automatically.

```python
import requests

# This request will verify the server's SSL certificate against certifi's CA bundle.
response = requests.get('https://httpbin.org/get')
```

If verification fails, Requests will raise an `SSLError`.

## Custom CA Bundle

Instead of using the default CA bundle, you can specify your own by passing the path to a CA bundle file or a directory of CA certificates to the `verify` parameter.

```python
import requests

# Using a custom CA bundle file
ca_bundle_path = '/path/to/your/ca.pem'
response = requests.get('https://httpbin.org/get', verify=ca_bundle_path)

# Using a directory of CA certificates
ca_cert_dir_path = '/path/to/your/certs/'
response = requests.get('https://httpbin.org/get', verify=ca_cert_dir_path)
```

### Using Environment Variables

Requests will also respect the `REQUESTS_CA_BUNDLE` and `CURL_CA_BUNDLE` environment variables. If either of these is set to a valid path, Requests will use it as the default CA bundle for all requests, overriding the `certifi` bundle.

```bash
export REQUESTS_CA_BUNDLE=/path/to/your/ca.pem
```

## Disabling SSL Verification

In certain situations, like local development or testing against a server with a self-signed certificate, you might need to disable SSL verification. You can do this by setting `verify=False`.

> **Warning:** Disabling SSL certificate verification will make your application vulnerable to man-in-the-middle (MitM) attacks. Without verification, there is no guarantee that you are communicating with the intended server. This should only be done in controlled, non-production environments.

```python
import requests

# This will disable SSL certificate verification and suppress any warnings.
response = requests.get('https://localhost:5000/get', verify=False)
```

## Client-Side Certificates

For mutual TLS (mTLS) authentication, you may need to provide a client-side certificate. You can do this using the `cert` parameter. The value can be a path to a single file containing both the certificate and the private key, or a tuple containing the paths to the certificate file and the key file separately.

**Single File (Certificate and Key)**

```python
import requests

cert_file_path = '/path/to/client.pem'
response = requests.get('https://api.example.com', cert=cert_file_path)
```

**Separate Files (Certificate and Key)**

```python
import requests

cert_file_path = '/path/to/client.cert'
key_file_path = '/path/to/client.key'
response = requests.get('https://api.example.com', cert=(cert_file_path, key_file_path))
```

## Persisting Verification Settings with a Session

If you need to make multiple requests to the same host with the same verification settings, it is more efficient to use a `Session` object. You can configure the `verify` and `cert` properties on the session, and these settings will apply to all subsequent requests made with that session.

```python
import requests

s = requests.Session()

# Set a custom CA bundle for the session
s.verify = '/path/to/ca.pem'

# Set a client-side certificate for the session
s.cert = ('/path/to/client.cert', '/path/to/client.key')

# Both settings will be used for this request
response = s.get('https://api.example.com/data')
```

By managing SSL settings effectively, you can ensure your application communicates securely while accommodating various network environments and authentication requirements.

---

For even more advanced network control, such as defining custom connection logic or handling specific protocols, proceed to the next section on [Custom Adapters and Hooks](./advanced-usage-adapters-and-hooks.md).