# build_meta_plus

A lightweight `setuptools` build backend wrapper that supports pre- and post-build hooks via `pyproject.toml`.

`build_meta_plus` delegates all standard PEP 517/660 build logic to [setuptools.build_meta](https://setuptools.pypa.io/en/latest/build_meta.html), but allows you to execute shell commands at key stages of the build process.

## Why use this?

The primary use case is cleaning up "trash" files that Python's build system leaves behind, or performing pre-build generation tasks.

For example, you can automatically remove `.egg-info` directories after a build:

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
