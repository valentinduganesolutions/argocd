# argocd

```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Credentials for web interface
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

# Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

```bash
# First app
k apply -f applications/main-app.yaml
```





