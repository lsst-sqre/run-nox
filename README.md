# run-tox

This is a [composite GitHub Action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) for running CI tasks through [nox](https://nox.thea.codes/en/stable/).

The action:

1. Sets up Python
2. Installs/updates pip and nox
3. Caches the nox sessions
4. Runs nox with the sessions you specify

## Example usage

```yaml
name: Python CI

"on":
  push:
    tags:
      - "*"
    branches:
      - "main"
  pull_request: {}

jobs:
  tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python:
          - "3.11"

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # full history for setuptools_scm

      - name: Run nox
        uses: lsst-sqre/run-nox@v1
        with:
          python-version: ${{ matrix.python }}
          nox-sessions: "typing test"
          cache-dependency: "**/requirements/*.txt"
```

## Inputs

- `cache-dependency` (string, optional) Glob pattern matching the files that contribute to which dependencies are installed in the nox sessions. This should match either the contributing frozen dependency files or the contributing `pyproject.toml` files. Unfortunately due to limitations in the GitHub Actions syntax only a single glob pattern is supported. Default is `pyproject.toml`.
- `cache-key-prefix` (string, optional) Prefix for the tox environment cache key. Set to distinguish from other caches. Default is `tox`.
- `nox-sessions` (string, required) The nox sessions to run, as a space-separated list. Example: `typing test` to run a type checking session and a Python test session.
- `nox-package` (string, optional) Pip requirement for nox itself (argument to `pip install`). Default is `nox`, without any version constraints.
- `nox-posargs` (string, optional) Space-separated command line arguments to pass to the nox command. The positional arguments are made available as the `session.posargs` attribute to nox sessions. Default is not to pass any arguments.
- `python-version` (string, required) The Python version.
- `use-cache` (boolean, optional) Flag to enable caching of the nox environment. Default is `true`.

## Outputs

No outputs.

## Developer guide

This repository provides a **composite** GitHub Action, a type of action that packages multiple regular actions into a single step.
We do this to make the GitHub Actions workflows of all our software projects more consistent and easier to maintain.
[You can learn more about composite actions in the GitHub documentation.](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)

Create new releases using the GitHub Releases UI and assign a tag with a [semantic version](https://semver.org), including a `v` prefix. Choose the semantic version based on compatibility for users of this workflow. If backwards compatibility is broken, bump the major version.

When a release is made, a new major version tag (i.e. `v1`, `v2`) is also made or moved using [nowactions/update-majorver](https://github.com/marketplace/actions/update-major-version).
We generally expect that most users will track these major version tags.
