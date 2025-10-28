# PR Comment Action

This action is a shorthand way to determine whether a given git ref is a branch, tag or commit.

If the specified ref doesn't exist, this action will exit.

## Inputs

| Name | Description | Required | Default | Example |
| ---- | ----------- | -------- | ------- | ------- |
| `repository` | The repository to get the git ref info from, in the format `owner/repo` | `true` | N/A | `"ACCESS-NRI/spack-packages"` |
| `ref` | The simple git reference (branch, tag, or commit SHA) to retrieve information about | `true` | N/A | `some-feature-branch` (branch), `v2.1` (tag), `8d29u23` (short SHA), `03d17eebfda5b873c576bad1dd77acdb8` (full SHA) |
| `token` | A GitHub Token/PAT that allows cloning of the repository | `false` | `github.token` | `gha_pat_abcds...` |

## Outputs

| Name | Description | Example |
| ---- | ----------- | ------- |
| `ref-type` | Type of the given ref, if it's valid | One of `branch`, `tag`, `commit` |
| `sha` | Full SHA of the given ref, if it's valid | `03d17eebfda5b873c576bad1dd77acdb8` |

## Examples

```yaml
# ...
jobs:
  get-ref:
    runs-on: ubuntu-latest
    env:
     ref: main
    steps:
    - id: resolve-ref
      uses: access-nri/actions/.github/actions/pr-comment@main
      with:
        repository: ACCESS-NRI/actions@main
        ref: ${{ env.ref }}

    - run: echo "Ref ${{ env.ref }} is a ${{ steps.resolve-ref.outputs.ref-type }} with SHA ${{ steps.resolve-ref.outputs.sha }}"
```
