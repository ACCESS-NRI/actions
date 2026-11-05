# Workflow list

- [validate-json](#validate-json-workflow)
- [publish-python-package](#publish-python-package-workflow)
- [release-python-package](#release-python-package-workflow)

## validate-json workflow

This workflow validates JSON (`*.json`) against a given schema (`*.schema.json`), provided they are in the same directory. 

### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| src | string | A directory that contains both the `*.json` and `*.schema.json` files | false | '.' | './config' |

### Outputs

There are no explicit outputs for this workflow. 

However, it does return job failure/success based on the result of the schema validations. 

### Examples

```yaml
jobs:
  validate:
    uses: access-nri/actions/.github/workflows/validate-json.yml@main
    with:
      src: 'config'
```

## publish-python-package workflow

> [!INFO]
> For an out-of-the-box python package release workflow, which includes a GitHub Release and tag, consider using [release-python-package](#release-python-package-workflow) instead.

This workflow builds and publishes a pure python package distribution on Pypi and Anaconda.org.

> [!IMPORTANT]
> This workflow only works for pure Python projects, where the built distribution is compatible with any supported Python version or architecture.

> [!IMPORTANT]
> This workflow sets the PyPI token, Anaconda token, and Anaconda username using the calling repository's secrets `PYPI_TOKEN`, `ANACONDA_TOKEN` and `ANACONDA_USERNAME`, respectively. 
> Make sure to define these secrets in the calling repository, and to call this workflow with `secrets: inherit`. For more details, see [Usage](#usage).

> [!NOTE]
> This workflow does not support [PyPI Trusted Publisher](https://docs.pypi.org/trusted-publishers/) technology, as [it cannot be used within a reusable workflow at this time](https://github.com/pypa/gh-action-pypi-publish?tab=readme-ov-file#trusted-publishing). Therefore, a PyPI token is required to publish to PyPI.

### About
This workflow builds a Python wheel and source tarball from the project’s `pyproject.toml`, generates a conda recipe using [Grayskull](https://github.com/conda/grayskull), and builds the corresponding conda package. It then publishes the wheel to PyPI and the conda package to Anaconda.org. It also uploads the `python-package` and `conda-package` artifacts, containing the python packages, and conda packages and recipe, respectively.

### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| pyproject-toml-dir | string | The directory where the `pyproject.toml` file is located, relative to the repository top-level directory. | NO | `.` | `path/to/pyproject/dir` |
| pypi-package | bool | Whether to create the Python wheel and publish it to PyPI. | NO | `true` | `false` |
| conda-package | bool | Whether to create the Conda package and publish it to Anaconda.org. | NO | `true` | `false` |
| dry-run | bool | Whether to perform a dry run without actually publishing the package. This can be useful for testing the publishing workflow. | NO | `true` | `false` |

### Outputs

| Name | Type | Description | Example |
| --- | --- | --- | --- |
| python-artifact-name | string | The name of the artifact containing the built python packages; empty when `pypi-package` is `false` | `python_package` |
| conda-artifact-name | string | The name of the artifact containing the built conda packages and recipe; empty when `conda-package` is `false` | `conda_package` |

### Usage

> [!IMPORTANT]
> This workflow sets the PyPI token, Anaconda token, and Anaconda username using the calling repository's secrets `PYPI_TOKEN`, `ANACONDA_TOKEN` and `ANACONDA_USERNAME`, respectively.
> Make sure to define these secrets in the calling repository, and to call this workflow with `secrets: inherit`.

#### Basic usage
```yaml
jobs:
  publish_python_package:
    uses: access-nri/actions/.github/workflows/publish-python-package.yml@main
    secrets: inherit
```

#### Create a GitHub release with the built packages and tarball
```yaml
on:
  push:
    tags:
      - '**'
jobs:
  publish_python_package:
    uses: access-nri/actions/.github/workflows/publish-python-package.yml@main
    secrets: inherit
    permissions:
      actions: write
  
  create-github-release:
    name: Create GitHub Release
    runs-on: ubuntu-latest
    needs: publish_python_package
    permissions:
      contents: write
    steps:
      - name: Download Python artifact
        uses: actions/download-artifact@v8
        with:
          name: ${{ needs.publish_python_package.outputs.python-artifact-name }}
          path: my_artifact_dir/python

      - name: Download Conda artifact
        if: ${{ needs.publish_python_package.outputs.conda-artifact-name != '' }}
        uses: actions/download-artifact@v8
        with:
          name: ${{ needs.publish_python_package.outputs.conda-artifact-name }}
          path: my_artifact_dir/conda

      - name: Create Release
        uses: softprops/action-gh-release@6cbd405e2c4e67a21c47fa9e383d020e4e28b836 #v2.3.3
        with:
          tag_name: ${{ github.ref_name }}
          name: my-package ${{ github.ref_name }}
          generate_release_notes: true
          fail_on_unmatched_files: true
          files: |
            my_artifact_dir/**/*
```

## release-python-package workflow

This workflow calls [publish-python-package](#publish-python-package-workflow) to build and publish a pure Python package distribution on Pypi and Anaconda.org. Then, it creates a GitHub release with the built distributions and the Conda package and recipe attached as artifacts.

### Version and tag selection

- When the calling workflow is triggered by a tag push, that exact tag is used for the release.
- For other events, the version is read from `[project].version` in `pyproject.toml`. If the version is missing, the workflow fails. Otherwise, an annotated tag with that version is created and pushed before publishing.
- No version prefix is added. Projects with a dynamic version (`[dynamic].version` in `pyproject.toml`) should use a tag-push trigger.

### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| pyproject-toml-dir | string | The directory containing `pyproject.toml`, relative to the repository top-level directory. | NO | `.` | `path/to/pyproject/dir` |
| pypi-package | bool | Whether to publish the Python package to PyPI. | NO | `true` | `false` |
| conda-package | bool | Whether to build and publish the Conda package to Anaconda.org. | NO | `true` | `false` |
| dry-run | bool | Useful for testing releases. Build packages without publishing, creating a tag, or creating a GitHub release. The proposed tag and release are logged instead. | NO | `false` | `true` |

At least one of `pypi-package` or `conda-package` must be `true`.

### Outputs

| Name | Type | Description | Example |
| --- | --- | --- | --- |
| python-artifact-name | string | The name of the artifact containing the built Python packages; empty when `pypi-package` is `false` | `python_package` |
| conda-artifact-name | string | The name of the artifact containing the built Conda packages and recipe; empty when `conda-package` is `false` | `conda_package` |

### Usage

> [!IMPORTANT]
> This workflow sets the PyPI token, Anaconda token, and Anaconda username using the calling repository's secrets `PYPI_TOKEN`, `ANACONDA_TOKEN` and `ANACONDA_USERNAME`, respectively.
> Make sure to define these secrets in the calling repository, and to call this workflow with `secrets: inherit`.

#### Basic Usage

```yaml
on:
  push:
    tags:
      - '**'

jobs:
  release:
    uses: access-nri/actions/.github/workflows/release-python-package.yml@main
    secrets: inherit
    permissions:
      contents: write
```

#### Test a package release without publishing anything
```yaml
on:
  push:
    tags:
      - '**'

jobs:
  release:
    uses: access-nri/actions/.github/workflows/release-python-package.yml@main
    secrets: inherit
    permissions:
      contents: write
    with:
      dry-run: true
```