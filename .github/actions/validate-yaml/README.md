# Validate YAML Action

This action validates a `.yaml` file given a `.json` spec, using `ajv`.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `schema-location` | `string` | Path on the runner to the schema to use for validation | `true` | N/A | `'./path/to/schema.json'`, `'/opt/schema.json'` |
| `yaml-location` | `string` | Path on the runner to the .yaml file to be validated | `true` | N/A | `'../my.yaml'`, `'/opt/some.yaml'` |
| `strict-schema-validation` | `string` | Whether to validate the schema itself along with the yaml | `false` | `false` | `true`, `false` or `log` |
| `schema-spec` | `string` | JSON schema specification to use, to be passed to `ajv` | `false` | `'draft07'` | `'draft2012-09'` (for others, see `ajv` documentation) |

## Outputs

This action has no outputs. The success of failure of the action denotes the success or failure of `ajv` to validate the data given the spec.

## Examples

```yaml
# ...
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - id: valid
        uses: access-nri/actions/.github/actions/validate-yaml@main
        with:
          yaml-location: ./config/my.yaml
          schema-location: ./schema/my.schema.json

      - if: always()
        run: echo "::notice::The validation result was ${{ steps.valid.outcome }}!"
```
