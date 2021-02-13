# fossology-scan
GitHub action to perform fossology scan

This action allows you to perform license, copyright and keyword scanning on
your repo.

Scanning can either be done in `diff` mode where the diff of base commit and
head commit is scanned. This is much faster and preferred for PR scanns.
The other mode of scan is `repo` mode where the whole repository is scanned for
licenses, copyrights and keywords.

The action currently has following scanners:
- `nomos`: It is a very precise license scanner
- `ojo`: Looks for `SPDX-License-Identifier` text
- `copyright`: Scanns for copyright, very low false-negative
- `keyword`: Scanns for harmful keywords

You can checkout more about the scanning in
[this wiki](https://github.com/fossology/fossology/wiki/FOSSology-scanners-in-CI).

## Inputs

### `scan-mode`
It tells wheteher to perform a `diff` scan or a `repo` scan. Defaults to `diff`.

### `scanners`
Space separated list of scanners to invoke. Allowd values:
- `nomos`
- `ojo`
- `copyright`
- `keyword`

Defaults to `nomos ojo copyright keyword`.

## Example

### Pull request scans

```yaml
name: License scan on PR

on: [pull_request]

jobs:
  compliance_check:
    runs-on: ubuntu-latest
    name: Perform license scan
    steps:
    - name: Checkout
      uses: actions/checkout@v2
    - name: License check
      id: compliance
      uses: GMishx/fossology-scan@v1
      with:
        scan-mode: 'diff'
        scanners: 'nomos ojo'
    - name: 'Upload report'
      uses: actions/upload-artifact@v2
      if: ${{ failure() }}
      with:
        name: Scan results
        path: results
```

### Tag scans

```yaml
name: License scan on release

on:
  release:
    types: [published]

jobs:
  compliance_check:
    runs-on: ubuntu-latest
    name: Perform license scan
    steps:
    - name: Checkout
      uses: actions/checkout@v2
    - name: License check
      id: compliance
      uses: GMishx/fossology-scan@v1
      with:
        scan-mode: 'repo'
        scanners: 'nomos ojo'
    - name: 'Upload report'
      uses: actions/upload-artifact@v2
      if: ${{ failure() }}
      with:
        name: Scan results
        path: results
```
