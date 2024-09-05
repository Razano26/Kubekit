# Cert-Manager

This setup will install cert-manager in your cluster. Cert-manager is a Kubernetes add-on to automate the management and issuance of TLS certificates from various issuing sources.

In our case, we will use cert-manager to automatically generate certificates for our ingress resources. Cert-manager supports multiple issuers, including Let's Encrypt, Venafi, and others.

Disclaimer: In this setup, we will use traefik as the ingress controller and Let's Encrypt as the certificate issuer.

## Prerequisites
- A Kubernetes cluster
- Helm

## Installation

1. Add the cert-manager helm repository

```bash
helm repo add jetstack https://charts.jetstack.io
```

2. Install the cert-manager

```bash
helm upgrade --install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --set crds.enabled=true
```

3. Verify the installation

```bash
kubectl get pods -n cert-manager
```

Now that cert-manager is installed, we can proceed to create a ClusterIssuer resource to issue certificates for our ingress resources.
You can use the `letsencrypt-prod` or `letsencrypt-staging` cluster issuers provided by cert-manager. The `letsencrypt-staging` issuer is used for testing purposes only.

4. Create a `cluster-issuer.yaml` file with the following content:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: YOUR_EMAIL_ADDRESS # Email address for certificate expiration notifications
    server: https://acme-v02.api.letsencrypt.org/directory # Let's Encrypt production server
    # server: https://acme-staging-v02.api.letsencrypt.org/directory # Let's Encrypt staging server
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: traefik
```

5. Apply the ClusterIssuer resource

```bash
kubectl apply -n cert-manager -f cluster-issuer.yaml
```

The `letsencrypt-prod` ClusterIssuer is now ready to issue certificates for your ingress resources.

You can now proceed to create your ingress resources and specify the `cert-manager.io/cluster-issuer: letsencrypt-prod` annotation to automatically generate certificates for your domains.

You can find an example of an ingress resource with cert-manager annotations in the [examples-ingress.yaml](./examples-ingress.yaml).

For more information on cert-manager, refer to the official documentation: [cert-manager.io](https://cert-manager.io/docs/).
