# SSL Certificate Verification

Requests verifies SSL certificates for HTTPS requests by default, a crucial security feature that prevents man-in-the-middle attacks. This verification relies on a system of trusted Certificate Authorities (CAs), much like a web browser. This section explains how to manage SSL/TLS verification, from using custom CAs to providing client-side certificates for authentication.

## Default CA Verification

By default, when you make a request to an `https://` URL, Requests validates the server's certificate against a bundle of trusted CAs.

```python
import requests

# This request succeeds because httpbin.org has a valid certificate 
# trusted by the default CA bundle.
response = requests.get('https://httpbin.org/get')
print(response.status_code)
# 200
```

Requests uses the `certifi` package to provide this default set of trusted root certificates. You can locate the CA bundle file it uses:

```python
import certifi

print(certifi.where())
# /path/to/your/virtualenv/lib/pythonX.X/site-packages/certifi/cacert.pem
```

## Custom CA Bundle

In enterprise or development environments, you may need to connect to services that use private or self-signed certificates. You can instruct Requests to trust a specific set of CAs by passing the path to a CA bundle file or a directory of certificates to the `verify` parameter.

```python
# Using a single CA bundle file (.pem)
requests.get('https://internal.service.com', verify='/path/to/ca.pem')

# Using a directory containing multiple CA certificates
requests.get('https://internal.service.com', verify='/path/to/certs/')
```

To persist this setting across multiple requests, you can configure it on a `Session` object:

```python
import requests

s = requests.Session()
s.verify = '/path/to/ca.pem'

# All requests made with this session will use the custom CA bundle
response = s.get('https://another.internal.service.com')
```

Alternatively, Requests will automatically use the CA bundle specified in the `REQUESTS_CA_BUNDLE` or `CURL_CA_BUNDLE` environment variables if `verify` is not set in your code.

## Client-Side Certificates

Some services require clients to present their own certificate for authentication, a process known as mutual TLS (mTLS). You can provide a client-side certificate using the `cert` parameter.

If your private key and certificate are in the same file:

```python
requests.get(
    'https://api.service.com/resource',
    cert='/path/to/client.pem'
)
```

If the key and certificate are in separate files, pass them as a tuple:

```python
requests.get(
    'https://api.service.com/resource',
    cert=('/path/to/client.crt', '/path/to/client.key')
)
```

As with the `verify` setting, you can set `cert` on a `Session` object to apply it to all requests made within that session.

## Disabling Verification

For local testing or on a completely trusted network, you may need to disable SSL certificate verification. You can do this by setting `verify=False`. 

**Warning**: This is highly insecure and should never be done in production. Disabling verification exposes your application to man-in-the-middle attacks.

```python
import requests

# This will disable certificate verification and may trigger an InsecureRequestWarning.
response = requests.get('https://self-signed.badssl.com/', verify=False)
```

## Verification Workflow

The following diagram illustrates how Requests determines which verification method to use for an HTTPS request.

```d2
direction: down

start: "Start Request"
is_https: "URL is HTTPS?" {
  shape: diamond
}
no_tls: "Proceed without TLS"
verify_false: "verify=False?" {
    shape: diamond
}
disable_verify: "Disable verification (insecure)" {
    style.fill: "#fce7c6"
}
is_path: "verify is a path?" {
    shape: diamond
}
custom_ca: "Use custom CA bundle"
default_ca: "Use default CA bundle (certifi)"
make_request: "Make Request"
end: "End"

start -> is_https
is_https -- "No" -> no_tls
is_https -- "Yes" -> verify_false

verify_false -- "Yes" -> disable_verify
verify_false -- "No" -> is_path

is_path -- "Yes" -> custom_ca
is_path -- "No" -> default_ca

disable_verify -> make_request
custom_ca -> make_request
default_ca -> make_request
no_tls -> end
make_request -> end
```

You are now equipped to handle SSL/TLS certificate verification in various scenarios. For deeper control over network behavior, see the next section on [Custom Adapters and Hooks](./advanced-usage-adapters-and-hooks.md).
