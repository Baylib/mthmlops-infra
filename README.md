# MThMLOps infrastructure
Within this repository lies all configuration and IAC definition of the MThMLOps project infrastructure.

## Requirements

### ArgoCD

To facilitate deployment our infrastructure is deployed using ArgoCD.
If you don't already use ArgoCD, you can install ArgoCD from argocd directory.

[View the README for the ArgoCD installation](./argocd/README.md)

## Installation

To ease the installation process we use the 
[app of apps patterns](https://argo-cd.readthedocs.io/en/latest/operator-manual/cluster-bootstrapping/#app-of-apps-pattern).
To install all the infrastructure install the mthmlops project and app-of-apps.

```bash
kubectl apply -f ./mthmlops-app-project.yaml
kubectl apply -f ./mthmlops-app-of-apps.yaml
kubectl apply -f ./mthmlops-repo.yaml # requires a personal access token or ssh key to this private repository
```

### Kubeflow-pipelines patch

To use kubeflow pipeline standalone it requires to patch the pipeline-runner role after the deployment.
The ArgoCD application is configured to ignore this difference.

```bash
kubectl patch role pipeline-runner -n kubeflow --type='json' -p='[
{
    "op": "add",
    "path": "/rules/-",
    "value": {
        "apiGroups": ["argoproj.io"],
        "resources": ["workflowtaskresults"],
        "verbs": ["get", "list", "create", "watch", "patch"]
    }
}]'
 ```