# Get git ref info Action

This action is a shorthand way to determine whether a given git ref is a branch, tag or commit.

If the specified ref doesn't exist, this action will fail with an error.

## Inputs

| Name | Description | Required | Default | Example |
| ---- | ----------- | -------- | ------- | ------- |
| `repository` | The repository to checkout and get the git ref info from, in the format `owner/repo`. Mutually exclusive with inputs.repository-path | This or `inputs.repository-path` required | N/A | `"ACCESS-NRI/spack-packages"` |
| `repository-path` | The path to a full-history local git repository to get the git ref info from. Mutually exclusive with inputs.repository | This or `inputs.repository` required | N/A | `"/opt/some/git/repo"` |
| `ref` | The simple git reference (branch, tag, or commit SHA) to retrieve information about | `true` | N/A | `some-feature-branch` (branch), `v2.1` (tag), `8d29u23` (short SHA), `03d17eebfda5b873c576bad1dd77acdb8` (full SHA) |
| `token` | A GitHub Token/PAT that allows cloning of the repository | `false` | `github.token` | `gha_pat_abcds...` |

## Outputs

| Name | Description | Example |
| ---- | ----------- | ------- |
| `ref-type` | Type of the given ref, if it's valid | One of `branch`, `tag`, `commit` |
| `sha` | Full SHA of the given ref, if it's valid | `03d17eebfda5b873c576bad1dd77acdb8` |

## Examples

### Checkout Example

```yaml
# ...
jobs:
  get-ref:
    runs-on: ubuntu-latest
    env:
     ref: main
    steps:
    - id: resolve-ref
      uses: access-nri/actions/.github/actions/get-git-ref-info@main
      with:
        repository: ACCESS-NRI/actions@main
        ref: ${{ env.ref }}

    - run: echo "Ref ${{ env.ref }} is a ${{ steps.resolve-ref.outputs.ref-type }} with SHA ${{ steps.resolve-ref.outputs.sha }}"
```

### Local Example

> [!NOTE]
> In order for local repositories to be interrogated correctly, they must have the full history. This is not the case by default for repositories checked out via `actions/checkout`. Specify `fetch-depth: 0` and `fetch-tags: true` in this case.

```yaml
# ...
jobs:
  get-ref:
    runs-on: ubuntu-latest
    env:
     ref: main
     repo-path: /opt/some/path
    steps:
    - uses: actions/checkout@v5
      with:
        path: ${{ env.repo-path }}
        fetch-depth: 0
        fetch-tags: true

    - id: resolve-ref
      uses: access-nri/actions/.github/actions/get-git-ref-info@main
      with:
        repository-path: ${{ env.repo-path }}
        ref: ${{ env.ref }}

    - run: echo "Ref ${{ env.ref }} is a ${{ steps.resolve-ref.outputs.ref-type }} with SHA ${{ steps.resolve-ref.outputs.sha }}"
```
