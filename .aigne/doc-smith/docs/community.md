# Community

Requests is one of the most downloaded Python packages today, pulling in around **30 million downloads per week**. According to GitHub, it is currently depended upon by over **1,000,000 repositories**. This incredible success is a testament to its simple, elegant design and the vibrant community that supports it.

The project was lovingly created by Kenneth Reitz and is now maintained by a dedicated team of "Keepers of the Crystals," ensuring its stability and continued development.

<x-cards>
  <x-card data-title="Changelog" data-icon="lucide:list-ordered" data-href="/community/changelog" >
    Explore the complete history of changes, including new features, bug fixes, and deprecations for every version of Requests.
  </x-card>
  <x-card data-title="Contributors" data-icon="lucide:users" data-href="/community/contributors" >
    See the full list of individuals who have contributed their time and expertise to making Requests the powerful tool it is today.
  </x-card>
</x-cards>

## How to Contribute

We welcome and appreciate contributions of all kinds, from bug reports and documentation improvements to new feature suggestions. Here’s how you can get started.

### Setting Up Your Environment

First, you'll need to clone the repository. Due to a historical commit, you may need to add a special configuration flag to avoid an error:

```shell Cloning the Repository icon=lucide:git-branch
# Clone with the necessary config flag
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git

# Or, apply the setting to your global Git config
git config --global fetch.fsck.badTimezone ignore
```

Once you have the code, you can install the development dependencies and run the test suite to ensure everything is working correctly.

```shell Running Tests icon=lucide:beaker
# Navigate into the cloned directory
cd requests

# Install development dependencies
python -m pip install -r requirements-dev.txt

# Run the test suite
python -m pytest tests
```

### Reporting Bugs

A great bug report is a huge help to the maintainers. To ensure we have all the necessary information about your environment, Requests includes a built-in helper module.

Before creating an issue, please run the following command and include its output in your report:

```shell Generating a Bug Report icon=lucide:bug
python -m requests.help
```

This command will print a JSON object containing details about your Python version, system, and the versions of key dependencies like `urllib3`, `idna`, and `OpenSSL`, which is invaluable for debugging.

## Next Steps

After exploring our community resources, you might want to dive deeper into the library's features.

<x-cards>
  <x-card data-title="User Guide" data-icon="lucide:book-open" data-href="/user-guide" >
    Learn the core functionalities of Requests through practical, code-first examples.
  </x-card>
  <x-card data-title="Advanced Usage" data-icon="lucide:rocket" data-href="/advanced-usage" >
    Explore advanced features like custom adapters, SSL verification, and proxies.
  </x-card>
</x-cards>