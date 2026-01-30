# Backstage Tasks

Backstage instance scaffolding and GitHub authentication setup.

## Taskfiles

- `backstage/init.yaml` - Instance scaffolding
- `backstage/github-auth.yaml` - GitHub authentication

---

## Prerequisites

<details>
<summary><b>Required Tools</b></summary>

| Tool | Minimum Version | Check Command |
|------|-----------------|---------------|
| Node.js | 22.x or 24.x | `node --version` |
| npm | 9.x+ | `npm --version` |
| yarn | 4.x+ | `yarn --version` |
| Docker | (for TechDocs) | `docker --version` |

```bash
# Check all prerequisites at once
task --taskfile backstage/init.yaml check-prerequisites
```

</details>

---

## Init Tasks

<details>
<summary><b>Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# Check prerequisites
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/init.yaml check-prerequisites

# Install yarn
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/init.yaml install-yarn

# Scaffold new instance (interactive)
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/init.yaml scaffold-new-instance

# Scaffold in current directory
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/init.yaml scaffold-new-instance-here

# Full init
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/init.yaml init
```

</details>

<details>
<summary><b>Local Usage</b></summary>

```bash
task --taskfile backstage/init.yaml check-prerequisites
task --taskfile backstage/init.yaml install-yarn
task --taskfile backstage/init.yaml scaffold-new-instance
task --taskfile backstage/init.yaml scaffold-new-instance-here
task --taskfile backstage/init.yaml init
```

</details>

---

## GitHub Auth Setup

### Authentication Modes

| Mode | Use Case | User Management |
|------|----------|-----------------|
| **Single User** | Personal/development | Manual - add users to `examples/org.yaml` |
| **Organization** | Team/production | Automatic - syncs users from GitHub org |

<details>
<summary><b>Environment Variables</b></summary>

| Variable | Single User | Org Mode | Description |
|----------|-------------|----------|-------------|
| `AUTH_GITHUB_CLIENT_ID` | Required | Required | GitHub OAuth App Client ID |
| `AUTH_GITHUB_CLIENT_SECRET` | Required | Required | GitHub OAuth App Client Secret |
| `GITHUB_ORG` | - | Required | GitHub organization name |
| `GITHUB_TOKEN` | Optional | Required | PAT with `read:org` scope |
| `BACKSTAGE_FRONTEND_URL` | Optional | Optional | Frontend base URL |
| `BACKSTAGE_BACKEND_URL` | Optional | Optional | Backend base URL |

</details>

<details>
<summary><b>GitHub Auth Tasks - Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# Show setup instructions
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml info

# Configure single user auth
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml configure

# Add user to catalog
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml add-user

# Configure org auth
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml configure-org

# Verify configuration
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml verify

# Full setup (single user)
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml full-setup

# Full setup (org mode)
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/backstage/github-auth.yaml full-setup-org
```

</details>

<details>
<summary><b>GitHub Auth Tasks - Local Usage</b></summary>

```bash
# Show setup instructions
task --taskfile backstage/github-auth.yaml info

# Configure single user auth
task --taskfile backstage/github-auth.yaml configure

# Add user to catalog
task --taskfile backstage/github-auth.yaml add-user

# Configure org auth
task --taskfile backstage/github-auth.yaml configure-org

# Verify configuration
task --taskfile backstage/github-auth.yaml verify

# Full setup (single user)
task --taskfile backstage/github-auth.yaml full-setup

# Full setup (org mode)
task --taskfile backstage/github-auth.yaml full-setup-org
```

</details>

---

## Troubleshooting

<details>
<summary><b>Common Issues</b></summary>

| Issue | Cause | Solution |
|-------|-------|----------|
| "Invalid redirect_uri" | Callback URL mismatch | Verify callback URL in GitHub OAuth App |
| "Client ID not found" | Missing env var | Ensure `AUTH_GITHUB_CLIENT_ID` is exported |
| CORS errors | Origin mismatch | Check `backend.cors.origin` |
| "Failed to sign-in" | Missing user in catalog | Add user to `examples/org.yaml` |
| "EADDRINUSE port 7007" | Port in use | Run `kill $(lsof -t -i:7007)` |

</details>

---

## Requirements

- [Task](https://taskfile.dev/)
- [Node.js](https://nodejs.org/) 22.x or 24.x
- [yarn](https://yarnpkg.com/) 4.x+
- [Docker](https://www.docker.com/) (for TechDocs)
