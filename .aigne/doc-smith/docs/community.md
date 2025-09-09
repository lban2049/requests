# Community

Requests is a community-driven project, lovingly created by Kenneth Reitz and maintained by a dedicated team of volunteers. It has become one of the most downloaded Python packages, pulling in around 30 million downloads per week, and is a dependency for over one million repositories on GitHub. This success is a testament to the vibrant community that supports and contributes to its development. This section provides insight into the project's history, its contributors, and how you can get involved.

## Project History and Contributors

The project has a long and rich history of evolution, from its initial conception to its current status as a core part of the Python ecosystem. The development is guided by the "Keepers of the Crystals," and its success is built upon the countless contributions from developers around the world.

To explore the project's journey and acknowledge the people behind it, please visit the dedicated pages:

<x-cards>
  <x-card data-title="Changelog" data-icon="lucide:history" data-href="/community/changelog">
    A detailed log of all changes, improvements, and bug fixes for each version of the Requests library.
  </x-card>
  <x-card data-title="Contributors" data-icon="lucide:users" data-href="/community/contributors">
    A list of all the individuals who have contributed to the development and success of the Requests library.
  </x-card>
</x-cards>

## How to Get Involved

Contributions are always welcome, whether it's reporting a bug, improving the documentation, or submitting a patch. Here’s how you can get started.

### Reporting Bugs

If you encounter a bug, you can help us by providing a detailed report. Requests includes a helpful utility to generate a summary of your system's configuration, which is invaluable for debugging.

Run the following command and include the output in your bug report:

```shell title="Generate System Info"
python -m requests.help
```

### Contributing Code and Documentation

If you'd like to contribute directly to the project, you can start by setting up a local development environment.

1.  **Clone the repository**

    Due to a known issue with commit timestamps, it's recommended to use the following command to clone the repository:

    ```shell title="Cloning the repository"
    git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
    cd requests
    ```

2.  **Install development dependencies**

    Use pip to install the necessary packages for testing and development.

    ```shell title="Install dependencies"
    python -m pip install -r requirements-dev.txt
    ```

3.  **Run tests**

    Before making any changes, ensure that all existing tests pass.

    ```shell title="Run tests"
    python -m pytest tests
    ```

4.  **Build documentation**

    If you are making changes to the documentation, you can build it locally to preview your changes.

    ```shell title="Build docs"
    make docs
    ```

After making your changes and adding relevant tests, you can submit a pull request on GitHub for review.