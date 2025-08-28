# Community

Requests is one of the most downloaded Python packages today, depended upon by over 1,000,000 repositories. This success is built on a strong community of developers and users. This section provides insight into the project's history, acknowledges the many contributors who have shaped it, and explains how you can get involved.

The project was created by Kenneth Reitz and is now maintained under the umbrella of the Python Software Foundation.

<x-cards>
  <x-card data-title="Changelog" data-icon="lucide:history" data-href="/community/changelog">
    Explore the detailed history of every version of Requests, from the initial release to the latest updates. See how features have evolved and bugs have been fixed over time.
  </x-card>
  <x-card data-title="Contributors" data-icon="lucide:users" data-href="/community/contributors">
    Meet the people behind Requests. This project is the result of countless contributions from individuals around the world, maintained by a dedicated team.
  </x-card>
</x-cards>

## How to Contribute

We welcome contributions from everyone. Whether it's reporting a bug, suggesting an improvement, or submitting a pull request, your help is valuable. The typical contribution workflow is as follows:

```d2
direction: down

find_issue: "Find a Bug or Have an Idea"
open_issue: "Open an Issue on GitHub"
discuss: "Discuss with Maintainers"
fork: "Fork the Repository"
branch: "Create a New Branch"
code: "Write Code & Add Tests"
run_tests: "Run Tests Locally" 
pr: "Submit a Pull Request"
review: "Code Review"
merge: "Merge to Main" { 
  shape: circle 
}

find_issue -> open_issue
open_issue -> discuss
discuss -> fork
fork -> branch
branch -> code
code -> run_tests: "python -m pytest tests"
run_tests -> pr
pr -> review
review -> merge
```

### Reporting Bugs

When you encounter a bug, providing detailed information about your environment is crucial for troubleshooting. Requests includes a built-in helper script to gather and format this information.

To generate a bug report summary, run the following command:

```shell
python -m requests.help
```

This will output a JSON object containing details about your Python environment, installed dependencies, and SSL versions. Please include this output in your bug report.

### Setting Up for Development

To work on the Requests codebase, you first need to clone the repository. Due to a known issue with an old commit timestamp, you may need to add a flag to avoid an error:

```shell
git clone -c fetch.fsck.badTimezone=ignore https://github.com/psf/requests.git
```

Alternatively, you can apply this setting to your global Git configuration:

```shell
git config --global fetch.fsck.badTimezone ignore
```

After cloning, you can install the development dependencies and run the test suite to verify your setup:

```shell
# Install development dependencies
python -m pip install -r requirements-dev.txt

# Run tests
python -m pytest tests
```

The strength of Requests lies in its community. By exploring the [Changelog](./community-changelog.md) and the list of [Contributors](./community-contributors.md), you can see the collaborative effort that makes this library robust and reliable.