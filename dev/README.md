# dev

Reusable [Task](https://taskfile.dev) files for local developer workflows.

## Requirements

- [`task`](https://taskfile.dev/installation/) (go-task)
- [`tmux`](https://github.com/tmux/tmux)
- [`gum`](https://github.com/charmbracelet/gum) — used for interactive prompts (choose / filter / input / confirm)

## Files

### `tmux.yaml`

Interactive tmux session manager built on `gum`. Run with `task --taskfile tmux.yaml <task>`.

| Task | Alias | What it does |
|------|-------|--------------|
| `tmux` | `t` | Smart entry point — creates a session if none exist, otherwise prompts to attach / create / kill / list. |
| `tmux-create` | `tc` | Create a new session from a preset (`custom`, `claude-code`, `k8s-ops`, `homelab`). Prompts for session name and working directory. |
| `tmux-attach` | `ta` | Fuzzy-pick an existing session to attach to. |
| `tmux-kill` | `tk` | Multi-select sessions to kill (with confirmation). |
| `tmux-list` | `tl` | Show all running sessions. |
| `tmux-project` | `tp` | One session per repo — pick a directory under `$PROJECTS_ROOT` (default `$HOME/code/stuttgart-things`) and attach/create a session named after it. |

#### Presets (`tmux-create`)

- **claude-code** — windows: `claude` (auto-runs `claude`), `shell`, `git`
- **k8s-ops** — windows: `k9s` (auto-runs `k9s`), `kubectl`, `flux` (auto-runs `flux get kustomizations -A`), `argo`
- **homelab** — windows: `main`, `proxmox`, `vault`, `logs`
- **custom** — single empty window

#### Examples

```bash
# from this directory
task --taskfile tmux.yaml t          # smart entry
task --taskfile tmux.yaml tc         # create new session
task --taskfile tmux.yaml tp         # jump to a project session

# or from anywhere
task -t /home/sthings/projects/tasks/dev/tmux.yaml ta
```

To use the short aliases globally, either symlink `tmux.yaml` into a directory you `cd` into often, or wrap the calls in shell functions / aliases in your `.bashrc`/`.zshrc`.
