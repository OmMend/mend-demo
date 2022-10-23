# Introduction

As we know a software vulnerability is a flaw or weakness present in the software or operating system and therefore as a best practice we should have a solution at place to scan the docker images for vulnerability issues. This GitHub is going to utilize an Mend's CLI scanner to achieve this. 

Mend CLI detects vulnerabilities of OS packages (Alpine, RHEL, CentOS, etc.) and application dependencies (Bundler, Composer, npm, yarn etc.). Mend CLI is easy to use. Just install the binary and you're ready to scan. All you need to do for scanning is to specify an image name of container.

We present the following use cases with GitHub actions for the sake of helping DevOps, Cloud & Dev teams to integrate Mend CLI in their CI workflow to scan the local container images prior to pushing them to registry.

## Using Sarif Format

```yaml

name: "Scan Mend Sarif file"
# Run workflow each time code is pushed or a pull request is created on your repository.
on:
  push:
  pull_request:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # This step checks out a copy of your repository.
    - name: Checkout repository
      uses: actions/checkout@v2
    - name: Upload SARIF file
      uses: github/codeql-action/upload-sarif@v1
      with:
        # Path to SARIF file relative to the root of the repository
        sarif_file: mend-ubuntu.sarif
        
```

## Using Table Format

```yaml



name: "Scan Container Image"
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
# This step checks out a copy of your repository.
    - name: Checkout repository
      uses: actions/checkout@v2
    - name: Scan image with Mend CLI
      shell: bash
      env:
        USER_KEY: ${{ secrets.USER_KEY }}
        API_KEY: ${{ secrets.API_KEY }}
      run: |
        mkdir -p ./.mnd/config
        chmod +x MNDCLI/mnd
        ./MNDCLI/mnd config h <<EOF
        1
        $USER_KEY
        $API_KEY
        yes
        EOF
        ./MNDCLI/mnd image Cotainer Image
```
