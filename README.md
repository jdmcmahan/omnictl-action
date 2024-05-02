# `omnictl` GitHub Action

This repository enables running general [`omnictl` commands](https://omni.siderolabs.com/docs/reference/cli/) as GitHub
Actions. Use this action to manage [Sidero Omni resources](https://omni.siderolabs.com/docs/reference/cluster-templates/)
as part of a GitOps workflow or CI/CD pipeline.

## Configuration

### Setup

Before using this action, you must create an Omni service account to authenticate with Sidero Omni.
See https://omni.siderolabs.com/docs/how-to-guides/how-to-create-a-service-account/ for more information about creating
a service account.

Once the service account is created, it is recommended to save the endpoint and service account key as repository
secrets (`Settings` > `Secrets and variables` > `Actions`). The below examples assume these values are saved
as `OMNI_ENDPOINT` and `OMNI_SERVICE_ACCOUNT_KEY`, respectively.

### Inputs

#### Required inputs

| Name                       | Description                                                                                                                                                                                                                                                                                                                                | Default             |
|:---------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| `omni-endpoint`            | The Omni endpoint URL generated for your service account. See https://omni.siderolabs.com/docs/how-to-guides/how-to-create-a-service-account/ for more information about creating a service account.                                                                                                                                       |                     |
| `omni-service-account-key` | The Omni service account key generated for your service account. See https://omni.siderolabs.com/docs/how-to-guides/how-to-create-a-service-account/ for more information about creating a service account.                                                                                                                                |                     |
| `command`                  | The omnictl command to run. See https://omni.siderolabs.com/docs/reference/cli/ for a full list of `omnictl` commands and parameters.                                                                                                                                                                                                      | `omnictl --version` |

#### Optional inputs

| Name                       | Description                                                                                                                                                                                                                                                                                                                                | Default             |
|:---------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| `working-directory`        | The working directory from which to execute the command.<br/><br/>This is useful for operations that rely on a specific file context such as cluster templates containing relative paths. Note that the "current directory" (`./`) is usually the root directory of your cloned repository if used in conjunction with `actions/checkout`. | `./`                |

## Examples

### Validate a cluster template

```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'path/to/cluster/resources/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    name: Validate cluster templates
    steps:
      - name: Checkout cluster templates
        uses: actions/checkout@v4

      - name: Run omnictl
        uses: jdmcmahan/omnictl-action@v1
        with:
          working-directory: ./path/to/cluster/resources/
          omni-endpoint: ${{ secrets.OMNI_ENDPOINT }}
          omni-service-account-key: ${{ secrets.OMNI_SERVICE_ACCOUNT_KEY }}
          command: omnictl cluster template -f cluster.yaml validate
```

### Sync a cluster template with Omni

```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'path/to/cluster/resources/**'

jobs:
  sync:
    runs-on: ubuntu-latest
    name: Sync cluster templates with Sidero Omni
    steps:
      - name: Checkout cluster templates
        uses: actions/checkout@v4

      - name: Run omnictl
        uses: jdmcmahan/omnictl-action@v1
        with:
          working-directory: ./path/to/cluster/resources/
          omni-endpoint: ${{ secrets.OMNI_ENDPOINT }}
          omni-service-account-key: ${{ secrets.OMNI_SERVICE_ACCOUNT_KEY }}
          command: omnictl cluster template -f cluster.yaml sync --verbose
```

### Create or update a resource directly

```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'path/to/cluster/resources/**'

jobs:
  apply:
    runs-on: ubuntu-latest
    name: Create or update a super important Omni resource
    steps:
      - name: Checkout cluster templates
        uses: actions/checkout@v4

      - name: Run omnictl
        uses: jdmcmahan/omnictl-action@v1
        with:
          working-directory: ./path/to/cluster/resources/
          omni-endpoint: ${{ secrets.OMNI_ENDPOINT }}
          omni-service-account-key: ${{ secrets.OMNI_SERVICE_ACCOUNT_KEY }}
          command: omnictl apply -f resource.yaml --verbose
```
