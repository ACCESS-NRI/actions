# publish-python-package workflow

This workflow builds and publishes a pure python package distribution on Pypi and Anaconda.org.

> [!IMPORTANT]
> This workflow only works for pure Python projects, where the built distribution is compatible with any supported Python version or architecture.

> [!IMPORTANT]
> When any of the `pypi_token`, `anaconda_token` or `anaconda_username` inputs is not provided, the workflow needs to be run with `secrets: inherit`. For more information, refer to [Usage](#usage).

> [!NOTE]
> This workflow does not support [PyPI Trusted Publisher](https://docs.pypi.org/trusted-publishers/) technology, as [it cannot be used within a reusable workflow at this time](https://github.com/pypa/gh-action-pypi-publish?tab=readme-ov-file#trusted-publishing). Therefore, a PyPI token is required to publish to PyPI.

## About
This workflow builds a Python wheel and source tarball from the project’s `pyproject.toml`, generates a conda recipe using [Grayskull](https://github.com/conda/grayskull), and builds the corresponding conda package. It then publishes the wheel to PyPI and the conda package to Anaconda.org, while also uploading an artifact containing the wheel, conda package, and tarball for further use.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| pyproject_toml_dir | bool | The directory where the `pyproject.toml` file is located, relative to the repository top-level directory. | NO | `.` | `path/to/pyproject/dir` |
| pypi_package | bool | Whether to create the Python wheel and publish it to PyPI. | NO | `true` | `false` |
| conda_package | bool | Whether to create the Conda package and publish it to Anaconda.org. | NO | `true` | `false` |
| pypi_token | string | The token used to publish the package to PyPI. Ignored if `pypi_package` is `false`. | NO | `${{ secrets.PYPI_TOKEN }}` | `${{ secrets.MY_CUSTOM_PYPI_TOKEN }}` |
| anaconda_token | string | The token used to publish the package to Anaconda.org. Ignored if `conda_package` is `false`. | NO | `${{ secrets.ANACONDA_TOKEN }}` | `${{ secrets.MY_CUSTOM_ANACONDA_TOKEN }}` |
| anaconda_username | string | The name of the conda channel where the package will be published to. Ignored if `conda_package` is false. | NO | `${{ secrets.ANACONDA_USERNAME }}` | `${{ secrets.MY_CUSTOM_ANACONDA_USERNAME }}` |

## Outputs

| Name | Type | Description | Example |
| --- | --- | --- | --- |
| artifact-name | string | The name of the artifact that gets uploaded by the workflow | `_dist_artifact` |

## Usage

```yaml

```
