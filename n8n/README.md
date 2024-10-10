# n8n

This setup provide exlpain how to deploy n8n on a Kubernetes cluster.

This application is a workflow automation tool, it allow you to create a workflow with different nodes (slack, email, http, ...). You can also create your own nodes with a custom code or use the ones provided by the community.

In this setup, we use the following tools:
- [helm](https://helm.sh/)
- [cert-manager](https://cert-manager.io/)
- [Traefik](https://doc.traefik.io/traefik/)

## Prerequisites

- A Kubernetes cluster
- Helm
- [Cert-manager](../cert-manager/README.md) with a ClusterIssuer

## Installation

1. Create a `values.yaml` file with the following content:

```yaml
ingress:
  enabled: true
  annotations: {
    cert-manager.io/cluster-issuer: letsencrypt-prod # Change this value with your ClusterIssuer or Issuer
  }
  hosts:
    - host: n8n.example.com # Change this value with your domain
      paths: []
  tls:
    - secretName: n8n-tls
      hosts:
        - n8n.example.com # Change this value with your domain
  className: "traefik"

persistence:
  enabled: true
  size: 10Gi # Change this value with the size you want
```

2. Install n8n with the following command:

```bash
helm upgrade --install n8n oci://8gears.container-registry.com/library/n8n --namespace n8n --create-namespace -f values.yaml
```

Now you can access to n8n with the domain you have set in the `values.yaml` file.