# BACKSTAGE

## INIT

<details>
<summary><b>Prerequisites</b></summary>

| Tool | Minimum Version | Check Command |
|------|-----------------|---------------|
| Node.js | 22.x or 24.x | `node --version` |
| npm | 9.x+ | `npm --version` |
| yarn | 4.x+ | `yarn --version` |
| Docker | (for TechDocs) | `docker --version` |

```bash
# Check all prerequisites at once
task -t /home/sthings/projects/tasks/backstage/init.yaml check-prerequisites
```

</details>

<details>
<summary><b>Install Yarn</b></summary>

```bash
# Install yarn globally via npm
npm install --global yarn

# Or use the task
task -t /home/sthings/projects/tasks/backstage/init.yaml install-yarn
```

</details>

<details>
<summary><b>Scaffold New Instance</b></summary>

```bash
# Option 1: Interactive scaffold (prompts for directory)
task -t /home/sthings/projects/tasks/backstage/init.yaml scaffold-new-instance

# Option 2: Scaffold in current directory
task -t /home/sthings/projects/tasks/backstage/init.yaml scaffold-new-instance-here

# Option 3: Manual scaffold
npx @backstage/create-app@latest
cd <app-name>
yarn start
```

The scaffolder will prompt for:
- **App name**: Name of your Backstage instance
- **Database**: SQLite (default) or PostgreSQL

> **Note**: Use `yarn start` (not `yarn dev`) to start the application.

</details>

<details>
<summary><b>Directory Structure</b></summary>

After scaffolding, the directory structure looks like:

```
<app-name>/
├── app-config.yaml           # Main configuration
├── app-config.local.yaml     # Local overrides (gitignored)
├── app-config.production.yaml
├── catalog-info.yaml
├── package.json
├── packages/
│   ├── app/                  # Frontend React application
│   │   ├── src/
│   │   │   ├── App.tsx       # Main app with routes & SignInPage
│   │   │   ├── apis.ts       # API configurations
│   │   │   └── components/   # UI components
│   │   └── package.json
│   └── backend/              # Backend Node.js application
│       ├── src/
│       │   └── index.ts      # Backend plugins registration
│       ├── Dockerfile
│       └── package.json
├── plugins/                  # Custom plugins (empty initially)
└── examples/
    ├── entities.yaml         # Example catalog entities
    └── org.yaml              # Example users and groups
```

</details>

---

## GITHUB AUTH SETUP

Choose your authentication mode:

| Mode | Use Case | User Management |
|------|----------|-----------------|
| **Single User** | Personal/development use | Manual - add users to `examples/org.yaml` |
| **Organization** | Team/production use | Automatic - syncs users from GitHub org |

---

### Option A: Single User Mode

<details>
<summary><b>Step 1: Create GitHub OAuth App</b></summary>

### Navigate to GitHub Developer Settings

1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. Click **OAuth Apps** in the left sidebar
3. Click **New OAuth App**

### Fill in OAuth App Details

| Field | Value (localhost) | Value (custom FQDN) |
|-------|-------------------|---------------------|
| **Application name** | `Backstage` | `Backstage` |
| **Homepage URL** | `http://localhost:3000` | `http://your-host.example.com:3000` |
| **Application description** | (optional) | (optional) |
| **Authorization callback URL** | `http://localhost:7007/api/auth/github/handler/frame` | `http://your-host.example.com:7007/api/auth/github/handler/frame` |

### After Creating

1. Note your **Client ID** (displayed immediately)
2. Click **Generate a new client secret**
3. Copy and save the **Client Secret** (shown only once!)

> **Important**: The callback URL must end with `/api/auth/github/handler/frame`

> **Note for FQDN**: Frontend and backend must use different ports (e.g., `:3000` and `:7007`)

</details>

<details>
<summary><b>Step 2: Set Environment Variables</b></summary>

### Required Variables

```bash
# GitHub OAuth credentials (required)
export AUTH_GITHUB_CLIENT_ID=<your-client-id>
export AUTH_GITHUB_CLIENT_SECRET=<your-client-secret>
```

