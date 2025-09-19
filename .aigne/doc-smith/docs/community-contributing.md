# Contributing

We welcome and appreciate contributions from the community! If you're looking to help improve the Requests library, this guide will walk you through setting up your development environment, running tests, and building the documentation.

## Cloning the Repository

First, you'll need a local copy of the source code. When cloning the Requests repository, you may encounter an error related to a bad commit timestamp. To avoid this, you should use a special git flag.

Use the following command to clone the repository:

```shell Cloning the Repository
$ git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

Alternatively, you can apply this setting to your global Git configuration to avoid having to specify it for every clone command:

```shell Global Git Configuration
$ git config --global fetch.fsck.badTimezone ignore
```

## Setting Up the Development Environment

Once you have the code, you need to set up an environment to install the necessary dependencies for development and testing. It's highly recommended to use a Python virtual environment for this.

1.  **Create and activate a virtual environment:**

    ```console Create Virtual Environment
    $ python -m venv venv
    $ source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

2.  **Install development dependencies:**

    The project includes a `requirements-dev.txt` file that lists all packages required for testing and development, such as `pytest`, `pytest-cov`, and `pytest-httpbin`. You can install them using pip:

    ```console Install Dependencies
    $ python -m pip install -r requirements-dev.txt
    ```

## Running Tests

Before submitting any changes, it's crucial to run the test suite to ensure that your changes don't break existing functionality. The project uses `pytest` for testing.

There are several ways to run the tests, depending on your needs.

-   **Run the standard test suite:**

    ```console Run Basic Tests
    $ python -m pytest tests
    ```

-   **Generate a test coverage report:**

    To check how much of the codebase is covered by tests, run the coverage command. This will output a report to the terminal and also create an XML file.

    ```console Generate Coverage Report
    $ python -m pytest --cov-config .coveragerc --verbose --cov-report term --cov-report xml --cov=src/requests tests
    ```

-   **Run tests for Continuous Integration (CI):**

    This command runs the tests and generates a `report.xml` file in JUnit XML format, which is commonly used by CI systems.

    ```console Run CI Tests
    $ python -m pytest tests --junitxml=report.xml
    ```

## Building the Documentation

If your contribution involves changes to the documentation, you should build it locally to preview your changes and ensure everything renders correctly.

To build the HTML documentation, navigate to the `docs` directory and use `make`:

```console Build Documentation
$ cd docs && make html
```

After the build is successful, you can view the generated documentation by opening the following file in your browser:

`docs/_build/html/index.html`
