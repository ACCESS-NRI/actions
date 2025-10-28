# actions

Custom GitHub Actions and Workflows for use across the ACCESS-NRI.

## Actions

* [setup-ssh](.github/actions/setup-ssh/README.md): Sets up private keys and `~/.ssh/known_hosts`.
* [docker-build-push](.github/actions/docker-build-push/README.md): Builds and pushes a Docker image.
* [bump-version](.github/actions/bump-version/README.md): Bumps a version string given a versioning scheme.
* [react-to-comment](.github/actions/react-to-comment/README.md): Lets `github-actions[bot]` react to a comment. Usually useful to let users know there is some action in progress.
* [pr-comment](.github/actions/pr-comment/README.md): _Outdated action. Please use the updated [comment](.github/actions/comment/README.md) action instead_. Convenience action for letting `github-actions[bot]` comment on a given PR.
* [commenter-permissions README.md](.github/actions/commenter-permission-check/README.md): Determine whether a commenter on a PR has write permissions on the repository.
* [comment](.github/actions/comment/README.md): Add, modify or delete a comment on a given issue or PR.
* [cache-svn-auth](.github/actions/cache-svn-auth/README.md): Cache Subversion (SVN)'s authentication password.
* [get-git-ref-info](.github/actions/get-git-ref-info/README.md): Determine if a given git ref is a branch, tag or SHA, if it exists.

## Workflows

* [validate-json.yml](.github/workflows/README.md#validate-json-workflow): Runs a matrix job that validates all the `*.json`/`*.schema.json`.
* [publish-python-package.yml](.github/workflows/README.md#publish-python-package-workflow): Builds and publishes a pure python package distribution on Pypi and Anaconda.org.
