# Community

Requests is a vibrant, community-driven project, lovingly created by Kenneth Reitz and now maintained under the umbrella of the Python Software Foundation. Its success and robustness are a testament to the contributions of hundreds of developers from around the world.

<div style="display: flex; gap: 1rem; align-items: center;">
  <img src="../../../ext/kr.png" alt="Kenneth Reitz" width="150"/>
  <img src="../../../ext/psf.png" alt="Python Software Foundation" width="150"/>
</div>

The project thrives on collaboration and is always welcoming new contributors. Whether you're fixing a bug, improving documentation, or suggesting a new feature, your input is valuable. Below you can find more information about the project's history and the people behind it.

<x-cards>
  <x-card data-title="Changelog" data-href="/community/changelog" data-icon="lucide:history">
    Explore the project's evolution through a detailed log of all changes, improvements, and bug fixes for each version of the library.
  </x-card>
  <x-card data-title="Contributors" data-href="/community/contributors" data-icon="lucide:users">
    See the full list of individuals who have generously dedicated their time and expertise to making Requests what it is today.
  </x-card>
</x-cards>

## How to Contribute

One of the most effective ways to contribute to Requests is by reporting bugs. A well-documented bug report helps maintainers identify and fix issues quickly. To streamline this process, Requests includes a built-in helper tool to gather relevant system information.

### Reporting Bugs

Before opening an issue, please run the following command in your terminal. It collects essential details about your environment, such as your Python version, operating system, and the versions of critical dependencies like `urllib3` and `idna`.

```bash
python -m requests.help
```

This command will output a JSON object containing the necessary diagnostic information. Please include this output in your bug report.

**Example Output:**

```json
{
  "chardet": {
    "version": "5.2.0"
  },
  "charset_normalizer": {
    "version": null
  },
  "cryptography": {
    "version": "42.0.5"
  },
  "idna": {
    "version": "3.7"
  },
  "implementation": {
    "name": "CPython",
    "version": "3.12.3"
  },
  "platform": {
    "release": "23.5.0",
    "system": "Darwin"
  },
  "pyOpenSSL": {
    "openssl_version": "1111014f",
    "version": "24.1.0"
  },
  "requests": {
    "version": "2.32.3"
  },
  "system_ssl": {
    "version": "1111014f"
  },
  "urllib3": {
    "version": "2.2.1"
  },
  "using_pyopenssl": true,
  "using_charset_normalizer": true
}
```

By providing this data, you help us spend less time asking for basic information and more time fixing the problem. We appreciate your help in keeping Requests reliable and robust for everyone.
