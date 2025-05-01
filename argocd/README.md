# ArgoCD

## Install with argo-cd community Helmchart

```
helm repo add argo-cd https:// .. 
helm install argocd argo-cd/argo-cd --namespace argocd --values ./values.yaml
```

helm install argocd argo/argo-cd --values ./values.yaml -n argocd --create-namespace