### Optional: Custom FQDN

```bash
# Custom URLs - must have different ports!
export BACKSTAGE_FRONTEND_URL=http://your-host.example.com:3000
export BACKSTAGE_BACKEND_URL=http://your-host.example.com:7007
```

### Persistent Configuration

Add to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.):

```bash
# Backstage GitHub Auth
export AUTH_GITHUB_CLIENT_ID=your-client-id
export AUTH_GITHUB_CLIENT_SECRET=your-client-secret

# Optional: Custom FQDN (must have different ports)
# export BACKSTAGE_FRONTEND_URL=http://your-host.example.com:3000
# export BACKSTAGE_BACKEND_URL=http://your-host.example.com:7007
```

</details>

<details>
<summary><b>Step 3: Add User to Catalog</b></summary>

GitHub auth requires a matching user entity in the Backstage catalog. Add your GitHub username to `examples/org.yaml`:

```yaml
# examples/org.yaml
---
apiVersion: backstage.io/v1alpha1
kind: User
metadata:
  name: your-github-username  # Must match your GitHub username exactly
spec:
  memberOf: [guests]
```

> **Important**: The `name` field must match your GitHub username exactly (case-sensitive).

</details>

<details>
<summary><b>Step 4: Configure Backstage Files (Single User)</b></summary>

### Option A: Use the Task (Automated)

```bash
cd /home/sthings/projects/tasks
task github-auth:configure
task github-auth:verify
```

### Option B: Manual Configuration

#### File 1: `app-config.yaml`

```yaml
auth:
  providers:
    guest: {}
    github:
      development:
        clientId: ${AUTH_GITHUB_CLIENT_ID}
        clientSecret: ${AUTH_GITHUB_CLIENT_SECRET}
        signIn:
          resolvers:
            - resolver: usernameMatchingUserEntityName
```

#### File 2: `packages/backend/src/index.ts`

```typescript
// auth plugin
backend.add(import('@backstage/plugin-auth-backend'));
backend.add(import('@backstage/plugin-auth-backend-module-guest-provider'));
backend.add(import('@backstage/plugin-auth-backend-module-github-provider'));
```

#### File 3: `packages/app/src/App.tsx`

```typescript
import { githubAuthApiRef } from '@backstage/core-plugin-api';

// In createApp:
components: {
  SignInPage: props => (
    <SignInPage
      {...props}
      auto
      providers={[
        'guest',
        {
          id: 'github-auth-provider',
          title: 'GitHub',
          message: 'Sign in using GitHub',
          apiRef: githubAuthApiRef,
        },
      ]}
    />
  ),
},
```

#### File 4: `examples/org.yaml`

```yaml
---
apiVersion: backstage.io/v1alpha1
kind: User
metadata:
  name: your-github-username
spec:
  memberOf: [guests]
```

</details>

---

### Option B: GitHub Organization Mode

<details>
<summary><b>Step 1: Create GitHub OAuth App (same as Single User)</b></summary>

Follow the same steps as Single User Mode above.

</details>

<details>
<summary><b>Step 2: Set Environment Variables</b></summary>

### Required Variables

```bash
# GitHub OAuth credentials (required)
export AUTH_GITHUB_CLIENT_ID=<your-client-id>
export AUTH_GITHUB_CLIENT_SECRET=<your-client-secret>

# GitHub organization name (required for org mode)
export GITHUB_ORG=your-org-name

# GitHub token with read:org scope (required for org sync)
export GITHUB_TOKEN=ghp_xxxx
```

### GitHub Token Requirements

Your `GITHUB_TOKEN` (Personal Access Token) needs these scopes:
- `read:org` - to read organization members
- `read:user` - to read user information
- `repo` (optional) - for private repository access

### Optional: Custom FQDN

```bash
export BACKSTAGE_FRONTEND_URL=http://your-host.example.com:3000
export BACKSTAGE_BACKEND_URL=http://your-host.example.com:7007
```

</details>

<details>
<summary><b>Step 3: Install GitHub Org Package</b></summary>

