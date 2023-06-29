# Gomboc.AI Terraform Action

### Setting up your workflow

Gomboc's Terraform Action requires a few settings to run on Github Actions.

---

Start by creating or expanding an existing workflow that you have, like so:

```
name: Gomboc.AI Terraform Scan

on:
  pull_request:
  push:
    branches: [ main ]

permissions:
  id-token: write
  contents: read

jobs:
  gomboc:
    runs-on: ubuntu-latest
    name: Gomboc Terraform Action
    steps:
      ...
```

---

Note the minimal set of permissions required:

| Permission | Description |
| --- | --- |
| `id-token: write` | Required to authenticate you in our service |
| `contents: read` | Required to read your Terraform plan and HCL templates |

---

Next, use [Github's Checkout Action](https://github.com/marketplace/actions/checkout):

```
- name: Check the code
    uses: actions/checkout@v3
```

This will allow us to scan your repository for Terraform templates. 

---

Finally, add one or more Gomboc.AI actions:

```
- name: Gomboc.AI - Terraform Scan
  uses: Gomboc-AI/actions/terraform/scan@main
```

### Variables

| variable | Required | Default | Description | Additional permissions |
| --- | --- | --- | --- | --- |
| `gomboc-config` | No |  `gomboc.yaml` | Path to Gomboc.AI's configuration file | N/A |
| `tf-directory` | No | `.` | The root directory for the Terraform configuration | N/A |
| `tf-plan` | No | `tfplan.json` | A filepath to a local JSON file describing your Terraform plan (relative to tf-directory) | N/A |
| `access-token` | No |   | Access token needed to perform API side effects (`secrets.GITHUB_TOKEN`) | N/A |
| `create-pr` | No |  `false` | Create a new PR with remediations | `contents: write` |
| `commit-on-current-branch` | No |  `false` | Commit remediations into the current branch | `contents: write` |

### Bring it all together

Your completed Gomboc.AI Terraform Workflow should look something like this:

```
name: Gomboc.AI Terraform

on:
  pull_request:
  push:
    branches: [ main ]

permissions:
  id-token: write
  contents: write

jobs:
  gomboc:
    runs-on: ubuntu-latest
    steps:
      - name: Check the code
        uses: actions/checkout@v3
        
      - name: Gomboc.AI - Terraform Scan
        uses: Gomboc-AI/actions/terraform/scan@main
        env:
          FORCE_COLOR: 3
        with:
          access-token: ${{ secrets.GITHUB_TOKEN }} 
          commit-on-current-branch: true
```

`FORCE_COLOR: 3` will force the GitHub console to output all the colors.

### About Gomboc.AI's configuration file

It is a YAML file that specifies different parameters for the scan action.

Here's an example:

```
policies: 
  must-implement:
    - Deletion Protection
    - Request Tracing
    - Client Authentication via IAM SigV4
    - Skip Terraform Destroy
```

| Element | Required | Description |
| --- | --- | --- |
| <kbd>policies.must-implement</kbd> | Yes | A list of capabilities that will be enforced |