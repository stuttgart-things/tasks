# Git Tasks

Taskfile collection for git operations, repository linting, and SOPS encryption.

## Usage

### Remote Taskfiles

```bash
export TASK_X_REMOTE_TASKFILES=1
```

### Local Taskfiles

```bash
task --taskfile /path/to/taskfile.yaml <task-name>
```

---

## git.yaml

Git workflow automation tasks including commits, PRs, branching, and releases.

<details>
<summary><b>Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# Commit and push
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml commit

# Create pull request
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml pr

# Create branch
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml branch

# Create release
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml release

# Run pre-commit hooks
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml check

# Run validation stage
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml run-validation-stage

# Run validation stage (interactive)
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml run-validation-stage-interactive

# Create GitHub issue
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml issue

# Switch to remote branch
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml switch-remote

# Switch to local branch
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml switch-local

# Tag repository
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml tag

# Run pre-commit hook script
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml run-pre-commit-hook
```

</details>

<details>
<summary><b>Local Usage</b></summary>

```bash
# Commit and push
task --taskfile git/git.yaml commit

# Create pull request
task --taskfile git/git.yaml pr

# Create branch
task --taskfile git/git.yaml branch

# Create release
task --taskfile git/git.yaml release

# Run pre-commit hooks
task --taskfile git/git.yaml check

# Run validation stage
task --taskfile git/git.yaml run-validation-stage

# Run validation stage (interactive)
task --taskfile git/git.yaml run-validation-stage-interactive

# Create GitHub issue
task --taskfile git/git.yaml issue

# Switch to remote branch
task --taskfile git/git.yaml switch-remote

# Switch to local branch
task --taskfile git/git.yaml switch-local

# Tag repository
task --taskfile git/git.yaml tag

# Run pre-commit hook script
task --taskfile git/git.yaml run-pre-commit-hook
```

</details>

<details>
<summary><b>Available Tasks</b></summary>

| Task | Description |
|------|-------------|
| `run-validation-stage` | Lint/validate multiple technologies (non-interactive) |
| `run-validation-stage-interactive` | Lint/validate multiple technologies (interactive with prompts) |
| `commit` | Commit + push code into branch |
| `pr` | Create pull request into main |
| `issue` | Create GitHub issue |
| `branch` | Create branch from main |
| `run-pre-commit-hook` | Run the pre-commit hook script |
| `check` | Run pre-commit hooks |
| `release` | Release new version |
| `switch-remote` | Switch to remote branch |
| `switch-local` | Switch to local branch |
| `tag` | Tag repository |

</details>

---

## linting.yaml

Repository linting and code analysis tasks using Dagger blueprints.

<details>
<summary><b>Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# Lint repository files
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/linting.yaml lint-repository-files

# Analyze lint report
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/linting.yaml analyze-lint-report
```

</details>

<details>
<summary><b>Local Usage</b></summary>

```bash
# Lint repository files
task --taskfile git/linting.yaml lint-repository-files

# Analyze lint report
task --taskfile git/linting.yaml analyze-lint-report
```

</details>

<details>
<summary><b>Available Tasks</b></summary>

| Task | Description |
|------|-------------|
| `lint-repository-files` | Lint repository using Dagger blueprint function |
| `analyze-lint-report` | Analyze lint report using Dagger blueprint |

</details>

---

## sops.yaml

SOPS encryption/decryption tasks with AGE key management.

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

## Requirements

- [Task](https://taskfile.dev/) - Task runner
- [gum](https://github.com/charmbracelet/gum) - Interactive prompts
- [Dagger](https://dagger.io/) - CI/CD engine
- [gh](https://cli.github.com/) - GitHub CLI
- [pre-commit](https://pre-commit.com/) - Git hooks
- [SOPS](https://github.com/getsops/sops) - Secrets management
- [age](https://github.com/FiloSottile/age) - Encryption tool
