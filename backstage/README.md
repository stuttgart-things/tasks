# BACKSTAGE

## INIT

<details>
<summary><b>Prerequisites</b></summary>

| Tool | Minimum Version | Check Command |
|------|-----------------|---------------|
| Node.js | 18.x or 20.x | `node --version` |
| npm | 9.x+ | `npm --version` |
| yarn | 1.22+ | `yarn --version` |
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
yarn dev
```

The scaffolder will prompt for:
- **App name**: Name of your Backstage instance
- **Database**: SQLite (default) or PostgreSQL

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
└── examples/                 # Example catalog entities
```

</details>

---

## GITHUB AUTH SETUP

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
| **Homepage URL** | `http://localhost:3000` | `https://backstage.example.com` |
| **Application description** | (optional) | (optional) |
| **Authorization callback URL** | `http://localhost:7007/api/auth/github/handler/frame` | `https://backstage-backend.example.com/api/auth/github/handler/frame` |

### After Creating

1. Note your **Client ID** (displayed immediately)
2. Click **Generate a new client secret**
3. Copy and save the **Client Secret** (shown only once!)

> **Important**: The callback URL must end with `/api/auth/github/handler/frame`

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
# Custom URLs (optional - defaults to localhost if not set)
export BACKSTAGE_FRONTEND_URL=https://backstage.example.com
export BACKSTAGE_BACKEND_URL=https://backstage-backend.example.com
```

### Persistent Configuration

Add to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.):

```bash
# Backstage GitHub Auth
export AUTH_GITHUB_CLIENT_ID=your-client-id
export AUTH_GITHUB_CLIENT_SECRET=your-client-secret

# Optional: Custom FQDN
# export BACKSTAGE_FRONTEND_URL=https://backstage.example.com
# export BACKSTAGE_BACKEND_URL=https://backstage-backend.example.com
```

Or use a `.env` file with [direnv](https://direnv.net/):

```bash
# .envrc (in backstage app directory)
export AUTH_GITHUB_CLIENT_ID=your-client-id
export AUTH_GITHUB_CLIENT_SECRET=your-client-secret
```

</details>

<details>
<summary><b>Step 3: Configure Backstage Files</b></summary>

### Option A: Use the Task (Automated)

```bash
# Navigate to tasks directory
cd /home/sthings/projects/tasks

# Run configuration task (interactive)
task github-auth:configure

# Verify configuration
task github-auth:verify
```

### Option B: Manual Configuration

Make the following changes to your Backstage instance:

---

#### File 1: `app-config.yaml`

Add GitHub provider under `auth.providers`:

```yaml
# app-config.yaml

app:
  title: Backstage
  # Support custom FQDN via env var, defaults to localhost
  baseUrl: ${BACKSTAGE_FRONTEND_URL:-http://localhost:3000}

backend:
  # Support custom FQDN via env var, defaults to localhost
  baseUrl: ${BACKSTAGE_BACKEND_URL:-http://localhost:7007}
  # ... other backend config ...
  cors:
    origin: ${BACKSTAGE_FRONTEND_URL:-http://localhost:3000}
    methods: [GET, HEAD, PATCH, POST, PUT, DELETE]
    credentials: true

auth:
  providers:
    guest: {}
    github:
      development:
        clientId: ${AUTH_GITHUB_CLIENT_ID}
        clientSecret: ${AUTH_GITHUB_CLIENT_SECRET}
```

---

#### File 2: `packages/backend/src/index.ts`

Add the GitHub auth module:

```typescript
// packages/backend/src/index.ts

// ... existing imports ...

// auth plugin
backend.add(import('@backstage/plugin-auth-backend'));
backend.add(import('@backstage/plugin-auth-backend-module-guest-provider'));
// Add this line:
backend.add(import('@backstage/plugin-auth-backend-module-github-provider'));

// ... rest of file ...
```

---

#### File 3: `packages/app/src/App.tsx`

Add `'github'` to the SignInPage providers:

```typescript
// packages/app/src/App.tsx

const app = createApp({
  apis,
  bindRoutes({ bind }) {
    // ... route bindings ...
  },
  components: {
    SignInPage: props => (
      <SignInPage {...props} auto providers={['guest', 'github']} />
    ),
  },
});
```

</details>

<details>
<summary><b>Step 4: Start Backstage</b></summary>

```bash
# Navigate to your Backstage instance
cd /path/to/your/backstage-app

# Ensure environment variables are set
echo "Client ID: $AUTH_GITHUB_CLIENT_ID"
echo "Client Secret: ${AUTH_GITHUB_CLIENT_SECRET:+[SET]}"

# Start development server
yarn dev
```

Access at: http://localhost:3000 (or your custom FQDN)

You should see both **Guest** and **GitHub** options on the sign-in page.

</details>

<details>
<summary><b>Troubleshooting</b></summary>

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| "Invalid redirect_uri" | Callback URL mismatch | Verify callback URL in GitHub OAuth App matches exactly |
| "Client ID not found" | Missing env var | Ensure `AUTH_GITHUB_CLIENT_ID` is exported |
| CORS errors | Origin mismatch | Check `backend.cors.origin` matches frontend URL |
| "Not found" on callback | Wrong callback path | Ensure URL ends with `/api/auth/github/handler/frame` |

### Verify Configuration

```bash
# Using the task
cd /home/sthings/projects/tasks
task github-auth:verify

# Manual verification
grep -r "github" app-config.yaml
grep -r "github-provider" packages/backend/src/index.ts
grep -r "'github'" packages/app/src/App.tsx
```

### Debug Mode

Start with debug logging:

```bash
LOG_LEVEL=debug yarn dev
```

</details>

---

## Task Reference

### Init Tasks (`init.yaml`)

```bash
# Check prerequisites
task -t backstage/init.yaml check-prerequisites

# Install yarn
task -t backstage/init.yaml install-yarn

# Scaffold new instance (interactive)
task -t backstage/init.yaml scaffold-new-instance

# Scaffold in current directory
task -t backstage/init.yaml scaffold-new-instance-here

# Full init (yarn + scaffold)
task -t backstage/init.yaml init
```

### GitHub Auth Tasks (`github-auth.yaml`)

```bash
# Show setup instructions (localhost)
task -t backstage/github-auth.yaml info

# Show setup instructions (custom FQDN - interactive)
task -t backstage/github-auth.yaml info-fqdn

# Apply GitHub auth config to instance (interactive)
task -t backstage/github-auth.yaml configure

# Update URLs for custom FQDN
task -t backstage/github-auth.yaml configure-urls

# Verify GitHub auth is configured
task -t backstage/github-auth.yaml verify

# Full setup (configure + info)
task -t backstage/github-auth.yaml full-setup
```

---

## Quick Reference

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `AUTH_GITHUB_CLIENT_ID` | Yes | - | GitHub OAuth App Client ID |
| `AUTH_GITHUB_CLIENT_SECRET` | Yes | - | GitHub OAuth App Client Secret |
| `BACKSTAGE_FRONTEND_URL` | No | `http://localhost:3000` | Frontend base URL |
| `BACKSTAGE_BACKEND_URL` | No | `http://localhost:7007` | Backend base URL |
| `GITHUB_TOKEN` | No | - | PAT for catalog GitHub integration |

### Files Modified for GitHub Auth

| File | Change |
|------|--------|
| `app-config.yaml` | Add `auth.providers.github` section |
| `packages/backend/src/index.ts` | Add `@backstage/plugin-auth-backend-module-github-provider` |
| `packages/app/src/App.tsx` | Add `'github'` to SignInPage providers |
