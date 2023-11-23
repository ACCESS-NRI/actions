# Docker Build and Push Action

This action builds and pushes a Docker image to a container repository.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| container-registry | string | Name of the container registry to push the image to | true | N/A | 'ghcr.io' |
| image-name | string | Name of the image to push (-t) | true | N/A | 'example-image:latest' |
| dockerfile-directory | string | Location of the Dockerfile | false | '.' | 'containers/' |
| dockerfile-name | string | Name of the Dockerfile that will be built | false | 'Dockerfile' | 'Dockerfile.prod' |
| build-args | string | List of Docker build arguments (--build-arg) | false | '' | 'VAR=value' |
| build-secrets | string | List of docker build secrets (--secret) | false | '' | 'KEY=abcd' |
| target | string | Build target for the Dockerfile | false | '' | 'ci' |
| push | string | Whether the built image should be pushed to the container repository | false | 'true' | 'true' |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| image-url| string | Full path of the built image | 'ghcr.io/access-nri/image:tag' |

## Example

```yaml
# ...
- uses: access-nri/actions/.github/actions/docker-build-push@main
  with:
    container-repository: ghcr.io
    image-name: my-image:latest
    build-args: |
      VAR1=value1
      VAR2=value2
    build-secrets: |
      SECRET1=${{ secrets.SECRET1 }}
      SECRET2=${{ secrets.SECRET2 }}
```
