# PR Comment Action

This action comments on a given PR as `github-actions[bot]`.

> ![NOTE]
> Requires `permissions.issues:write` or `permissions.pull-requests:write` depending on what you want commented

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `comment` | `string` | The body of the comment, in GitHub Markdown format | `true` | N/A | `"Hello this is a comment from PR 20"` |
| `pr` | `number` | The Pull Request Number that will be commented on | `false` | `github.event.[pull_request\|issue].number` | `20` |
| `edit-last` | `boolean` | Edit the last comment by `github-actions[bot]` instead of creating a new one | `false` | `false` | `true` |
| `token` | `string` | A GitHub Token/PAT that allows commenting on the given PR | `false` | `github.token` | `gha_pat_abcds...` |

outputs:
  comment-link:
    description:
    value: ${{ steps.comment.outputs.link }}

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `comment-link` | `string` | A link to the added comment | `https://github.com/OWNER/REPO/pull/PR#issuecomment-ID` |

## Examples

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
    - uses: access-nri/actions/.github/actions/pr-comment@main
      with:
        comment: |
          Wow, a comment on PR `${{ github.event.pull_request.number }}`!
          With multilines!
```

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
    - uses: access-nri/actions/.github/actions/pr-comment@main
      with:
        pr: 123
        token: ${{ secrets.MY_PAT }}
        edit-last: true
        comment: |
          Wow, a comment on PR `123`!
          With multilines!
```
