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

## Configuration

Update your `pyproject.toml` to use `build_meta_plus` as the build backend:

```toml
[build-system]
requires = ["setuptools>=61", "wheel", "build_meta_plus"]
build-backend = "build_meta_plus"

[tool.build_meta_plus]
pre-build = [
    "echo 'Starting build...'"
]
post-build = [
    "rm -rf *.egg-info",
    "echo 'Cleanup complete.'"
]
```

## Usage

Interaction is identical to standard Python build tools:

- **Build Package:** `python -m build`
- **Install Package:** `pip install .`
- **Editable Install:** `pip install -e .`

## How it works

The backend wraps the standard `build_sdist`, `build_wheel`, and `build_editable` hooks. When any of these are called, `build_meta_plus` executes the commands defined in `pre-build`, runs the original `setuptools` hook, and then executes the `post-build` commands.
