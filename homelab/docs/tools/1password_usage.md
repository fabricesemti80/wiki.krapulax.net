# 1Password Usage

This document outlines how 1Password is integrated into the homelab automation workflow to manage secrets securely.

## Overview

We utilize 1Password to avoid storing secrets in plain text (`.env` files) on the local filesystem. This is achieved through the 1Password CLI (`op`) and direct integrations with our Infrastructure-as-Code tools.

## Setup

### 1Password CLI (`op`)

The foundation of the integration is the `op` CLI tool. It must be installed and authenticated on the management machine.

```bash
# Check status
op account list
```

### Service Account Authentication

For automated environments (CI/CD, scripts) where manual login is not possible, we use 1Password Service Accounts. This allows authentication via a single environment variable.

**Step 1: Create Service Account**

1. Log in to 1Password.com.
2. Navigate to **Developer Tools** > **Service Accounts**.
3. Click **Create Service Account**, name it (e.g., `Homelab-Automation`), and choose the specific vaults it can access.
4. **Save the Service Account Token** immediately. You won't be able to see it again.

**Step 2: Configure Environment**
Set the `OP_SERVICE_ACCOUNT_TOKEN` environment variable on the machine running your scripts or automation.

```bash
export OP_SERVICE_ACCOUNT_TOKEN="your_service_account_token_here"
```

**Step 3: Verify Access**
Run a command to verify the service account can access the vaults.

```bash
# Verify identity
op user get --me

# List items in a vault (replace vault_name)
op item list --vault vault_name
```

**Step 4: Use in Automation**
Once the environment variable is set, tools like the Terraform provider and `op run` will automatically use this token for authentication without requiring `op signin`.

## Integrations

### Terraform / OpenTofu

We use the **1Password Provider** to inject secrets directly into Terraform resources.

* **Provider**: `1password/onepassword`
* **Usage**: Secrets are referenced by their vault item ID or path.
* **Auth**: Automatically detects `OP_SERVICE_ACCOUNT_TOKEN`.

### Ansible

We use the **1Password Lookup Plugin** to retrieve secrets during playbook execution.

* **Collection**: `community.general.onepassword_lookup`
* **Usage**: Variables are populated dynamically at runtime.
* **Auth**: Requires the `op` CLI to be authenticated (via the token env var) on the controller node.

```yaml
# Example
api_key: "{{ lookup('community.general.onepassword', 'vault_id', item='item_name', field='password') }}"
