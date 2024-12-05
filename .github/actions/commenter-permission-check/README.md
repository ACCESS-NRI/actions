# PR Commenter Permissions Check Action

Action that determines whether a commenter on a PR has certain permissions on the repository

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `minimum-permission` | `string` | Minimum level of permission required on the given repository. One of: `none` `read` `write` `admin` | `true` | N/A | `admin` |
| `exit-if-permission-denied` | `boolean` | Exit when commenter permission is denied | `true` | `false` | `true` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `has-permission` | `string` | If the commenter has at least the required permission level | `true` |

## Examples

### Simple

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
    - id: commenter
      uses: access-nri/actions/.github/actions/commenter-permission-check@main
      with:
        minimum-permission: write

    - if: steps.commenter.outputs.has-permission == 'true'
      run: echo "${{ github.event.comment.user.login }} has permission to do this step"
```

### More Complex

```yaml
# ...
jobs:
  commenter:
    runs-on: ubuntu-latest
    steps:
    - uses: access-nri/actions/.github/actions/commenter-permission-check@main
      with:
        minimum-permission: admin
        exit-if-permission-denied: true
```
