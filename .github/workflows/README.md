# Workflow list

- [validate-json](#validate-json-workflow)
- [publish-python-package](#publish-python-package-workflow)

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
    uses: access-nri/actions/.github/workflows/validate-json@main
    with:
      src: 'config'
```

## publish-python-package workflow

This workflow builds and publishes a pure python package distribution on Pypi and Anaconda.org.

> [!IMPORTANT]
> This workflow only works for pure Python projects, where the built distribution is compatible with any supported Python version or architecture.

> [!IMPORTANT]
> This workflow automatically sets the default PyPI token, Anaconda token, and Anaconda username using specific GitHub secrets from the calling repository. Therefore, if any of the inputs `pypi-token`, `anaconda-token`, or `anaconda-username` are not provided, the workflow must be run with `secrets: inherit`. For more details, see [Usage](#usage).

> [!NOTE]
> This workflow does not support [PyPI Trusted Publisher](https://docs.pypi.org/trusted-publishers/) technology, as [it cannot be used within a reusable workflow at this time](https://github.com/pypa/gh-action-pypi-publish?tab=readme-ov-file#trusted-publishing). Therefore, a PyPI token is required to publish to PyPI.

### About
This workflow builds a Python wheel and source tarball from the project’s `pyproject.toml`, generates a conda recipe using [Grayskull](https://github.com/conda/grayskull), and builds the corresponding conda package. It then publishes the wheel to PyPI and the conda package to Anaconda.org, while also uploading an artifact containing the wheel, conda package, and tarball for further use.

### Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| pyproject-toml-dir | bool | The directory where the `pyproject.toml` file is located, relative to the repository top-level directory. | NO | `.` | `path/to/pyproject/dir` |
| pypi-package | bool | Whether to create the Python wheel and publish it to PyPI. | NO | `true` | `false` |
| conda-package | bool | Whether to create the Conda package and publish it to Anaconda.org. | NO | `true` | `false` |
| pypi-token | string | The token used to publish the package to PyPI. Ignored if `pypi-package` is `false`. | NO | `${{ secrets.PYPI_TOKEN }}` | `${{ secrets.MY_CUSTOM_PYPI_TOKEN }}` |
| anaconda-token | string | The token used to publish the package to Anaconda.org. Ignored if `conda-package` is `false`. | NO | `${{ secrets.ANACONDA_TOKEN }}` | `${{ secrets.MY_CUSTOM_ANACONDA_TOKEN }}` |
| anaconda-username | string | The name of the conda channel where the package will be published to. Ignored if `conda-package` is false. | NO | `${{ secrets.ANACONDA_USERNAME }}` | `${{ secrets.MY_CUSTOM_ANACONDA_USERNAME }}` |

### Outputs

| Name | Type | Description | Example |
| --- | --- | --- | --- |
| artifact-name | string | The name of the artifact that gets uploaded by the workflow | `_dist_artifact` |

### Usage

#### Basic Usage
```yaml
jobs:
  publish_python_package:
    uses: access-nri/actions/.github/workflows/publish-python-package.yml@main
    secrets: inherit
```

> [!NOTE]
> This will use the PyPI token, Anaconda token and Anaconda username from the calling repo's `${{ secrets.PYPI_TOKEN }}`, `${{ secrets.ANACONDA_TOKEN }}` and `${{ secrets.ANACONDA_USERNAME }}`, respectively.


#### Custom anaconda token
```yaml
jobs:
  publish_python_package:
    uses: access-nri/actions/.github/workflows/publish-python-package.yml@main
    secrets: inherit
    with:
      anaconda-token: ${{ secrets.CUSTOM_ANACONDA_TOKEN }}
```

> [!NOTE]
> This will use the PyPI token and Anaconda username from the calling repo's `${{ secrets.PYPI_TOKEN }}` and `${{ secrets.ANACONDA_USERNAME }}`, respectively.


#### Custom toml directory, pypi token and with no conda package publishing
```yaml
jobs:
  publish_python_package:
    uses: access-nri/actions/.github/workflows/publish-python-package.yml@main
    secrets: inherit
    with:
      pyproject-toml-dir: "myproject/"
      pypi-token: ${{ secrets.MY_PYPI_TOKEN }}
      conda-package: false
```

> [!NOTE]
> This will use the Anaconda token and username from the calling repo's `${{ secrets.ANACONDA_TOKEN }}` and `${{ secrets.ANACONDA_USERNAME }}`, respectively.

