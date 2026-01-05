# Run Ansible Playbook with Cloud OIDC

![Built with Kiro](https://img.shields.io/badge/Built_with-Kiro-8845f4?logo=robot&logoColor=white)&nbsp;![Release](https://github.com/subhamay-bhattacharyya-gha/run-ansible-playbook-wf/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/run-ansible-playbook-wf)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/0759cf31f1c25f01365caa97eea304f5/raw/run-ansible-playbook-wf.json?)

A reusable GitHub Actions workflow that runs Ansible playbooks with cloud provider authentication using OpenID Connect (OIDC).

## Reusable Workflow: Run Ansible Playbook with Cloud OIDC

### Description

This reusable workflow sets up a complete Ansible environment with cloud provider authentication using OIDC and executes your specified Ansible playbook. It supports AWS, Azure, and GCP, automatically installing the necessary CLI tools and configuring authentication without requiring long-lived credentials.

**Key Features:**
- Multi-cloud OIDC authentication (AWS, Azure, GCP)
- Automatic installation of cloud CLI tools when missing
- Python and Ansible setup with configurable versions
- Ansible playbook execution with custom extra variables
- Input validation and debugging output
- Flexible checkout with release tag support

---

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `cloud-provider` | Target cloud provider (aws, gcp, azure) | Yes | — |
| `release-tag` | Git release tag to check out. If omitted, the latest commit on the default branch is used. | No | `""` |
| `python-version` | Python version for installing ansible | No | `"3.12"` |
| `ansible-extra-vars` | Extra variables to pass to the Ansible playbook | No | — |
| `ansible-playbook-path` | Path to the Ansible playbook file | Yes | — |
| `aws-region` | AWS region for authentication. Required when cloud-provider is 'aws'. | No | — |
| `azure-audience` | OIDC audience for Azure token exchange | No | `"api://AzureADTokenExchange"` |

## Secrets

| Name | Description | Required |
|------|-------------|----------|
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
name: Run Ansible Playbook on AWS

on:
  workflow_dispatch:
    inputs:
      message:
        description: 'Message to pass to playbook'
        required: false
        default: 'Hello from GitHub Actions!'

jobs:
  run-ansible:
    uses: subhamay-bhattacharyya-gha/run-ansible-playbook-wf/.github/workflows/run-ansible-playbook.yaml@main
    with:
      cloud-provider: "aws"
      aws-region: "us-west-2"
      python-version: "3.11"
      ansible-playbook-path: "deploy.yml"
      ansible-extra-vars: "message=${{ github.event.inputs.message || 'Hello from push event!' }}"
    secrets:
      aws-role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
```

### Azure Example

```yaml
name: Run Ansible Playbook on Azure

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'

jobs:
  run-ansible:
    uses: subhamay-bhattacharyya-gha/run-ansible-playbook-wf/.github/workflows/run-ansible-playbook.yaml@main
    with:
      cloud-provider: "azure"
      ansible-playbook-path: "azure-deploy.yml"
      ansible-extra-vars: "environment=${{ github.event.inputs.environment }}"
    secrets:
      azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
      azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      azure-subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

### GCP Example

```yaml
name: Run Ansible Playbook on GCP

on:
  push:
    branches: [main]

jobs:
  run-ansible:
    uses: subhamay-bhattacharyya-gha/run-ansible-playbook-wf/.github/workflows/run-ansible-playbook.yaml@main
    with:
      cloud-provider: "gcp"
      release-tag: "v1.0.0"
      ansible-playbook-path: "gcp-infrastructure.yml"
      ansible-extra-vars: "project_id=my-gcp-project region=us-central1"
    secrets:
      gcp-wif-provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
      gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT_EMAIL }}
```

## What This Workflow Does

1. **Input Validation**: Validates the cloud provider and prints all inputs for debugging
2. **Playbook Validation**: Validates that the specified Ansible playbook file exists
3. **Repository Checkout**: Checks out the specified release tag or latest commit
4. **Python Setup**: Installs the specified Python version
5. **Ansible Installation**: Installs Ansible via pip
6. **Cloud CLI Installation**: Automatically installs AWS CLI or Azure CLI if missing
7. **OIDC Authentication**: Configures cloud provider authentication using OIDC tokens
8. **Ansible Playbook Execution**: Runs the specified playbook with custom extra variables

The workflow provides a complete end-to-end solution for running Ansible playbooks with cloud authentication, eliminating the need for manual setup steps or long-lived credentials.

## License

MIT