# HashiCorp Vault – DevOps Project

## Project Overview

This project demonstrates how to install, configure, and manage HashiCorp Vault on Ubuntu Linux.

HashiCorp Vault is a secrets management tool used to securely store, access, and manage sensitive information such as passwords, API keys, and tokens.

## Technologies Used

- Ubuntu Linux
- HashiCorp Vault
- CLI (Command Line Interface)
- Vault Web UI
- KV Version 2 Secrets Engine
- Userpass Authentication
- ACL Policies
- systemd

## Project Configuration

| Configuration | Value |
|---|---|
| Operating System | Ubuntu |
| Vault Service | vault-8201 |
| Vault Address | http://127.0.0.1:8200 |
| Storage Backend | File |
| Storage Directory | /opt/vault-8200/data |
| Secrets Engine | KV Version 2 |
| Authentication | Userpass |
| Policy | task4-readonly |

> Note: This project uses a separate Vault instance on port 8201. The existing Vault instance on port 8200 and its data are kept separate.

## Task 1 – Install and Initialize Vault

### Check Vault version

```bash
vault version
```

### Check service status

```bash
sudo systemctl status vault-8201
```

### Set Vault address

```bash
export VAULT_ADDR="http://127.0.0.1:8201"
```

### Initialize Vault

```bash
vault operator init
```

Save all generated unseal keys and the initial root token securely.

### Unseal Vault

```bash
vault operator unseal
```

Enter a different unseal key each time. Repeat until the required threshold is reached.

### Verify Vault

```bash
vault status
```

Expected:

- Initialized: true
- Sealed: false

## Task 2 – Enable Userpass Authentication

### Check authentication methods

```bash
vault auth list
```

### Enable Userpass

```bash
vault auth enable userpass
```

### Create a user

```bash
vault write auth/userpass/users/student \
  password='YourStrongPassword123!' \
  policies='default'
```

### Verify user

```bash
vault read auth/userpass/users/student
```

### Test user login

```bash
vault login -method=userpass username=student
```

Enter the password when prompted.

## Task 3 – Configure KV Version 2 Secrets Engine

### List secrets engines

```bash
vault secrets list
```

### Enable KV Version 2

```bash
vault secrets enable -path=secret kv-v2
```

If `secret/` is already enabled, do not enable it again.

### Store a secret

```bash
vault kv put secret/student \
  username="admin" \
  password="Student@123" \
  department="DevOps"
```

### Retrieve the secret

```bash
vault kv get secret/student
```

### Update the secret

```bash
vault kv put secret/student \
  username="admin" \
  password="NewStudent@456" \
  department="Cloud DevOps"
```

### View previous version

```bash
vault kv get -version=1 secret/student
```

### View metadata

```bash
vault kv metadata get secret/student
```

## Task 4 – Store and Retrieve Secrets

### Store a new secret

```bash
vault kv put secret/task4 \
  username="admin" \
  password="Admin@12345" \
  department="DevOps" \
  project="HashiCorp Vault"
```

### Retrieve the secret

```bash
vault kv get secret/task4
```

### Retrieve individual fields

```bash
vault kv get -field=username secret/task4
```

```bash
vault kv get -field=department secret/task4
```

### Update secret

```bash
vault kv put secret/task4 \
  username="admin" \
  password="NewAdmin@67890" \
  department="Cloud DevOps" \
  project="HashiCorp Vault"
```

### View version history

```bash
vault kv get -version=1 secret/task4
```

```bash
vault kv get -version=2 secret/task4
```

## Task 5 – Create Vault Policies and Verify Access

### Create policy file

```bash
nano task5-policy.hcl
```

Add the following:

```hcl
path "secret/data/task4" {
  capabilities = ["read"]
}

path "secret/metadata/task4" {
  capabilities = ["read"]
}
```

### Upload policy

```bash
vault policy write task4-readonly task5-policy.hcl
```

### Verify policy

```bash
vault policy list
```

### Enable Userpass if necessary

```bash
vault auth enable userpass
```

### Create a read-only user

```bash
vault write auth/userpass/users/task5user \
  password='ChooseYourOwnStrongPassword!' \
  policies='task4-readonly'
```

### Log in as the read-only user

```bash
vault login -method=userpass username=task5user
```

Enter the password when prompted.

### Test read permission

```bash
vault kv get secret/task4
```

Expected: Secret data is displayed.

### Test write permission

```bash
vault kv put secret/task4 username="testuser"
```

Expected: Permission denied.

### Verify token capabilities

```bash
vault token capabilities secret/data/task4
```

Expected:

```text
read
```

## Access Vault Web UI

Open the following URL in your Ubuntu browser:

**http://127.0.0.1:8201**

Log in using your Vault token.

You can access:

- Secrets Engines
- KV secrets
- ACL Policies
- Authentication Methods
- Secret version history

## Verify Vault Port

```bash
sudo ss -lntp | grep 8201
```

Expected listener:

```text
127.0.0.1:8201
```

## Important Security Notes

- Never share Vault root tokens or unseal keys.
- Store unseal keys securely and separately.
- Do not commit real passwords, tokens, or credentials to GitHub.
- Use strong passwords for Vault users.
- The sample credentials in this README are for practice only.
- Keep the existing Vault data directory safe.
- This configuration listens on localhost only. Configure TLS and appropriate network access before exposing Vault to other machines.

## Project Outcome

After completing these tasks, you will understand how to:

1. Install and initialize HashiCorp Vault.
2. Unseal and authenticate Vault.
3. Configure Userpass authentication.
4. Enable and manage KV Version 2 secrets.
5. Store, retrieve, and update secrets.
6. Create ACL policies.
7. Assign policies to users.
8. Verify read and write permissions.
9. Access Vault through the Web UI.

---

**Project:** HashiCorp Vault Installation and Secrets Management

**Platform:** Ubuntu Linux

**Vault Port:** 8201

**Status:** Tasks 1–5 documented.
