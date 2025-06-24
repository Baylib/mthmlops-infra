# ArgoCD

## Install with argo-cd community Helmchart

```
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo-cd/argo-cd --namespace argocd --values ./values.yaml --create-namespace
```
