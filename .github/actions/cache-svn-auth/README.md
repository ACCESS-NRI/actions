# SVN Authentication Caching Action

This action uses GnuPG (GPG) passphrase caching to cache [Subversion (SVN)](https://svnbook.red-bean.com/en/1.7/svn-book.pdf) authentication password, for a specific svn realm.<br>

## Inputs

| Name | Type | Description | Required | Example |
| ---- | ---- | ----------- | -------- | ------- |
| `username` | `string` | The SVN username. | YES | `myuser` |
| `password` | `string` | The SVN password. | YES | `mypassword` |
| `realm` | `string` | A svn realm string, often in the form `<https://svn.domain.com:443> Description of realm` | YES | `<https://awesome.svn.repo.com:443> My awesome SVN repo` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `svn-cache-id` | `string` | The svn cache-id used for the authentication. This is obtained by computing the md5 hash of the svn `realm` input. | `2be6a67d04b1c8c6d879daafa52fd762` |

## Usage 
This action is useful to cache SVN credentials with GPG, so svn commands can be issued in `--non-interactive` mode without needing manual authentication.

## Examples

<details>
<summary><b>Cache SVN credentials for further steps</b></summary>

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