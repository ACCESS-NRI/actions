# validate-json workflow

This workflow validates JSON (`*.json`) against a given schema (`*.schema.json`), provided they are in the same directory. 

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| src | string | A directory that contains both the `*.json` and `*.schema.json` files | false | '.' | './config' |

## Outputs

There are no explicit outputs for this workflow. 

However, it does return job failure/success based on the result of the schema validations. 

## Examples

```yaml
jobs:
  validate:
    uses: access-nri/actions/.github/workflows/validate-json@main
    with:
      src: 'config'
```
