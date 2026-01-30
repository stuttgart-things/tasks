# Stuttgart Things Tasks

Collection of reusable Taskfiles for automation workflows.

## Overview

This repository contains modular Taskfiles for various DevOps and platform engineering tasks:

| Category | Description |
|----------|-------------|
| [Git](git.md) | Git workflows, commits, PRs, releases, and repository linting |
| [Kubernetes](kubernetes.md) | Kubernetes and Crossplane operations |
| [Ansible](ansible.md) | Ansible playbook execution via Dagger |
| [Backstage](backstage.md) | Backstage instance scaffolding and GitHub auth setup |
| [SOPS](sops.md) | SOPS encryption/decryption with AGE keys |

## Usage

### Remote Taskfiles

Include remote taskfiles in your `Taskfile.yaml`:

```yaml
version: '3'

includes:
  git:
    taskfile: https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml
  ansible:
    taskfile: https://raw.githubusercontent.com/stuttgart-things/tasks/main/ansible/execute.yaml
```

Run tasks with the namespace prefix:

```bash
task git:commit
task ansible:lint
```

### Direct Execution

Run remote taskfiles directly without including them:

```bash
export TASK_X_REMOTE_TASKFILES=1
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/git/git.yaml commit
```

### Local Execution

```bash
task --taskfile git/git.yaml commit
```

## Requirements

- [Task](https://taskfile.dev/) - Task runner
- [gum](https://github.com/charmbracelet/gum) - Interactive prompts
- [Dagger](https://dagger.io/) - CI/CD engine
- [gh](https://cli.github.com/) - GitHub CLI
- [pre-commit](https://pre-commit.com/) - Git hooks

## Resources

- [GitHub Repository](https://github.com/stuttgart-things/tasks)
- [Taskfile Documentation](https://taskfile.dev)