```bash
cd /path/to/your/backstage-app
yarn --cwd packages/backend add @backstage/plugin-catalog-backend-module-github-org
```

</details>

<details>
<summary><b>Step 4: Configure Backstage Files (Organization)</b></summary>

### Option A: Use the Task (Automated)

```bash
cd /home/sthings/projects/tasks
task github-auth:configure-org
task github-auth:verify
```

### Option B: Manual Configuration

#### File 1: `app-config.yaml`

```yaml
auth:
  providers:
    guest: {}
    github:
      development:
        clientId: ${AUTH_GITHUB_CLIENT_ID}
        clientSecret: ${AUTH_GITHUB_CLIENT_SECRET}
        signIn:
          resolvers:
            # Try to match existing catalog user first
            - resolver: usernameMatchingUserEntityName
            # Fallback resolvers for org members
            - resolver: emailLocalPartMatchingUserEntityName
            - resolver: emailMatchingUserEntityProfileEmail

catalog:
  # ... existing config ...
  # GitHub organization provider - syncs users and teams
  providers:
    githubOrg:
      id: production
      githubUrl: https://github.com
      orgs:
        - ${GITHUB_ORG}
      schedule:
        frequency: { hours: 1 }
        timeout: { minutes: 10 }
```

#### File 2: `packages/backend/src/index.ts`

```typescript
// auth plugin
backend.add(import('@backstage/plugin-auth-backend'));
backend.add(import('@backstage/plugin-auth-backend-module-guest-provider'));
backend.add(import('@backstage/plugin-auth-backend-module-github-provider'));

// catalog plugin
backend.add(import('@backstage/plugin-catalog-backend'));
backend.add(import('@backstage/plugin-catalog-backend-module-scaffolder-entity-model'));
// GitHub organization entity provider - syncs users/teams from GitHub org
backend.add(import('@backstage/plugin-catalog-backend-module-github-org'));
```

#### File 3: `packages/app/src/App.tsx`

Same as Single User Mode - add `githubAuthApiRef` to SignInPage.

</details>

---

### Step 5: Start Backstage

```bash
# Navigate to your Backstage instance
cd /path/to/your/backstage-app

# Ensure environment variables are set
echo "Client ID: $AUTH_GITHUB_CLIENT_ID"
echo "Client Secret: ${AUTH_GITHUB_CLIENT_SECRET:+[SET]}"
echo "GitHub Org: ${GITHUB_ORG:-not set}"
echo "GitHub Token: ${GITHUB_TOKEN:+[SET]}"

# Kill any existing processes on the ports
kill $(lsof -t -i:7007 -i:3000) 2>/dev/null

# Start development server
yarn start
```

Access at: http://localhost:3000 (or your custom FQDN with port)

---

<details>
<summary><b>Troubleshooting</b></summary>

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| "Invalid redirect_uri" | Callback URL mismatch | Verify callback URL in GitHub OAuth App matches exactly |
| "Client ID not found" / "provider is misconfigured" | Missing env var | Ensure `AUTH_GITHUB_CLIENT_ID` and `AUTH_GITHUB_CLIENT_SECRET` are exported |
| CORS errors | Origin mismatch | Check `backend.cors.origin` matches frontend URL |
| "Not found" on callback | Wrong callback path | Ensure URL ends with `/api/auth/github/handler/frame` |
| "Failed to sign-in, unable to resolve user identity" | Missing user in catalog | Add user to `examples/org.yaml` or enable org sync |
| "Cannot destructure property 'Component'" | Wrong provider format | Use `githubAuthApiRef` object format, not string `'github'` |
| "Conflict between app baseUrl and backend baseUrl" | Same URL for both | Use different ports (`:3000` and `:7007`) |
| "EADDRINUSE port 7007" | Port already in use | Run `kill $(lsof -t -i:7007)` before starting |
| "ERR_MODULE_NOT_FOUND github-org" | Missing package | Run `yarn --cwd packages/backend add @backstage/plugin-catalog-backend-module-github-org` |
| Org users not syncing | Missing/invalid token | Ensure `GITHUB_TOKEN` has `read:org` scope |

