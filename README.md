# Bootstrap Ansible with Cloud OIDC

![Built with Kiro](https://img.shields.io/badge/Built_with-Kiro-8845f4?logo=robot&logoColor=white)&nbsp;![Release](https://github.com/subhamay-bhattacharyya-gha/bootstrap-ansible-wf/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/bootstrap-ansible-wf)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/f6b6f5bb3da58cb3a6ca3f01bd7582cd/raw/bootstrap-ansible-wf.json?)

A reusable GitHub Actions workflow that bootstraps Ansible with cloud provider authentication using OpenID Connect (OIDC).

## Reusable Workflow: Bootstrap Ansible with Cloud OIDC

### Description

This reusable workflow sets up a complete Ansible environment with cloud provider authentication using OIDC. It supports AWS, Azure, and GCP, automatically installing the necessary CLI tools and configuring authentication without requiring long-lived credentials.

**Key Features:**
- Multi-cloud OIDC authentication (AWS, Azure, GCP)
- Automatic installation of cloud CLI tools when missing
- Python and Ansible setup with configurable versions
- Input validation and debugging output
- Flexible checkout with release tag support

---

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `cloud-provider` | Target cloud provider (aws, gcp, azure) | Yes | — |
| `release-tag` | Git release tag to check out. If omitted, the latest commit on the default branch is used. | No | `""` |
| `python-version` | Python version for installing ansible | No | `"3.12"` |
| `aws-region` | AWS region for authentication. Required when cloud-provider is 'aws'. | No | — |
| `azure-audience` | OIDC audience for Azure token exchange | No | `"api://AzureADTokenExchange"` |

## Secrets

| Name | Description | Required |
|------|-------------|----------|
| `tfc-token` | HCP Terraform Cloud API token. Required when backend-type is 'remote'. | No |
| `aws-role-to-assume` | AWS IAM role ARN to assume. Required when cloud-provider is 'aws'. | No |
| `gcp-wif-provider` | GCP Workload Identity Federation provider. Required when cloud-provider is 'gcp'. | No |
| `gcp-service-account` | GCP service account email for authentication. Required when cloud-provider is 'gcp'. | No |
| `azure-client-id` | Azure client ID for authentication. Required when cloud-provider is 'azure'. | No |
| `azure-tenant-id` | Azure tenant ID for authentication. Required when cloud-provider is 'azure'. | No |
| `azure-subscription-id` | Azure subscription ID for authentication. Required when cloud-provider is 'azure'. | No |

---

## Example Usage

### AWS Example

```yaml
name: Deploy with Ansible to AWS

on:
  workflow_dispatch:

jobs:
  bootstrap-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Bootstrap Ansible with AWS OIDC
        uses: subhamay-bhattacharyya-gha/bootstrap-ansible-wf/.github/workflows/ansible-bootstrap-oidc.yaml@main
        with:
          cloud-provider: "aws"
          aws-region: "us-west-2"
          python-version: "3.11"
        secrets:
          aws-role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
      
      - name: Run Ansible Playbook
        run: |
          ansible-playbook deploy.yml \
            --extra-vars "environment=production"
```

### Azure Example

```yaml
name: Deploy with Ansible to Azure

on:
  workflow_dispatch:

jobs:
  bootstrap-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Bootstrap Ansible with Azure OIDC
        uses: subhamay-bhattacharyya-gha/bootstrap-ansible-wf/.github/workflows/ansible-bootstrap-oidc.yaml@main
        with:
          cloud-provider: "azure"
        secrets:
          azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
          azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      
      - name: Run Ansible Playbook
        run: |
          ansible-playbook deploy.yml \
            --extra-vars "environment=production"
```

### GCP Example

```yaml
name: Deploy with Ansible to GCP

on:
  workflow_dispatch:

jobs:
  bootstrap-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Bootstrap Ansible with GCP OIDC
        uses: subhamay-bhattacharyya-gha/bootstrap-ansible-wf/.github/workflows/ansible-bootstrap-oidc.yaml@main
        with:
          cloud-provider: "gcp"
          release-tag: "v1.0.0"
        secrets:
          gcp-wif-provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
          gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT_EMAIL }}
      
      - name: Run Ansible Playbook
        run: |
          ansible-playbook deploy.yml \
            --extra-vars "environment=production"
```

## What This Workflow Does

1. **Input Validation**: Validates the cloud provider and prints all inputs for debugging
2. **Repository Checkout**: Checks out the specified release tag or latest commit
3. **Python Setup**: Installs the specified Python version
4. **Ansible Installation**: Installs Ansible via pip
5. **Cloud CLI Installation**: Automatically installs AWS CLI or Azure CLI if missing
6. **OIDC Authentication**: Configures cloud provider authentication using OIDC tokens
7. **Environment Preparation**: Sets up environment variables for subsequent Ansible execution

After this workflow completes, your job environment will have:
- Ansible installed and ready to use
- Cloud provider CLI tools installed
- Authentication configured via OIDC
- Environment variables set for cloud provider access

## License

MIT