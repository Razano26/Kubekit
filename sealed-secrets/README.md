# Sealed-Secrets

This setup will install Sealed Secrets in your cluster. Sealed Secrets is a Kubernetes add-on to encrypt your Secrets with a public key. The Sealed Secrets controller will then decrypt the SealedSecrets and create the original Secret in the target namespace.

In our case, we will use Sealed Secrets to encrypt our Secrets and store them in a Git repository. This way, we can store our Secrets in a secure way and share them with our team members.

## Prerequisites
- A Kubernetes cluster
- Helm

## Installation

1. Add the sealed-secrets helm repository

```bash
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
```

2. Install the sealed-secrets

In case you need to set a custom install, you can read the default values in the [values_default.yaml](./values_default.yaml) file and set the desired values in a custom `values.yaml` file.

```bash
helm upgrade --install sealed-secrets sealed-secrets/sealed-secrets --namespace kube-system
```

## Usage

To create a SealedSecret, you can use the `kubeseal` CLI tool. You can find the installation instructions [here](https://github.com/bitnami-labs/sealed-secrets?tab=readme-ov-file#kubeseal).

To create a SealedSecret from a Secret, you can use the following command:

```bash
kubectl create secret generic my-secret
kubeseal < my-secret.yaml > my-sealed-secret.yaml
```

To decrypt a SealedSecret, you can use the following command:

```bash
kubeseal --recovery-unseal < my-sealed-secret.yaml > my-secret.yaml
```