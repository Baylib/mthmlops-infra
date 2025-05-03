# MThMLOps infrastructure
Within this repository lies all configuration and IAC definition of the MThMLOps project infrastructure.

## Requirements

### Minikube

The current state of this application is designed to work on minikube.
At the moment some changes are still required to be able to run on openshift (anyuid, routes,...)

### ArgoCD

To facilitate deployment our infrastructure is deployed using ArgoCD.
If you don't already use ArgoCD, you can install ArgoCD from argocd directory.

[View the README for the ArgoCD installation](./argocd/README.md)

## Installation

### Pre-installation

Some secrets must be present on the cluster.

Kubeflow requires a webhook-server-tls certificate
```bash
openssl req -x509 -newkey rsa:2048 -keyout webhook-server.key -out webhook-server.crt -days 365 -nodes
kubectl create secret tls webhook-server-tls --cert=webhook-server.crt --key=webhook-server.key --namespace=kubeflow
```

ArgoCD will require a git personal access token or ssh key to access private GitHub repositories.

### Installation

To ease the installation process we use the 
[app of apps patterns](https://argo-cd.readthedocs.io/en/latest/operator-manual/cluster-bootstrapping/#app-of-apps-pattern).
To install all the infrastructure install the mthmlops project, app-of-apps and repo.

```bash
kubectl apply -f ./mthmlops-app-project.yaml
kubectl apply -f ./mthmlops-app-of-apps.yaml
kubectl apply -f ./mthmlops-repo.yaml # requires a personal access token or ssh key to this private repository
```

To make any change to namespaces fork this repository.
At the moment kubeflow pipelines requires to make changes in their manifests definition in their git repository to change the namespace.
That's why we kept the default kubeflow namespace.

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