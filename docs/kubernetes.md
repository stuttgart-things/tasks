# Kubernetes Tasks

Kubernetes and Crossplane operations.

## Taskfiles

- `kubernetes/` - Kubernetes task collection

---

## Crossplane Configuration

<details>
<summary><b>External Cluster Provider Configuration</b></summary>

### Create Kubeconfig Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: kubeconfig-cicd
  namespace: crossplane-system
data:
  sthings-cicd: <KUBECONFIG-BASE64>
type: Opaque
```

### Create ProviderConfig

```bash
kubectl apply -f - <<EOF
apiVersion: helm.crossplane.io/v1beta1
kind: ProviderConfig
metadata:
  name: cicd
spec:
  credentials:
    source: Secret
    secretRef:
      name: kubeconfig-cicd
      namespace: crossplane-system
      key: sthings-cicd
EOF
```

</details>

---

## Usage

<details>
<summary><b>Remote Usage</b></summary>

```bash
export TASK_X_REMOTE_TASKFILES=1

# List available tasks
task https://raw.githubusercontent.com/stuttgart-things/tasks/main/kubernetes/<taskfile>.yaml --list
```

</details>

<details>
<summary><b>Local Usage</b></summary>

```bash
# List available tasks
task --taskfile kubernetes/<taskfile>.yaml --list
```

</details>

---

## Requirements

- [Task](https://taskfile.dev/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Crossplane](https://crossplane.io/) (for Crossplane tasks)
