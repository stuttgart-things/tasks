# Git Tasks

Git workflow automation tasks including commits, PRs, branching, releases, and repository linting.

## Taskfiles

- `git/git.yaml` - Core git workflows
- `git/linting.yaml` - Repository linting with Dagger
- `git/sops.yaml` - SOPS encryption (see [SOPS](sops.md))

---

## git.yaml

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

# Create GitHub issue
task --taskfile git/git.yaml issue

# Switch branches
task --taskfile git/git.yaml switch-remote
task --taskfile git/git.yaml switch-local

# Tag repository
task --taskfile git/git.yaml tag
```

</details>

<details>
<summary><b>Available Tasks</b></summary>

| Task | Description |
|------|-------------|
| `run-validation-stage` | Lint/validate multiple technologies (non-interactive) |
| `run-validation-stage-interactive` | Lint/validate multiple technologies (interactive) |
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

## Requirements

- [Task](https://taskfile.dev/)
- [gum](https://github.com/charmbracelet/gum)
- [Dagger](https://dagger.io/)
- [gh](https://cli.github.com/)
- [pre-commit](https://pre-commit.com/)
- [semantic-release](https://semantic-release.gitbook.io/)
