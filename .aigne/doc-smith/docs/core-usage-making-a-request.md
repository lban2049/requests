# Making a Request

Making an HTTP request with the Requests library is straightforward and intuitive. For every major HTTP verb, there is a corresponding function that handles the request-response cycle for you. This section covers the basic patterns for making requests and inspecting the responses you receive.

## Making a GET Request

The `GET` method is used to request data from a specified resource. This is the most common type of request you'll make. To send a `GET` request, simply use the `requests.get()` function.

```python A simple GET request icon=logos:python
import requests

r = requests.get('https://api.github.com/events')
```

When you make a request, Requests returns a `Response` object. This object contains all the information sent back by the server, including the status code, headers, and the response body. A successful `GET` request will typically have a status code of `200`.

```python Checking the response status icon=logos:python
>>> r.status_code
200
```

## Other HTTP Methods

Requests provides a simple function for every standard HTTP method, making your code clean and readable.

<x-cards data-columns="2">
  <x-card data-title="POST" data-icon="lucide:send-to-back">
    Used to submit an entity to the specified resource, often causing a change in state or side effects on the server.
  </x-card>
  <x-card data-title="PUT" data-icon="lucide:arrow-up-square">
    Replaces all current representations of the target resource with the request payload.
  </x-card>
  <x-card data-title="DELETE" data-icon="lucide:trash-2">
    Deletes the specified resource.
  </x-card>
  <x-card data-title="PATCH" data-icon="lucide:edit">
    Used to apply partial modifications to a resource.
  </x-card>
  <x-card data-title="HEAD" data-icon="lucide:heading-1">
    Asks for a response identical to a GET request, but without the response body. Useful for checking metadata like headers before a full download.
  </x-card>
  <x-card data-title="OPTIONS" data-icon="lucide:sliders-horizontal">
    Used to describe the communication options for the target resource.
  </x-card>
</x-cards>

Here are some quick examples for each method:

```python Example HTTP method calls icon=logos:python
# POST request
r = requests.post('https://httpbin.org/post', data={'key': 'value'})

# PUT request
r = requests.put('https://httpbin.org/put', data={'key': 'value'})

# DELETE request
r = requests.delete('https://httpbin.org/delete')

# HEAD request
r = requests.head('https://httpbin.org/get')

# OPTIONS request
r = requests.options('https://httpbin.org/get')

# PATCH request
r = requests.patch('https://httpbin.org/patch', data={'key':'value'})
```

For more detailed information on sending data in the body of your requests, see the [POSTing Data](./core-usage-posting-data.md) section.

## The Generic `request()` Function

Under the hood, all the specific HTTP method functions (`get`, `post`, etc.) are convenient wrappers for the central `requests.request()` function. If you prefer, or if you need to specify the method dynamically, you can use it directly.

For example, `requests.get(url)` is equivalent to `requests.request('get', url)`.

```python Using the generic request() function icon=logos:python
>>> import requests
>>> req = requests.request('GET', 'https://httpbin.org/get')
>>> req
<Response [200]>
```

The `request` function accepts the following parameters:

<x-field-group>
  <x-field data-name="method" data-type="string" data-required="true" data-desc="The HTTP method for the request: GET, OPTIONS, HEAD, POST, PUT, PATCH, or DELETE."></x-field>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="The URL for the new Request object."></x-field>
  <x-field data-name="params" data-type="dict | list | bytes" data-required="false" data-desc="Data to send in the query string of the Request."></x-field>
  <x-field data-name="data" data-type="dict | list | bytes | file" data-required="false" data-desc="Data to send in the body of the Request."></x-field>
  <x-field data-name="json" data-type="object" data-required="false" data-desc="A JSON serializable Python object to send in the body of the Request."></x-field>
  <x-field data-name="headers" data-type="dict" data-required="false" data-desc="A dictionary of HTTP Headers to send with the Request."></x-field>
  <x-field data-name="cookies" data-type="dict | CookieJar" data-required="false" data-desc="A dictionary or CookieJar object to send with the Request."></x-field>
  <x-field data-name="files" data-type="dict" data-required="false" data-desc="A dictionary for multipart encoding uploads."></x-field>
  <x-field data-name="auth" data-type="tuple" data-required="false" data-desc="An authentication tuple to enable Basic/Digest/Custom HTTP Auth."></x-field>
  <x-field data-name="timeout" data-type="float | tuple" data-required="false" data-desc="How many seconds to wait for the server to send data before giving up."></x-field>
  <x-field data-name="allow_redirects" data-type="boolean" data-default="true" data-required="false" data-desc="Enable or disable redirection. Defaults to True."></x-field>
  <x-field data-name="proxies" data-type="dict" data-required="false" data-desc="Dictionary mapping protocol to the URL of the proxy."></x-field>
  <x-field data-name="verify" data-type="boolean | string" data-default="true" data-required="false" data-desc="Controls TLS certificate verification. Can be a boolean or a path to a CA bundle."></x-field>
  <x-field data-name="stream" data-type="boolean" data-default="false" data-required="false" data-desc="If False, the response content will be immediately downloaded."></x-field>
  <x-field data-name="cert" data-type="string | tuple" data-required="false" data-desc="Path to an SSL client cert file (.pem) or a ('cert', 'key') tuple."></x-field>
</x-field-group>

## Inspecting the Response

Once you have the `Response` object, you can access all the information you need. As shown earlier, you can check the status code, but you can also view headers and the response body.

```python Inspecting the Response object icon=logos:python
>>> r = requests.get('https://httpbin.org/json')

# Check the status code for success
>>> r.status_code
200

# View the response headers, which are returned as a Python dictionary
>>> r.headers['content-type']
'application/json'

# Get the server's encoding
>>> r.encoding
'utf-8'

# Access the response body as plain text
>>> r.text
'{\n  "slideshow": {\n    "author": "Yours Truly", \n    "date": "date of publication", ...'

# Or, for JSON responses, let Requests handle the decoding
>>> r.json()
{'slideshow': {'author': 'Yours Truly', 'date': 'date of publication', ...}}
```

## Next Steps

Now that you know how to make basic requests, you can explore how to customize them further:

<x-cards data-columns="3">
  <x-card data-title="Passing URL Parameters" data-icon="lucide:at-sign" data-href="/core-usage/passing-url-parameters">
    Learn how to add query strings to your request URLs.
  </x-card>
  <x-card data-title="Handling Response Content" data-icon="lucide:file-text" data-href="/core-usage/handling-response-content">
    Dive deeper into accessing the response body in various formats.
  </x-card>
  <x-card data-title="POSTing Data" data-icon="lucide:file-up" data-href="/core-usage/posting-data">
    Explore different ways to send data in the body of your request.
  </x-card>
</x-cards>