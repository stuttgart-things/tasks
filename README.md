# stuttgart-things/tasks

collection of taskfiles

## Usage

Include remote taskfiles in your `Taskfile.yaml`:

```yaml
version: '3'

includes:
  git:
    taskfile: https://raw.githubusercontent.com/stuttgart-things/tasks/refs/heads/main/git/git.yaml
  ansible:
    taskfile: https://raw.githubusercontent.com/stuttgart-things/tasks/refs/heads/main/ansible/execute.yaml
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
task --taskfile https://raw.githubusercontent.com/stuttgart-things/tasks/refs/heads/main/git/git.yaml
```
