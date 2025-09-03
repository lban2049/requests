# Community

Requests is a project fueled by its community. As one of the most downloaded Python packages today, pulling in around 30 million downloads per week, it is depended upon by over a million repositories. This widespread adoption is a testament to the vibrant community of developers who use, support, and contribute to the library.

Originally created by Kenneth Reitz, Requests is now proudly maintained under the umbrella of the Python Software Foundation (PSF), ensuring its continued development and stability for years to come.

![Python Software Foundation](https://raw.githubusercontent.com/psf/requests/main/ext/psf.png)

## Explore the Community

The Requests community is made up of developers, contributors, and maintainers. Here's how you can learn more about the project's history and the people behind it.

<x-cards data-columns="2">
  <x-card data-title="Changelog" data-icon="lucide:list-checks" data-href="/community/changelog" data-cta="View Changelog">
    Stay up-to-date with the latest developments, bug fixes, and feature enhancements. Our detailed changelog provides a complete history of every version of Requests.
  </x-card>
  <x-card data-title="Contributors" data-icon="lucide:users" data-href="/community/contributors" data-cta="See Contributors">
    The success of Requests is thanks to the many individuals who have contributed their time and expertise. See the full list of maintainers and contributors who have shaped the project.
  </x-card>
</x-cards>

## Getting Involved

Whether you're reporting a bug or contributing code, your participation is welcome.

### Reporting Bugs

If you encounter a bug, providing a detailed report is the most effective way to help. Requests includes a built-in helper to generate a summary of your system configuration, which helps us diagnose issues more quickly.

To use it, run the following command in your terminal:

```shell
python -m requests.help
```

This will output a JSON object with version information for Requests and its dependencies, which you can include in your bug report.

### Contributing Code

Ready to contribute? Start by setting up a local development environment. You'll need to clone the repository first. Due to historical commit timestamps, you may need to add a specific git configuration flag to avoid errors:

```shell
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

Alternatively, you can apply this setting to your global Git configuration:

```shell
git config --global fetch.fsck.badTimezone ignore
```

After cloning, you can install the development dependencies and run the test suite to ensure everything is set up correctly.

---

The community is the backbone of Requests. We encourage you to get involved and help us continue to build a simple, yet elegant, HTTP library for everyone. To learn more about how to use the library, head over to the [User Guide](./user-guide.md).