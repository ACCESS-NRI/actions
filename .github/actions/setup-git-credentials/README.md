# Setup Git Credentials Action

Action that sets up various common git config and commit-signing GPG keys in a job

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `user-name` | `string` | Git `user.name` config value | `true` | N/A | `"John Smith"` |
| `user-email` | `string` | Git `user.email` config value | `true` | N/A | `"email@gmail.com"` |
| `global-config` | `boolean` | Write to global `~/.gitconfig` rather than `.git/config` for this job. Using `false` requires a git repository already checked out. | `false` | `true` | `true` |
| `gpg-signing-key` | `string` | GPG signing key for verified commits | `false` | N/A | `"3AA5C34371567BD2"` |

## Outputs

There are no outputs from this action.

## Example

Setting global config with signing key:

```yaml
steps:
  - uses: access-nri/actions/.github/actions/setup-git-credentials@main
    with:
      user-name: Some One
      user-email: some-one@email.com
      gpg-signing-key: ${{ secrets.COMMIT_SIGNING_KEY }}
```

Setting repo config without signing key:

```yaml
steps:
  - uses: actions/checkout@v4

  - uses: access-nri/actions/.github/actions/setup-git-credentials@main
    with:
      user-name: Some One
      user-email: some-one@email.com
      global-config: false
```
