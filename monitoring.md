# Gitops Monitoring and Alerting

## What is GitOps and ArgoCD

- Challenge is consistently delivering application across multi-cluster environments
- Gitops is a declaritive approach to addressing this.  Application configuration is stored in Git, which is treated as the single source of truth.
- Allows config to be version controlled, auditability.
- ArgoCD is a declarative GitOps continuous delivery tool for kubernetes.  It allows application deployment and lifecycle management to be automated, auditable and easy to understand.
- For OpenShift, the GitOps operator allows for easy installation of one or more argocd instances on the cluster.

## What is Prometheus

Prometheus is an open source monitoring and alerting tool that gathers metrics exposed by software components, and provides the ablility to run queries against these metrics. The metrics can then be visualized using Grafana dashboards. The GitOps Operator automatically configures the OpenShift monitoring stack to scrape metrics from any ArgoCD instance it installs.

What does ArgoCD expose

What can you do with this

What is alerting
