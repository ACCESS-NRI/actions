# Comment Action

This action adds/edits/deletes a comment on a given PR/issue.<br>
If no token is provided, the comment will be issued as  `github-actions[bot]`.<br>
If a token is provided, the comment will be issued as the owner of the token (or its creator in the case of a GitHub-Organization-owned PAT).

> [!IMPORTANT]
> Minimum required permissions for the token:
> - `metadata: read` (set by default for the `github.token`)
> - `contents: read` (set by default for the `github.token`)
> - `issues: write` or `pull-requests: write` (depending whether you are commenting an issue or a PR)

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `message` | `string` | The comment body. Supports Markdown format. | `true` | N/A | `Hello this is a comment from PR 20` |
| `number` | `number` | The Pull Request / Issue Number that will be commented on. If the action is called within a `pull_request` or `issue` event, it defaults to the PR/Issue associated with the event. If the action is NOT called within a `pull_request` or `issue` event and `number` is not provided, an error will be thrown. | `false` | N/A | `20` |
| `label` | `string` | A label to identify the comment (useful for editing or deleting a specific comment). If  `label` is provided together with `comment-id`, `label` will be ignored. | `false` | `default-label` | `my-new-label` |
| `comment-id` | `number` | The ID of the comment to edit or delete. If not provided, a new comment is created. If `comment-id` is provided together with `label`, `label` will be ignored. | `false` | N/A | `2650933115` |      
| `repo` | `string` | The repository to comment on, in the format: <REPOSITORY_OWNER>/<REPOSITORY_NAME>. It defaults to the repository where the action is running. | `false` | current repo | `ACCESS-NRI/actions` |
| `delete` | `boolean` | If set to `true`, it deletes the comment. | `false` | `false` | `true` |
| `token` | `string` | A GitHub Token/PAT that allows commenting on the given PR/Issue. Defaults to `github.token`. | `false` | `github.token` | `gha_pat_abcds...` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `comment-url` | `string` | The URL of the added/edited comment. Returns nothing if `delete` is set to `true`. | `https://github.com/OWNER/REPO/pull/PR#issuecomment-ID` |

## Examples

<details>
<summary><b>Add a comment to a PR in a workflow triggered by the same PR</b></summary>

```yaml
# ...
on: pull-request
jobs:
  comment:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
    - uses: access-nri/actions/.github/actions/pr-comment@main
      with:
        message: |
          Wow, a comment on PR `${{ github.event.pull_request.number }}`!
          With multilines!
```
</details>

<details>
<summary><b>Add a comment to the PR #34 within the same repo</b></summary>

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
        number: 34
        message: |
          Wow, a comment on PR #34!
          With multilines!
```
</details>

<details>
<summary><b>Add/edit a comment labelled as 'my-label' to the PR #34 within the same repo</b></summary>

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
        number: 34
        label: my-label
        message: |
          Wow, a comment on PR #34!
          This has the hidden label: 'my-label'.
```
<b>IMPORTANT</b><br>
If a comment with the provided label is already present, that comment is edited. Otherwise, a new comment with the provided label is added.

</details>


<details>
<summary><b>Delete the comment labelled as 'my-label' to the PR #34 within the same repo</b></summary>

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
        number: 34
        label: my-label
        delete: true
```

</details>

<details>
<summary><b>Add a comment to the PR #115 of the repo ACCESS-NRI/actions</b></summary>

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
    - uses: access-nri/actions/.github/actions/pr-comment@main
      with:
        number: 115
        repo: ACCESS-NRI/actions
        token: ${{secrets.PR_WRITE_TOKEN}}
        message: |
          Wow, a comment on PR 115 of the ACCESS-NRI/actions repo!
```
<b>IMPORTANT</b><br>
The default `github.token` does not have access to other repos.<br>
To add/edit/delete a comment in a different repo, an external token needs to be provided. This could be, for example a [PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).<br>
In the example above, the minimum token's permissions for the commented repo need to be:
<ul>
    <li><code>metadata: read</code></li>
    <li><code>contents: read</code></li>
    <li><code>pull-requests: write</code></li>
</ul>
</details>