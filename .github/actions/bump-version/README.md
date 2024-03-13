# Bump Version Action

This action bumps a version string, given a versioning scheme. The bump can be configured (such as a major or minor bump) in the action.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `version` | `string` | The version string that will be bumped | `true` | N/A | `"2024.02.1"`, `"12.23"` |
| `versioning-scheme` | `string` | The type of versioning scheme used. [See below for explanation](#versioning-schemes) |`true` | N/A | `"calver-minor"` |
| `bump-type` | `string` | The type of bump. This changes depending on the `versioning-scheme`. [See below for explanation](#versioning-schemes) | `true` | N/A | "minor" |

### Versioning Schemes

We currently support these versioning schemes:

| Scheme | Example | Available Bumps | Example Bumps | Description |
| ------ | ------- | --------------- | ------------- | ----------- |
| `calver-minor` | `YYYY.0M.MINOR` | `major` bumps to `CURRENT_YEAR.CURRENT_MONTH.0`, `minor` bumps to `YYYY.0M.(MINOR+1)` | `major` (`2023.02.3` -> `2024.01.0`, AKA updated to current year and month). `minor` (`2023.02.3` -> `2023.02.4`) | A variation of [Calender Versioning](https://calver.org/) where the Day field is instead a `MINOR` version number |
| `semver-major-minor` | `MAJOR.MINOR` | `major` bumps to `(MAJOR+1).0`, `minor` bumps to `MAJOR.(MINOR+1)` | `major` (`12.2` -> `13.0`), `minor` (`12.2` -> `12.3`) | A variation of [Semantic Versioning](https://semver.org/) which only deals with `MAJOR` and `MINOR` versions |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `before` | `string` | Version before being bumped | `"2023.02.1"` |
| `after` | `string` | Version after being bumped | `"2023.02.2"` |
| `type` | `string` | Type of bump | `"minor"` |

## Usage

```yaml
#...
jobs:
  bump:
    runs-on: ubuntu-latest
    steps:
      - id: cal-bump
        uses: access-nri/actions/.github/actions/bump-version@main
        with:
          version: 2023.12.1
          versioning-scheme: calver-minor
          bump-type: minor

      - run: echo "After doing a ${{ steps.cal-bump.outputs.type }} bump on version ${{ steps.cal-bump.outputs.before }}, we now have version ${{ steps.cal-bump.outputs.after}}!"  # After doing a minor bump on version 2023.12.1, we now have version 2023.12.2!

      - id: semver-bump
        uses: access-nri/actions/.github/actions/bump-version@main
        with:
          version: 12.23
          versioning-scheme: semver-major-minor
          bump-type: major

      - run: echo "This version went from ${{ steps.semver-bump.outputs.before }} to ${{ steps.semver-bump.outputs.after }}!"  # This version went from 12.23 to 13.0!
```
