# ArgoCD

This setup provide an ArgoCD operator.

This operator give you the ability to deploy applications from different source (git, helm, ...) and monitor the state of the application.
It also provide a way to sync the application with the source, so you can have a gitops workflow.

In this setup, we use the following tools:
- [ArgoCD](https://argoproj.github.io/argo-cd/)
- [Traefik](https://doc.traefik.io/traefik/)
- [Cert-manager](https://cert-manager.io/)
- [Helm](https://helm.sh/)

## Prerequisites

- A Kubernetes cluster
- Helm 3
- [Cert-manager](../cert-manager/README.md) with a ClusterIssuer

## Installation

1. Add the ArgoCD helm repository

```bash
helm repo add argo https://argoproj.github.io/argo-helm
```

2. Create a `values.yaml` file with the following content:

If you want to use external authentication, you can follow this yaml file: [values_gh_OICD.yaml](./values_gh_OICD.yaml)

```yaml
global:
  domain: argocd.example.com # Change this to your domain
configs:
  params:
    server.insecure: true
server:
  certificate:
    enabled: true
    issuer:
      group: cert-manager.io
      kind: ClusterIssuer
      name: letsencrypt-prod # Change this to your ClusterIssuer
  ingress:
    enabled: true
    ingressClassName: traefik # Change this to your ingress class
    tls: true

```

3. Install the ArgoCD operator

```bash
helm upgrade --install argocd argo/argo-cd --namespace argocd --values values.yaml --create-namespace
```

4. Get the password of the admin user

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

5. Access the ArgoCD dashboard

You can access the ArgoCD dashboard at `https://argocd.example.com` with the username `admin` and the password you get in the previous step.

