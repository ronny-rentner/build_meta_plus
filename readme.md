# build-meta-plus

[![PyPI Package](https://img.shields.io/pypi/v/build-meta-plus.svg)](https://pypi.org/project/build-meta-plus)
[![Run Tests](https://github.com/ronny-rentner/build-meta-plus/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/ronny-rentner/build-meta-plus/actions/workflows/tests.yml)
[![Python >=3.9](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)

A lightweight, drop-in replacement for the default [setuptools.build_meta](https://setuptools.pypa.io/en/latest/build_meta.html) backend that supports automated pre- and post-build hooks via `pyproject.toml`.

`build_meta_plus` maintains full compatibility with `setuptools` while restoring the flexibility of custom build steps—ideal for replacing legacy `setup.py` hooks in modern PEP 517 projects.

## Why use this?

`build_meta_plus` is ideal for streamlining your development workflow, from automating pre-build generation tasks to maintaining a tidy project environment by automatically managing temporary build artifacts.

For example, you can ensure your workspace remains clean by removing `.egg-info` directories immediately after a build:

```toml
[tool.build_meta_plus]
post-build = ["rm -rf *.egg-info"]
```

## Getting Started

To use `build_meta_plus` in your project, configure your `pyproject.toml` to use it as the build backend and define your hooks:

```toml
[build-system]
requires = ["setuptools>=61", "wheel", "build_meta_plus"]
build-backend = "build_meta_plus"

[tool.build_meta_plus]
pre-build = [
    "echo 'Generating assets...'",
    "python scripts/generate_version.py"
]
post-build = [
    "rm -rf *.egg-info",
    "echo 'Cleanup complete.'"
]
```

When you run standard build or install commands (like `python -m build` or `pip install .`), `build_meta_plus` automatically intercepts the PEP 517/660 hooks, executes your `pre-build` commands, delegates the actual build to `setuptools`, and finally runs your `post-build` cleanup.
