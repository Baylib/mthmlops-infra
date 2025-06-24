# ArgoCD

## Minikube Ingress controller and DNS
Add ingress controller to minikube
```
minikube addons enable ingress
minikube ip 
```

Update your /etc/hosts file like so: 
```
<minikube-ip>   argocd.local
<minikube-ip>   airflow.local
<minikube-ip>   kpf.local
<minikube-ip>   minio.local
<minikube-ip>   svm-api.local
```

## Install with argo-cd community Helmchart

```
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo-cd/argo-cd --namespace argocd --values ./values.yaml --create-namespace
```
### ArgoCD initial admin secret 

```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```
