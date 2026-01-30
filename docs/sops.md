# SOPS Encryption Tasks

SOPS encryption/decryption tasks with AGE key management using Dagger.

## Taskfile

- `git/sops.yaml` - SOPS encryption operations

---

## sops.yaml

<details>
<summary><b>Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# Generate AGE key pair
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/sops.yaml generate-age-key

# Generate SOPS config
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/sops.yaml generate-sops-config

# Encrypt file
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/sops.yaml encrypt

# Decrypt file
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/sops.yaml decrypt
```

</details>

<details>
<summary><b>Local Usage</b></summary>

```bash
# Generate AGE key pair
task --taskfile git/sops.yaml generate-age-key

# Generate SOPS config
task --taskfile git/sops.yaml generate-sops-config

# Encrypt file
task --taskfile git/sops.yaml encrypt

# Decrypt file
task --taskfile git/sops.yaml decrypt
```

</details>

<details>
<summary><b>Available Tasks</b></summary>

| Task | Description |
|------|-------------|
| `generate-age-key` | Generate a new AGE key pair |
| `generate-sops-config` | Generate a .sops.yaml configuration file |
| `encrypt` | Encrypt a plaintext file using SOPS with an AGE key |
| `decrypt` | Decrypt a SOPS-encrypted file using an AGE key |

</details>

---

## Environment Variables

The tasks will interactively prompt for AGE keys, but you can also set environment variables:

| Variable | Description |
|----------|-------------|
| `AGE_PUBLIC_KEY` | AGE public key (age1...) |
| `AGE_PRIVATE_KEY` | AGE private key (AGE-SECRET-KEY-...) |
| `SOPS_AGE_KEY` | Alternative AGE key variable |

---

## Workflow Example

```bash
# 1. Generate a new AGE key pair
task --taskfile git/sops.yaml generate-age-key
# Outputs key to ./age-key.txt

# 2. Generate SOPS config
task --taskfile git/sops.yaml generate-sops-config
# Creates .sops.yaml with encryption rules

# 3. Encrypt secrets
task --taskfile git/sops.yaml encrypt
# Encrypts ./secrets.yaml to ./secrets.enc.yaml

# 4. Decrypt when needed
task --taskfile git/sops.yaml decrypt
# Decrypts to stdout or file
```

---

## Requirements

- [Task](https://taskfile.dev/)
- [Dagger](https://dagger.io/)
- [gum](https://github.com/charmbracelet/gum)
- [SOPS](https://github.com/getsops/sops)
- [age](https://github.com/FiloSottile/age)
