# Description

With this action you can fetch secrets from [Azure Key Vault](https://docs.microsoft.com/en-us/rest/api/keyvault/about-keys--secrets-and-certificates) instance and consume in your GitHub Action workflows.

Fetched secrets will be set as environment variables and can be consumed in the subsequent actions in the workflow using the [env context](https://docs.github.com/en/actions/learn-github-actions/variables#using-the-env-context-to-access-environment-variable-values) e.g `${{ env.STORAGE_ACCOUNT_NAME }}`. All environment variables values are masked in log.

## Pre-configuration notes

Authenticate with [Azure login action](<https://github.com/Azure/login#github-action-for-azure-login>) and give roles to Azure service principal to Get and List secrets from Azure key vault. More information you can find [here](https://learn.microsoft.com/en-us/azure/key-vault/general/rbac-guide?tabs=azure-cli)

# Example

```on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Init secrets from KV as envs
      uses: ./.github/actions/setup-kv-secrets
      with:
        kv-subscription-name: ${{ secrets.CI_KEY_VAULT_SUBSCRIPTION }}
        kv-name: ${{ secrets.CI_KEY_VAULT_NAME }}

    - name: Create container
      run: |
        az storage container create \
        --auth-mode login \
        --account-name ${{ env.STORAGE_ACCOUNT_NAME }} \
        --name ${{ env.CONTAINER_NAME }}
```

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
