# actions

Custom GitHub Actions for use across the ACCESS-NRI.

## Actions

* `setup-ssh`: Sets up private keys and ~/.ssh/known_hosts: [setup-ssh README.md](.github/actions/setup-ssh/README.md).
* `docker-build-push`: Builds and pushes a docker image: [docker-build-push README.md](.github/actions/docker-build-push/README.md).
* `bump-version`: Bumps a version string given a versioning scheme: [bump-version README.md](.github/actions/bump-version/README.md).
* `react-to-comment`: Lets `github-actions[bot]` react to a comment. Usually useful to let users know there is some action in progress: [react-to-comment](.github/actions/react-to-comment/README.md).
* `pr-comment`: Convenience action for letting `github-actions[bot]` comment on a given PR: [pr-comment](.github/actions/pr-comment/README.md).
* `setup-git-credentials`: Action that packages up the setting of common git config and verification keys: [setup-git-credentials](.github/actions/setup-git-credentials/README.md).

## Workflows

* `validate-json.yml`: Runs a matrix job that validates all the `*.json`/`*.schema.json`: [validate-json README.md](.github/workflows/README.md).
