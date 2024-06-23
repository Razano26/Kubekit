# Gitlab runner

This setup provide a Gitlab runner with kubernetes executor.

That means that the runner will be able to run jobs in a kubernetes cluster.
The kubernetes operator will create a pod for each job and the runner will execute the job in that pod.
That means the runner allow you to run multiple jobs in parallel.

## Prerequisites

- A kubernetes cluster
- A gitlab runner authentication token (see [here](https://docs.gitlab.com/runner/register/#register-with-a-runner-authentication-token))
- Helm 

## Installation

1. Add the gitlab helm repository

```bash
helm repo add gitlab https://charts.gitlab.io
```

2. Create a `values.yaml` file with the following content:

```yaml
gitlabUrl: "https://gitlab.com/" # or your gitlab url
runnerToken: "YOUR_RUNNER_TOKEN"
rbac:
  create: true
serviceAccount:
  create: true
```

3. Install the gitlab runner chart

```bash
helm upgrade --install -n gitlab-runner -f values.yaml gitlab-runner gitlab/gitlab-runner --create-namespace
```