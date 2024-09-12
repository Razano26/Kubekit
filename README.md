# Kubekit

This repository provides a straightforward approach to deploying essential services on a kubernetes cluster. Below is a list of applications, their descriptions, relevant links, and the current documentation status.

## Applications
| Application | Description | Source | Status |
|-------------|-------------|---------------|--------|
| [ArgoCD](./argocd) | Declarative GitOps CD for Kubernetes | [Site](https://argoproj.github.io/cd/) | ✅ |
| [Cert Manager](./cert-manager) | Automates the management and issuance of TLS certificates | [Site](https://cert-manager.io/) | ✅ |
| [Countdown](./countdown) | A simple web countdown timer | [GitHub](https://github.com/Yooooomi/easy-countdown) | 🔄 |
| [GitHub Actions Runner Controller](./github/runners) | Manages self-hosted GitHub Actions runners | [Documentation](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/about-actions-runner-controller) | ❌ |
| [Grafana](./monitoring/grafana) | Open platform for beautiful analytics and monitoring | [Site](https://grafana.com/) | 🔄 |
| [Harbor](./registry) | A registry for various artifacts | [Site](https://goharbor.io/) | ✅ |
| [GitLab Runner](./gitlab/runner) | Self-hosted runner for GitLab CI/CD | [Documentation](https://docs.gitlab.com/runner/install/kubernetes.html#upgrading-gitlab-runner-using-the-helm-chart) | ✅ |
| [Loki](./monitoring/loki) | Log aggregation system like Prometheus, but for logs | [Site](https://grafana.com/oss/loki/) | ❌ |
| [Prometheus](./monitoring/prometheus) | Monitoring system and time series database | [Site](https://prometheus.io/) | 🔄 |
| [Sealed Secrets](./sealed-secrets) | Kubernetes controller and tool for one-way encrypted Secrets | [GitHub](https://github.com/bitnami-labs/sealed-secrets) | ✅ |
| [Trivy](./trivy) | A simple and comprehensive vulnerability scanner for containers | [Site](https://trivy.dev/) | ✅ |
| [Uptime Kuma](./uptime) | Simple uptime monitor | [GitHub](https://github.com/louislam/uptime-kuma) | 🔄 |

## Status Key

- ✅: Written documentation
- 🔄: Partially written documentation
- ❌: Documentation not drafted

Feel free to explore the links provided for more details on each application.
