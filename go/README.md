# Go Tasks

Taskfile collection for Go-based Kubernetes microservice workflows.

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

## k8s-microservice.yaml

Commit and push workflow with Dagger-based pre-commit validation for Kubernetes microservices. Supports both programmatic (non-interactive) and interactive (gum) modes.

<details>
<summary><b>Include in Your Taskfile</b></summary>

Add the following to your project's `Taskfile.yaml`:

```yaml
version: 3
includes:
  k8sMicroservice:
    taskfile: https://raw.githubusercontent.com/stuttgart-things/tasks/refs/heads/main/go/k8s-microservice.yaml
```

This also pulls in the git sub-taskfiles (`git.yaml`, `precommit.yaml`, `linting.yaml`) automatically.

</details>

<details>
<summary><b>Environment Setup</b></summary>

Enable remote taskfile support:

```bash
export TASK_X_REMOTE_TASKFILES=1
```

If using GitHub-related tasks (PRs, issues), ensure your GitHub token is set:

```bash
export GITHUB_TOKEN=<your-token>
```

</details>

<details>
<summary><b>Example Calls</b></summary>

#### Programmatic (default, no gum required)

Commit on the current branch with the default message `feat: <branch-name>`:

```bash
task k8sMicroservice:commit
```

Commit with a custom message:

```bash
task k8sMicroservice:commit COMMIT_MSG="fix: resolve null pointer in handler"
```

Commit on a specific branch:

```bash
task k8sMicroservice:commit BRANCH=feat/my-feature
```

Enable pre-commit hooks:

```bash
task k8sMicroservice:commit ENABLE_PRE_COMMIT=true
```

Skip specific pre-commit hooks:

```bash
task k8sMicroservice:commit ENABLE_PRE_COMMIT=true SKIP_HOOKS="trailing-whitespace end-of-file-fixer"
```

Disable YAML or Markdown linting:

```bash
task k8sMicroservice:commit ENABLE_YAML=false
task k8sMicroservice:commit ENABLE_MARKDOWN=false
```

Exclude files from secrets scanning (regex pattern with `|` separator):

```bash
task k8sMicroservice:commit SECRETS_EXCLUDE_FILES="node_modules/.*|dist/.*|.*\.gen\.go" #pragma: allowlist secret
```

Fail on specific findings:

```bash
# Fail if any linter produces findings
task k8sMicroservice:commit FAIL_ON=any

# Fail only on YAML lint findings
task k8sMicroservice:commit FAIL_ON=yaml

# Fail only on secret scan findings
task k8sMicroservice:commit FAIL_ON=secrets

# Fail on error-level findings
task k8sMicroservice:commit FAIL_ON=error

# Fail on warning-level or higher findings
task k8sMicroservice:commit FAIL_ON=warning
```

Lint a specific directory:

```bash
task k8sMicroservice:commit SRC=docs
```

#### Interactive (requires gum)

Enable interactive mode for confirmation prompts and commit message selection via gum:

```bash
task k8sMicroservice:commit INTERACTIVE=true
```

</details>

<details>
<summary><b>Variables</b></summary>

| Variable | Default | Description |
|----------|---------|-------------|
| `MODULE` | `github.com/stuttgart-things/blueprints/repository-linting@v1.62.0` | Dagger module for linting |
| `SRC` | `./` | Source directory to lint |
| `BRANCH` | Current git branch | Target branch for commit and push |
| `INTERACTIVE` | `false` | Set to `true` to enable gum-based interactive prompts |
| `COMMIT_MSG` | `feat: <BRANCH>` | Commit message (only used when `INTERACTIVE=false`) |
| **YAML Linting** | | |
| `ENABLE_YAML` | `true` | Enable YAML linting |
| `YAML_CONFIG_PATH` | `.yamllint` | Path to yamllint config |
| `YAML_OUTPUT_FILE` | `yamllint-findings.txt` | Output file for yamllint results |
| **Markdown Linting** | | |
| `ENABLE_MARKDOWN` | `true` | Enable Markdown linting |
| `MARKDOWN_CONFIG_PATH` | `.mdlrc` | Path to markdownlint config |
| `MARKDOWN_OUTPUT_FILE` | `markdown-findings.txt` | Output file for markdownlint results |
| **Pre-commit** | | |
| `ENABLE_PRE_COMMIT` | `false` | Enable pre-commit hooks |
| `PRE_COMMIT_CONFIG_PATH` | `.pre-commit-config.yaml` | Path to pre-commit config |
| `PRE_COMMIT_OUTPUT_FILE` | `pre-commit-findings.txt` | Output file for pre-commit results |
| `SKIP_HOOKS` | _(empty)_ | Space-separated list of pre-commit hooks to skip |
| **Secrets Scanning** | | |
| `ENABLE_SECRETS` | `true` | Enable secrets scanning |
| `SECRETS_OUTPUT_FILE` | `secret-findings.json` | Output file for secrets scan results |
| `SECRETS_EXCLUDE_FILES` | `dagger.json\|.dagger/.*\|dist/.*\|node_modules/.*` | Regex pattern to exclude files/dirs (use `\|` separator) |
| **Output** | | |
| `MERGED_OUTPUT_FILE` | `all-findings.txt` | Merged output file for all lint findings |
| **Fail Control** | | |
| `FAIL_ON` | `none` | Controls when the pipeline fails. See below for supported values |

**`FAIL_ON` Supported Values:**
- `none` — never fail (only report findings)
- `any` — fail if any linter produces findings
- `yaml` — fail only on YAML lint findings
- `markdown` — fail only on Markdown lint findings
- `secrets` — fail only on secret scan findings
- `precommit` — fail only on pre-commit findings
- `error` — fail on error-level findings (yamllint errors; any finding from other linters)
- `warning` — fail on warning-level or higher findings (yamllint warnings/errors; any finding from other linters)

</details>

<details>
<summary><b>Available Tasks</b></summary>

| Task | Description |
|------|-------------|
| `commit` | Commit and push with Dagger pre-commit validation |

</details>

<details>
<summary><b>Workflow</b></summary>

The `commit` task runs the following steps:

1. Run Dagger `validate-multiple-technologies` with enabled linters:
   - YAML linting (yamllint) - enabled by default
   - Markdown linting (markdownlint) - enabled by default
   - Pre-commit hooks - disabled by default (enable with `ENABLE_PRE_COMMIT=true`)
   - Secrets scanning (detect-secrets) - enabled by default
2. Display merged findings with `bat` or `cat`
3. Evaluate fail condition based on `FAIL_ON` setting
4. Set upstream branch and pull latest changes
5. Stage all changes
6. Commit changes:
   - **Programmatic mode** (`INTERACTIVE=false`): uses `COMMIT_MSG` directly
   - **Interactive mode** (`INTERACTIVE=true`): prompts for confirmation and commit message via gum
7. Push to origin

All linters run in parallel. Results are merged in fixed order: YAML, Markdown, Pre-Commit, Secrets.

</details>

---

## Requirements

- [Task](https://taskfile.dev/) - Task runner
- [Dagger](https://dagger.io/) - CI/CD engine
- [bat](https://github.com/sharkdp/bat) - Syntax-highlighted file viewer
- [gum](https://github.com/charmbracelet/gum) - Interactive prompts (only required when `INTERACTIVE=true`)
- [git](https://git-scm.com/) - Version control
