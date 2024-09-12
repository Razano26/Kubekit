# Grafana

This setup will install Grafana in your cluster. Grafana is an open platform for beautiful analytics and monitoring.

In this setup, we use the following tools:
- [Helm](https://helm.sh/)
- [Traefik](https://doc.traefik.io/traefik/)
- [Cert-manager](https://cert-manager.io/)

## Prerequisites
- A Kubernetes cluster
- Helm

## Installation

1. Add the Grafana helm repository

```bash
helm repo add grafana https://grafana.github.io/helm-charts
```

2. Create a `values.yaml` file with the following content:

If you want to use external authentication, you can follow this yaml file: [values_gh_oauth.yaml](./values_gh_oauth.yaml)

```yaml
ingress:
  enabled: true
  annotations: {
    spec.ingressClassName: traefik,
    cert-manager.io/cluster-issuer: letsencrypt-prod
  }
  hosts:
    - example.com
  tls: 
    - secretName: grafana-tls
      hosts:
        - example.com
persistence:
  enabled: true
```

3. Install Grafana

```bash
helm upgrade --install grafana grafana/grafana --namespace monitoring --values values.yaml --create-namespace
```

4. Get the password of the admin user

```bash
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

5. Access the Grafana dashboard

You can access the Grafana dashboard at `https://example.com` with the username `admin` and the password you get in the previous step.
