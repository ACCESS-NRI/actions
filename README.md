# actions

Custom GitHub Actions for use across the ACCESS-NRI.

## Actions

* `setup-ssh`: Sets up private keys and ~/.ssh/known_hosts: [setup-ssh README.md](.github/actions/setup-ssh/README.md). 
* `docker-build-push`: Builds and pushes a docker image: [docker-build-push README.md](.github/actions/docker-build-push/README.md).

## Workflows

* `validate-json.yml`: Runs a matrix job that validates all the `*.json`/`*.schema.json`: [validate-json README.md](.github/workflows/README.md).
