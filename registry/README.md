# Harbor Registry

This setup provide a Harbor registry with an external postgresql database.

Harbor is an open source trusted cloud native registry project that stores, signs, and scans content.
You can use Harbor to store and distribute Docker images, Helm Charts, CNAB bundles, OPAs, and other OCI artifacts.

Disclaimer: In this setup, we will use cert-manager to generate a certificate for the domain name.

## Prerequisites
- A kubernetes cluster
- Helm
- A domain name pointing to the cluster

## Installation

1. Add necessary helm repositories

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add harbor https://helm.goharbor.io
```

2. Create a `psql-values.yaml` file for the postgresql database with the following content:

```yaml
auth:
  username: harbor
  password: STRONG_PASSWORD
  postgresPassword: STRONG_PASSWORD # This value must be the same as the password
  database: registry
```

3. Install the postgresql database

```bash
helm upgrade --install db bitnami/postgresql --values psql-values.yaml --create-namespace --namespace harbor
```

4. Create a `values.yaml` file with the following content:

```yaml
expose:
  tls:
    enabled: true
    certSource: secret
    secret:
      secretName: "harbor-registry-tls"
  ingress:
    hosts:
      core: YOUR_DOMAIN_NAME
    annotations:
      cert-manager.io/cluster-issuer: "letsencrypt-prod"
externalURL: https://YOUR_DOMAIN_NAME
database:
  type: external
  external:
    host: "db-postgresql"
    username: "postgres"
    coreDatabase: "registry"
    existingSecret: "db-postgresql"
```

5. Install the harbor registry

```bash
helm upgrade --install harbor-registry harbor/harbor --values values.yaml --namespace harbor --create-namespace
```

6. Now you can access the harbor registry at `https://YOUR_DOMAIN_NAME` with the default username `admin` and password `Harbor12345`.
7. It's recommended to change the default password after the first login and create users for personal use and not use the admin account.
8. You can also login to the registry using the `docker login YOUR_DOMAIN_NAME` command.