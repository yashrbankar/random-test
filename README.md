# Qualys QScanner GitHub Action

Scan container images and code repositories with [Qualys QScanner](https://www.qualys.com/downloads/qscanner/) and publish findings to the GitHub **Security** tab (code scanning, SARIF).

## Usage

```yaml
permissions:
  contents: read
  security-events: write
steps:
  - uses: actions/checkout@v4
  - uses: Qualys/qscanner-action@v1
    with:
      access-token: ${{ secrets.QUALYS_ACCESS_TOKEN }}
      pod: ${{ vars.QUALYS_POD }}      # US1, US2, US3, EU1, CA1 ...
      qscanner-version: v4.6.0
      scan-command: image              # image | code | image-archive
      target: myorg/myapp:latest
      mode: evaluate-policy
      policy-tags: dev,all
```

Full workflows: [examples/qscanner.yml](examples/qscanner.yml).

## Setup

1. Create a Qualys access token and add it as secret `QUALYS_ACCESS_TOKEN` (repo or organization level).
2. Add your POD as variable `QUALYS_POD`.
3. Grant `security-events: write` to the job (needed for SARIF upload; code scanning requires GitHub Advanced Security on private repos).

## Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `access-token` | yes | | Qualys access token |
| `pod` | yes | | Qualys POD |
| `qscanner-version` | yes | | QScanner version, e.g. `v4.6.0` |
| `target` | yes | | Image name, path or clone URL |
| `scan-command` | no | `image` | QScanner command |
| `scan-types` | no | | e.g. `os,sca,secret` |
| `mode` | no | `get-report` | `inventory-only`, `scan-only`, `get-report`, `evaluate-policy` |
| `policy-tags` | no | | Policy tags for `evaluate-policy` |
| `report-format` | no | `table,sarif` | Report formats |
| `output-dir` | no | `$RUNNER_TEMP/qscanner-output` | Report directory |
| `upload-sarif` | no | `true` | Upload SARIF to code scanning |
| `sarif-category` | no | `qualys-qscanner` | SARIF category (use distinct values per scan) |
| `upload-artifact` | no | `false` | Upload output dir as artifact |
| `fail-on-policy-deny` | no | `true` | Fail on policy DENY (exit code 42) |
| `extra-args` | no | | Extra global QScanner flags |

## Outputs

`exit-code`, `sarif-file`, `output-dir`.

## Notes

- Linux runners only.
- Reports are always uploaded before the step fails on policy DENY.
