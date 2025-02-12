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
| `message` | `string` | The comment body. Supports Markdown format. | NO | N/A | `Hello this is a comment from PR 20` |
| `number` | `number` | The Pull Request / Issue Number that will be commented on.<br>If `number` is not provided, the following rules will apply:<br>- if `url` is provided, `number` will be inferred from the url;<br>- if `url` is not provided and the action is called within a `pull_request` or `issue` event, `number` will default to the PR/Issue associated with the event;<br>- in any other case, an error will be thrown.<br>Cannot be used together with `url`.| NO | N/A | `20` |
| `label` | `string` | A label to identify the comment (useful for editing or deleting a specific comment). If `label` is provided together with `comment-id` or a `url` containing the COMMENT_ID portion, `label` will be ignored. | NO | Comment date in the format `YYYY-MM-DDTHH:MM:SS` | `my-new-label` |
| `comment-id` | `number` | The ID of the comment to edit or delete. If not provided, a new comment is created. If `comment-id` is provided together with `label`, `label` will be ignored. Cannot be used together with a `url` containing the COMMENT_ID portion. | NO | N/A | `2650933115` |      
| `repo` | `string` | The repository to comment on, in the format `REPOSITORY_OWNER/REPOSITORY_NAME`. It defaults to the repository where the action is running. Cannot be used together with `url`. | NO | current repo | `ACCESS-NRI/actions` |
| `url` | `string` | The comment url in the format `https://github.com/REPOSITORY_OWNER/REPOSITORY_NAME/issues/NUMBER#issuecomment-COMMENT_ID`, or the PR/issue url in the form `https://github.com/REPOSITORY_OWNER/REPOSITORY_NAME/issues/NUMBER`. If a comment `url` (containing the COMMENT_ID portion) is provided together with `label`, `label` will be ignored. A comment `url` (containing the COMMENT_ID portion) cannot be used together with `comment-id`. Cannot be used together with `number` or `repo`. | NO | N/A | `https://github.com/ACCESS-NRI/actions/issues/13#issuecomment-2485204240` |
| `delete` | `boolean` | If set to `true`, it deletes the comment. | NO | `false` | `true` |
| `token` | `string` | A GitHub Token/PAT that allows commenting on the given PR/Issue. Defaults to `github.token`. | NO | `github.token` | `gha_pat_abcds...` |

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
    - uses: access-nri/actions/.github/actions/comment@main
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
    - uses: access-nri/actions/.github/actions/comment@main
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
    - uses: access-nri/actions/.github/actions/comment@main
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
    - uses: access-nri/actions/.github/actions/comment@main
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
    - uses: access-nri/actions/.github/actions/comment@main
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

<details>
<summary><b>Add a comment to PR #115 of the repo ACCESS-NRI/actions using its url</b></summary>

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
    - uses: access-nri/actions/.github/actions/comment@main
      with:
        url: https://github.com/ACCESS-NRI/actions/pull/115
        token: ${{secrets.PR_WRITE_TOKEN}}
        message: |
          Wow, a comment on PR 115 of the ACCESS-NRI/actions repo!
          Using its URL!!!
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

<details>
<summary><b>Edit a specific PR/issue comment using its url</b></summary>

```yaml
# ...
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
    - uses: access-nri/actions/.github/actions/comment@main
      with:
        url: https://github.com/ACCESS-NRI/actions/pull/115#issuecomment-2466989248
        token: ${{secrets.PR_WRITE_TOKEN}}
        message: |
          Wow, I'm editing comment 2466989248 on PR 115 of the ACCESS-NRI/actions repo!
          I'm using its URL!!!
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