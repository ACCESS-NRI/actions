# Version Check Action

Perform a version sanity check based on versioning scheme and previous version.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| `versioning-scheme` | `string` | The type of [versioning scheme](#supported-versioning-schemes) used, not including any prefix. | YES | N/A | `semver`, `semver-major-minor` |
| `version` | `string` | The version string to perform the sanity check on, including any prefix. If not provided and this action was run on a tag push, `version` is set to `${{github.ref_name}}`. | NO | `${{github.ref_name}}` | `1.2.5`, `v1.0`  |
| `previous-version` | `string` | The previous version used for version sanity check, including any prefix. If not provided, `previous-version` is set through the [latest tag](#latest-tag). | NO | [Latest tag](#latest-tag) | `1.2.5`, `v1.0`  |
| `prefix` | `string` | Any prefix to the `versioning-scheme` included in the `version` string. | NO | N/A | `v`, `dev`  |

### Supported Versioning Schemes

| Scheme | Format | Checks performed | Description |
| ------ | ------- | ---------------- | ----------- |
| `semver` | `MAJOR.MINOR.PATCH` | [`semver` scheme checks](#semver-scheme) | [Semantic Versioning](https://semver.org/) scheme with `MAJOR`, `MINOR` and `PATCH` versions |
| `semver-major-minor` | `MAJOR.MINOR` | [`semver-major-minor` scheme checks](#semver-major-minor-scheme) | A variation of [Semantic Versioning](https://semver.org/) scheme, with only `MAJOR` and `MINOR` versions |

If a `prefix` is present, the versioning scheme is the part of the `version` string without `prefix`.


### Sanity checks performed
#### `semver` scheme
1. `version` matches the `semver` format, including any `prefix`: `<prefix>[0-9]+.[0-9]+.[0-9]+`
2. There are no jumps from `previous-version` to `version` and `version > previous-version`. 
   For this check, `prefix` is not considered and gets stripped from `version` and `previous-version` before performing the check.

#### `semver-major-minor` scheme
1. `version` matches the `semver-major-minor` format, including any `prefix`: `<prefix>[0-9]+.[0-9]+`
2. There are no jumps from `previous-version` to `version` and `version > previous-version`.
   For this check, `prefix` is not considered and gets stripped from `version` and `previous-version` before performing the check.


### Latest tag
If `previous-version` is not provided, it is inferred as follows:
- If `version` **is provided**, `previous-version` is set to the **latest repository tag**, ordered according to the selected `versioning-scheme`.
- If `version` **is not provided** (and is therefore inferred from `${{github.ref_name}}`), `previous-version` is set to the **second-latest repository tag**, ordered according to the selected `versioning-scheme`.

If no suitable `previous-version` can be determined, no sanity check against a previous version is performed.

> [!NOTE]
> If a `prefix` is provided, it is still considered when inferring the `previous-version`.

## Examples

<details>
<summary><b>Check version inferred from a tag push</b></summary>

```yaml
# ...
jobs:
# ...
  version-sanity-check:
    name: Version sanity check
    runs-on: ubuntu-latest
    steps:
      - name: Check version
        uses: access-nri/actions/.github/actions/version-check@main
        with: 
          versioning-scheme: semver
```
</details>

<details>
<summary><b>Check version with <code>vMAJOR.MINOR</code> format</b></summary>

```yaml
# ...
jobs:
# ...
  version-sanity-check:
    name: Version sanity check
    runs-on: ubuntu-latest
    steps:
      - name: Check version
        uses: access-nri/actions/.github/actions/version-check@main
        with: 
          versioning-scheme: semver-major-minor
          version: v1.2
          prefix: v
```
</details>

<details>
<summary><b>Check version computed in previous steps or jobs</b></summary>

```yaml
# ...
jobs:
# ...
  get-versions:
    name: Version sanity check
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.get-version.outputs.version }}
      previous-version: ${{ steps.get-previous-version.outputs.previous-version }}
    steps:
      - name: Get version
        id: get-version
        run: |
          ...
          echo "version=..." >> $GITHUB_OUTPUT
      - name: Get previous version
        id: get-previous-version
        run: |
          ...
          echo "previous-version=..." >> $GITHUB_OUTPUT

  version-sanity-check:
    name: Version sanity check
    runs-on: ubuntu-latest
    needs: [get-versions]
    steps:
      - name: Check version
        uses: access-nri/actions/.github/actions/version-check@main
        with: 
          versioning-scheme: semver
          version: ${{ needs.get-versions.outputs.version }}
          previous-version: ${{ needs.get-versions.outputs.previous-version }}
```
</details>