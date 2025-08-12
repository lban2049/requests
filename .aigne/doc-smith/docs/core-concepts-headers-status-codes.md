# Headers & Status Codes

HTTP communication relies heavily on headers and status codes to convey crucial information about requests and responses. Headers act as metadata, providing details about the message body, the sender, or the intended recipient. Status codes are numerical indicators that signal the outcome of a server's attempt to fulfill a client's request. Requests simplifies the handling of both, especially by providing a flexible way to manage HTTP headers through a case-insensitive dictionary structure.

To understand how headers and status codes fit into the overall request-response cycle, you can refer to the [Requests & Responses](./core-concepts-requests-responses.md) section.

## HTTP Headers: Case-Insensitive Handling

Requests uses a specialized dictionary-like object called `CaseInsensitiveDict` for managing HTTP headers. This structure ensures that header lookups are case-insensitive, aligning with HTTP specification best practices, while still preserving the original casing of the header names when they are stored or iterated over. This means you can access a header like `Content-Type` using `headers['content-type']` or `headers['Content-Type']`, and get the same result.

Here's how `CaseInsensitiveDict` simplifies header manipulation:

```python
from requests.structures import CaseInsensitiveDict

# Create an instance of CaseInsensitiveDict
headers = CaseInsensitiveDict()

# Set headers
headers['Content-Type'] = 'application/json'
headers['Accept-Encoding'] = 'gzip, deflate'

# Access headers case-insensitively
print(headers['content-type']) # Output: application/json
print(headers['accept-encoding']) # Output: gzip, deflate

# Original casing is preserved for iteration
for key, value in headers.items():
    print(f"{key}: {value}")
# Output:
# Content-Type: application/json
# Accept-Encoding: gzip, deflate

# In Requests, default headers are also handled this way
import requests

response = requests.get('https://httpbin.org/headers')

# Accessing response headers (which are also a CaseInsensitiveDict)
print(response.headers['content-type'])
print(response.headers.get('Server'))
```

Requests also provides utility functions for more advanced header manipulation, such as `parse_header_links` for parsing `Link` headers or `parse_dict_header` for extracting key-value pairs from complex header strings. These utilities are typically for internal use or highly specific custom scenarios.

## HTTP Status Codes

Requests provides a convenient `codes` object within its top-level module to easily reference common HTTP status codes by their descriptive names. This `codes` object is an instance of `LookupDict`, which allows you to access status codes using attribute access (e.g., `requests.codes.ok`) or dictionary-style lookups (e.g., `requests.codes['not_found']`). Many codes have multiple aliases, and both upper- and lower-case versions of the names are recognized.

Here are some examples of how to use the `requests.codes` object:

```python
import requests

# Access a status code by name
print(requests.codes.ok) # Output: 200
print(requests.codes['not_found']) # Output: 404

# Using aliases
print(requests.codes.all_ok) # Output: 200
print(requests.codes.teapot) # Output: 418
print(requests.codes['temporary_redirect']) # Output: 307
print(requests.codes['\o/']) # Output: 200 (for success)
print(requests.codes['/o\']) # Output: 500 (for server error)
```

Below is a table of common HTTP status codes and their associated names and aliases available via `requests.codes`:

| Code | Names / Aliases |
|---|---|
| 100 | continue |
| 200 | ok, okay, all_ok, all_okay, all_good, \o/, ✓ |
| 201 | created |
| 204 | no_content |
| 301 | moved_permanently, moved, \o- |
| 302 | found |
| 307 | temporary_redirect, temporary_moved, temporary |
| 400 | bad_request, bad |
| 401 | unauthorized |
| 403 | forbidden |
| 404 | not_found, -o- |
| 418 | im_a_teapot, teapot, i_am_a_teapot |
| 500 | internal_server_error, server_error, /o\, ✗ |
| 503 | service_unavailable, unavailable |

## Conclusion

Understanding HTTP headers and status codes is fundamental to building robust and effective web applications. Requests provides intuitive tools like `CaseInsensitiveDict` for header management and the `requests.codes` object for status code interpretation, simplifying common HTTP interactions. These core concepts lay the groundwork for more complex scenarios.

For more advanced configurations related to HTTP requests, proceed to the [Advanced Usage](./advanced-usage.md) section. If you need a comprehensive reference of all available APIs and their details, consult the [API Reference](./api-reference.md).