### Verify Configuration

```bash
# Using the task
cd /home/sthings/projects/tasks
task github-auth:verify

# Manual verification
grep -A5 "github:" app-config.yaml
grep "github-provider" packages/backend/src/index.ts
grep "githubAuthApiRef" packages/app/src/App.tsx
```

### Debug Mode

```bash
LOG_LEVEL=debug yarn start
```

### Check Environment Variables

```bash
echo "CLIENT_ID: $AUTH_GITHUB_CLIENT_ID"
echo "CLIENT_SECRET: ${AUTH_GITHUB_CLIENT_SECRET:+[SET]}"
echo "GITHUB_ORG: ${GITHUB_ORG:-not set}"
echo "GITHUB_TOKEN: ${GITHUB_TOKEN:+[SET]}"
echo "FRONTEND_URL: ${BACKSTAGE_FRONTEND_URL:-http://localhost:3000}"
echo "BACKEND_URL: ${BACKSTAGE_BACKEND_URL:-http://localhost:7007}"
```

</details>

---

## Task Reference

### Init Tasks (`init.yaml`)

```bash
task -t backstage/init.yaml check-prerequisites
task -t backstage/init.yaml install-yarn
task -t backstage/init.yaml scaffold-new-instance
task -t backstage/init.yaml scaffold-new-instance-here
task -t backstage/init.yaml init
```

### GitHub Auth Tasks (`github-auth.yaml`)

```bash
# Information
task -t backstage/github-auth.yaml info              # Show setup instructions
task -t backstage/github-auth.yaml info-fqdn         # Show setup with custom FQDN

# Single User Mode
task -t backstage/github-auth.yaml configure         # Configure single user auth
task -t backstage/github-auth.yaml add-user          # Add a user to catalog

# Organization Mode
task -t backstage/github-auth.yaml configure-org     # Configure org auth with user sync

# Utilities
task -t backstage/github-auth.yaml configure-urls    # Update URLs for custom FQDN
task -t backstage/github-auth.yaml verify            # Verify configuration

# Full Setup
task -t backstage/github-auth.yaml full-setup        # Single user: configure + add-user + info
task -t backstage/github-auth.yaml full-setup-org    # Org mode: configure-org + info
```

---

## Quick Reference

### Environment Variables

| Variable | Single User | Org Mode | Default | Description |
|----------|-------------|----------|---------|-------------|
| `AUTH_GITHUB_CLIENT_ID` | Required | Required | - | GitHub OAuth App Client ID |
| `AUTH_GITHUB_CLIENT_SECRET` | Required | Required | - | GitHub OAuth App Client Secret |
| `GITHUB_ORG` | - | Required | - | GitHub organization name |
| `GITHUB_TOKEN` | Optional | Required | - | PAT with `read:org` scope |
| `BACKSTAGE_FRONTEND_URL` | Optional | Optional | `http://localhost:3000` | Frontend base URL |
| `BACKSTAGE_BACKEND_URL` | Optional | Optional | `http://localhost:7007` | Backend base URL |

### Files Modified

| File | Single User | Org Mode |
|------|-------------|----------|
| `app-config.yaml` | `auth.providers.github` | `auth.providers.github` + `catalog.providers.githubOrg` |
| `packages/backend/src/index.ts` | `github-provider` | `github-provider` + `github-org` |
| `packages/app/src/App.tsx` | `githubAuthApiRef` | `githubAuthApiRef` |
| `examples/org.yaml` | Add users manually | Auto-synced from GitHub |
| `packages/backend/package.json` | - | Add `@backstage/plugin-catalog-backend-module-github-org` |

### Mode Comparison

| Feature | Single User | Organization |
|---------|-------------|--------------|
| User management | Manual (`org.yaml`) | Automatic sync |
| Setup complexity | Simple | Moderate |
| GitHub Token | Optional | Required (`read:org`) |
| Team sync | No | Yes |
| Best for | Development/personal | Team/production |
