# Bump Version Action

This action bumps a version string, given a versioning scheme. The bump can be configured (such as a major or minor bump) in the action.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `version` | `string` | The version string that will be bumped | `true` | N/A | `"2024.02.1"`, `"12.23"` |
| `versioning-scheme` | `string` | The type of versioning scheme used. [See below for explanation](#versioning-schemes) |`true` | N/A | `"calver-minor"` |
| `bump-type` | `string` | The type of bump. This changes depending on the `versioning-scheme`. [See below for explanation](#versioning-schemes) | `true` | N/A | "minor" |

### Versioning Schemes

We currently support these versioning schemes. See the expanded section on Bumps after this table for more information.

| Scheme | Example | Available Bumps | Description |
| ------ | ------- | --------------- | ----------- |
| `calver-minor` | `YYYY.0M.00MINOR` | `major`, `minor`, `current` | A variation of [Calender Versioning](https://calver.org/) where the Day field is instead a double-padded `MINOR` version number |
| `semver-major-minor` | `MAJOR.MINOR` | `major`, `minor` | A variation of [Semantic Versioning](https://semver.org/) which only deals with `MAJOR` and `MINOR` versions |

#### `calver-minor` Bumps

| Bump | Effect | Example |
| ---- | ------ | ------- |
| `major` | `YYYY.0M.00MINOR` -> `YYYY.(0M+1).000` | `2024.02.003` -> `2024.03.000`, `2024.12.000` -> `2025.01.000` |
| `minor` | `YYYY.0M.00MINOR` -> `YYYY.0M.00(MINOR+1)` | `2024.01.009` -> `2024.01.010` |
| `current` | `YYYY.0M.00MINOR` -> `(CURRENT_YEAR).(CURRENT_MONTH).000` | `2020.04.001` -> `2024.01.000` (the current date) |

#### `semver-major-minor` Bumps

| Bump | Effect | Example |
| ---- | ------ | ------- |
| `major` | `MAJOR.MINOR` -> `(MAJOR+1).0` | `12.3` -> `13.0` |
| `minor` | `MAJOR.MINOR` -> `MAJOR.(MINOR+1)` | `12.3` -> `12.4` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| `before` | `string` | Version before being bumped | `"2023.02.001"` |
| `after` | `string` | Version after being bumped | `"2023.02.002"` |
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
          version: 2023.12.001
          versioning-scheme: calver-minor
          bump-type: minor

      - run: echo "After doing a ${{ steps.cal-bump.outputs.type }} bump on version ${{ steps.cal-bump.outputs.before }}, we now have version ${{ steps.cal-bump.outputs.after}}!"  # After doing a minor bump on version 2023.12.001, we now have version 2023.12.002!

      - id: semver-bump
        uses: access-nri/actions/.github/actions/bump-version@main
        with:
          version: 12.23
          versioning-scheme: semver-major-minor
          bump-type: major

      - run: echo "This version went from ${{ steps.semver-bump.outputs.before }} to ${{ steps.semver-bump.outputs.after }}!"  # This version went from 12.23 to 13.0!
```
