# Trivy operator

This setup provide a trivy operator.

This opoerator will scan all images in the cluster and create a report if a vulnerability is found in the image.
Reports are are CRD and can be requested by the user.

But for better user experience, all data are also sent to a prometheus instance, so you can create alerts and dashboards and use grafana to visualize the data.

[Example of a dashboard](https://grafana.com/grafana/dashboards/16337/)

## Prerequisites
- A kubernetes cluster
- Helm
- If you want grafana dashboards
  - A prometheus instance
  - A grafana instance

## Installation

1. Add the trivy helm repository

```bash
helm repo add aqua https://aquasecurity.github.io/helm-charts/
```

2. Create a `values.yaml` file with the following content:

```yaml
operator:
  metricsVulnIdEnabled: true # Enable or disable the metrics for the vulnerability ID

service:
  headless: true # If you want to expose the service as a headless service

serviceMonitor:
  enabled: true # If you want to create a service monitor for prometheus
trivy:
  ignoreUnfixed: true # If you want to ignore unfixed vulnerabilities
```

3. Install the trivy operator

```bash
helm upgrade --install trivy-operator aqua/trivy-operator --namespace trivy-system --values trivy_values.yaml 
```

4. Show the vulnerabilities

After few minutes, you should see the vulnerabilities with the following command:

```bash
kubectl get vulnerabilityreports --all-namespaces
```
