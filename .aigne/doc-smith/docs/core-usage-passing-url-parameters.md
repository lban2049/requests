# Passing URL Parameters

Often, you need to send data in the URL's query string. If you were constructing the URL by hand, this data would be given as key/value pairs after a question mark, e.g., `httpbin.org/get?key=val`. Requests simplifies this by allowing you to provide these arguments as a dictionary or a list of tuples, using the `params` keyword argument.

### Passing Parameters in a Dictionary

For most cases, using a dictionary is the simplest way to add query parameters to a URL. Requests will automatically format the dictionary into a URL-encoded query string.

**Parameters**

<x-field-group>
  <x-field data-name="url" data-type="string" data-required="true" data-desc="URL for the new Request object."></x-field>
  <x-field data-name="params" data-type="dict | list | bytes" data-required="false" data-desc="Data to send in the query string of the Request."></x-field>
</x-field-group>

**Example**

Let's pass `key1=value1` and `key2=value2` to the `httpbin.org/get` endpoint:

```python Sending a GET request with URL parameters icon=logos:python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

You can see that the URL has been correctly encoded by printing it. The output will be:

```text URL Output
https://httpbin.org/get?key1=value1&key2=value2
```

**Example Response**

The response from `httpbin.org` will reflect the parameters you sent:

```json Response from httpbin.org icon=mdi:code-json
{
  "args": {
    "key1": "value1", 
    "key2": "value2"
  }, 
  ...
}
```

### Passing a List of Tuples

In some cases, you may need to supply multiple values for a single key. To achieve this, you should pass a list of tuples as the value for `params`.

**Example**

```python Passing multiple values for a single key icon=logos:python
import requests

payload = [('key1', 'value1'), ('key1', 'value2')]
r = requests.get('https://httpbin.org/get', params=payload)

print(r.url)
```

This will result in a URL where `key1` appears twice:

```text URL Output
https://httpbin.org/get?key1=value1&key1=value2
```

**Example Response**

`httpbin.org` will parse this into a list of values for the `key1` argument:

```json Response from httpbin.org icon=mdi:code-json
{
  "args": {
    "key1": [
      "value1", 
      "value2"
    ]
  }, 
  ...
}
```

By using the `params` argument, you let Requests handle the URL encoding, ensuring that your parameters are sent correctly without manual string manipulation.

Now that you know how to make a request with parameters, the next step is to learn about [Handling Response Content](./core-usage-handling-response-content.md).