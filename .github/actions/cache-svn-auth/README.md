# SVN Authentication Caching Action

This action uses GnuPG (GPG) passphrase caching to cache [Subversion (SVN)](https://svnbook.red-bean.com/en/1.7/svn-book.pdf)'s authentication password, for a specific SVN realm.<br>

> [!IMPORTANT]
> GitHub Runners are ephemeral, so SVN credential caching only persists within the same job. To ensure SVN access works as expected, this action must be executed in a step within the same job where subsequent SVN commands are run.
> If SVN access is required across multiple jobs, you’ll need to invoke this action separately in each of those jobs.

## Inputs

| Name | Type | Description | Required | Example |
| ---- | ---- | ----------- | -------- | ------- |
| `username` | `string` | The SVN username. | YES | `myuser` |
| `password` | `string` | The SVN password. | YES | `mypassword` |
| `realm` | `string` | The SVN realm. Often in the form `<https://svn.domain.com:443> Description of realm`.<br>To find the correct realm for your SVN repo, you can read the `Authentication realm:` field when being prompted for authentication after running a command like `svn info https://your.svn.server.com/path/to/repo`. | YES | `<https://awesome.svn.repo.com:443> My awesome SVN repo` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `svn-cache-id` | `string` | The SVN cache-id used for the authentication. This is obtained by computing the md5 hash of the SVN `realm` input. | `2be6a67d04b1c8c6d879daafa52fd762` |

## Usage 
This action is useful to cache SVN credentials with GPG, so SVN commands can be issued in `--non-interactive` mode without needing manual authentication.

## Examples

<details>
<summary><b>Cache SVN credentials for the MetOffice SVN repo</b></summary>

```yaml
# ...
jobs:
# ...
  cache-svn-auth:
    name: Cache SVN Authentication for MetOffice Repo
    runs-on: ubuntu-latest
    steps:
      - name: Cache SVN authentication
        uses: access-nri/actions/.github/actions/cache-svn-auth@main
        with: 
          username: '${{ secrets.MOSRS_USERNAME }}'
          password: '${{ secrets.MOSRS_PASSWORD }}'
          realm: '<https://code.metoffice.gov.uk:443> Met Office Code'
    
      - name: Run svn command
        run: |
          svn info --non-interactive https://code.metoffice.gov.uk/svn/utils/shumlib/trunk/
```

:bulb:**TIP**<br>
For the workflow above to work, you need to set the `MOSRS_USERNAME` and `MOSRS_PASSWORD` [GitHub secrets](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets) in the repository that uses the workflow.

</details>
