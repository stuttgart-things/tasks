# CONFIG


<details><summary><b>CROSSPLANE - EXTERNAL CLUSTER PROVIDER CONFIGURATION</b></summary>

```bash
apiVersion: v1
kind: Secret
metadata:
  name: kubeconfig-cicd
  namespace: crossplane-system
data:
  sthings-cicd: <KUBECONFIG-BASE64>
type: Opaque
```

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
