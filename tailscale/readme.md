# Tailscale Operator

## Overview

This guide explains how to deploy the Tailscale Operator in a Kubernetes cluster.
With this operator, you can securely access your Kubernetes API server through your Tailscale network, and manage access using Tailscale ACLs.

## Prerequisites

- A kubernetes cluster
- A tailscale account with admin access
- The tailscale desktop app installed on your machine

## Installation

### 1. Update the Access control rules

To begin, you need to update the [Access control](https://login.tailscale.com/admin/acls/file) rules in your tailscale account.
We add 2 new tags:

- `tag:k8s-operator` : This tag is used to scope the OAuth client to the operator.
- `tag:k8s` : This tag is used to scope the ACLs to the kubernetes cluster.

To add the tags, you need to add the following on the `ACLs` section:

```json
{
	"tagOwners": {
		"tag:k8s-operator": [],
		"tag:k8s": ["tag:k8s-operator"]
	},
  "acls": [
		{
			"action": "accept",
			"src":    ["group:admins"],
			"dst":    ["tag:k8s-operator:443"]
		},
    ...
	],
  ...
}
```

### 2. Create a tailscale `OAuth client`

Now you can create a tailscale `OAuth client` in your tailscale account to register the operator.
Go to the [`OAuth client`](https://login.tailscale.com/admin/settings/oauth) page in your tailscale account and create a new client.
The OAuth client needs to be created with the following scopes:

- `auth_keys` : Read and write access to the auth keys. (scope to the tag `tag:k8s-operator`)
- `devices:core` : Read and write access to the devices. (scope to the tag `tag:k8s-operator`)

After creating the OAuth client, note the `client_id` and `client_secret`; you'll need them to install the operator.

### 3. Install the operator

To install the operator, you need to create a `values.yaml` file with the following content:

```yaml
oauth:
  clientId: 'xx' # The client_id you got from the OAuth client
  clientSecret: 'tskey-client-xx-xxx' # The client_secret you got from the OAuth client
apiServerProxyConfig:
  mode: 'true' # Enable the API server proxy
```

Now you can install the operator:

```bash
helm repo add tailscale https://pkgs.tailscale.com/helmcharts # Add the tailscale helm chart repo
helm upgrade --install tailscale-operator tailscale/tailscale-operator --namespace=tailscale --create-namespace --values=values.yaml --wait # Install the operator
```

After the installation, you can check the operator is running:

```bash
kubectl get pods -n tailscale
```

You should see the operator pod running:

```bash
kubectl get pods -n tailscale
NAME                        READY   STATUS    RESTARTS        AGE
operator-85d58cc684-747vg   1/1     Running   0               5m
```

### 4. Update the ACLs to enable the api server proxy

To enable the api server proxy, you need to update the [Access control](https://login.tailscale.com/admin/acls/file) rules in your tailscale account.
We add the `group:admins` group and the right associated tag to the `ACLs` section:

Add to the `ACLs` section the following:

```json
{
	"groups": {
		"group:admins": ["labeyrielouis@gmail.com"]
	},
  "grants": [
		{
			"src": ["group:admins"],
			"dst": ["tag:k8s-operator"],
			"app": {
				"tailscale.com/cap/kubernetes": [
					{
						"impersonate": {
							"groups": ["system:masters"]
						}
					}
				]
			}
		}
	],
  ...
}
```

Now you can use the tailscale CLI to setup the kubeconfig file:

```bash
tailscale configure kubeconfig <operator-hostname> # By default, the hostname for the operator node is tailscale-operator.
```

This will add a cluster in your kubernetes config file, you can check it with:

```bash
kubectl config get-contexts
CURRENT   NAME                                      CLUSTER                     AUTHINFO                          NAMESPACE
*         tailscale-operator                        tailscale-operator          tailscale-auth
          orbstack                                  orbstack                    orbstack
          vps                                       vps                         vps                               default
```

PS: The cluster name can be different, it depends on the operator hostname and network name.

### 5. Connect to the cluster

Now you can connect to the cluster with the tailscale desktop app.
Ensure you're on a different network than the one hosting your Kubernetes cluster.
Connect to your tailscale network. And now you can use the kubernetes CLI to manage the cluster.

```bash
kubectl get nodes
NAME            STATUS   ROLES           AGE     VERSION
talos-czd-5hu   Ready    control-plane   7d11h   v1.32.3
```
