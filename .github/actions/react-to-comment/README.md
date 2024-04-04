# React To Comment Action

This action adds a reaction to a given comment, or alternatively the comment referred to by the `on.issue_comment` trigger. Reactions are defined in [GitHub's Emoji syntax](https://docs.github.com/en/rest/reactions/reactions?apiVersion=2022-11-28#create-reaction-for-an-issue-comment).

> [!NOTE]
> Ensure the job has the correct permissions for the type of comment reaction (either `permissions.issues:write` or `permissions.pull-request:write`).

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `reaction` | `string` | The reaction to the given comment in GitHubs Emoji syntax | `true` | `"+1"` | `"rocket"` |
| `repository` | `string` | The GitHub `OWNER/REPO` combination | `false` | `${{ github.repository }}` | `"ACCESS-NRI/actions"` |
| `comment-id` | `number` | The `COMMENT_ID` of the issue/pull request comment | `false` | `${{ github.event.comment.id }}` | `1234567` |
| `token` | `string` | Appropriate Github Token/PAT to react to the given comment | `true` | N/A | `${{ secrets.GITHUB_TOKEN }}` |

## Example

```yaml
# ...
- uses: access-nri/actions/.github/actions/react-to-comment@main
  with:
    reaction: rocket
    token: ${{ secrets.GITHUB_TOKEN }}
```
