# Ansible Tasks

Collection of reusable Ansible automation tasks using Dagger.

## Usage

<details>
<summary>🚀 Execute Ansible Playbooks with Dagger</summary>

### RKE2 Single Node Installation

Install RKE2 on a single node with Cilium CNI and LoadBalancer configuration:

```bash
dagger call -m github.com/stuttgart-things/blueprints/vm@v1.50.0 \
  execute-ansible \
  --playbooks "sthings.baseos.setup,sthings.rke.rke2" \
  --hosts "10.31.103.27" \
  --ssh-user=env:SSH_USER \
  --ssh-password=env:SSH_PASSWORD \
  --parameters="install_rke2=true \
    rke2_state=present \
    rke2_k8s_version=1.33.4 \
    rke2_release_kind=rke2r1 \
    cluster_setup=singlenode \
    install_cillium=true \
    deploy_helm_charts=true \
    install_helm_diff=false \
    cilium_lbrange_start_ip=10.31.103.8 \
    cilium_lbrange_stop_ip=10.31.103.8 \
    ingress_service_type=LoadBalancer" \
  --requirements="/home/sthings/projects/ansible/requirements.yaml" \
  --inventoryType="cluster" \
  --progress plain -vv
```

### Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `--playbooks` | `sthings.baseos.setup,sthings.rke.rke2` | Playbooks to execute |
| `--hosts` | `10.31.103.27` | Target host(s) |
| `--ssh-user` | `env:SSH_USER` | SSH username from environment |
| `--ssh-password` | `env:SSH_PASSWORD` | SSH password from environment |
| `--requirements` | Path to requirements.yaml | Ansible Galaxy requirements |
| `--inventoryType` | `cluster` | Inventory type |
| `--progress` | `plain` | Progress output format |

### Ansible Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `install_rke2` | `true` | Install RKE2 |
| `rke2_state` | `present` | Desired state |
| `rke2_k8s_version` | `1.33.4` | Kubernetes version |
| `rke2_release_kind` | `rke2r1` | Release kind |
| `cluster_setup` | `singlenode` | Cluster topology |
| `install_cillium` | `true` | Install Cilium CNI |
| `deploy_helm_charts` | `true` | Deploy Helm charts |
| `cilium_lbrange_start_ip` | `10.31.103.8` | LoadBalancer IP range start |
| `cilium_lbrange_stop_ip` | `10.31.103.8` | LoadBalancer IP range end |
| `ingress_service_type` | `LoadBalancer` | Ingress service type |

</details>

## Requirements

- Dagger CLI installed
- SSH access to target hosts
- Environment variables set:
  - `SSH_USER` - SSH username
  - `SSH_PASSWORD` - SSH password

## Resources

- [Stuttgart Things Blueprints](https://github.com/stuttgart-things/blueprints)
- [Dagger Documentation](https://docs.dagger.io)