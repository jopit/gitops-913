# Gitops Monitoring and Alerting

## What is GitOps and ArgoCD

One big challenge when following a continuous delivery approach to application development for Kubernetes is consistently delivering applications across multi-cluster environments. Gitops is a declaritive approach to addressing this.  Application configuration is stored in Git, which is treated as the single source of truth. This allows configuration to be version controlled, and provides for auditability of all changes.

The challenge then becomes finding an easy and automated way to continuousl release these changes. ArgoCD is a declarative GitOps continuous delivery tool for Kubernetes.  It allows application deployment and lifecycle management to be automated, auditable and easy to understand.

For OpenShift, the GitOps Operator allows for easy installation of one or more argocd instances on the cluster.

## What is Prometheus

Prometheus is an open source monitoring and alerting tool that gathers metrics exposed by software components, and provides the ablility to run queries against these metrics. The metrics can then be visualized using Grafana dashboards. The GitOps Operator automatically configures the OpenShift monitoring stack to scrape metrics from any ArgoCD instance the operator installs.

Two of the types of metrics supported by Prometheus are **counters** and **guages**

From the documentation

> A **counter** is a cumulative metric that represents a single monotonically increasing counter whose value can only increase or be reset to zero on restart. For example, you can use a counter to represent the number of requests served, tasks completed, or errors.

Note that a counter can never decrease.

> A **gauge** is a metric that represents a single numerical value that can arbitrarily go up and down.
>
> Gauges are typically used for measured values like temperatures or current memory usage, but also "counts" that can go up and down, like the number of concurrent requests.


## What does ArgoCD expose

ArgoCD currently exposes two sets of metrics.  The first is a set of metrics for application managed by argocd

- Gauge for the health status of the application
- Gauge for the sync status of the application
- Counter for the sync history of the application

The second set of metrics concerns the ArgoCD API server request and response activity. It provides information about request totals, response codes etc. and can be used to gather information about the health of ArgoCD itself.

## What is alerting

Prometheus can be configured to raise an alert when some particular metric exceeds a particular value. Out of the box, the GitOps Operator creates an alert rule to raise an alert for ArgoCD  applications that are out of sync. This alert will show up in the OpenShift admin console.

You can add additional alert rules.  For example, to add an alert rule that will fire when an ArgoCD application has a health status of missing, create a file called *health-alert.yaml* with the following contents
```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: argocd-health-alert
spec:
  groups:
  - name: argocd-health-alert
    rules:
    - alert: ArgoCDHealthAlert
      annotations:
        message: |
          Application {{ $labels.name }} has health status Missing
      expr: argocd_app_info{health_status="Missing"} > 0
      labels:
        severity: warning
```
and then run the command `oc apply -f health-alert.yaml -n <namespace>`, where `<namespace>` is the namespace into which ArgoCD is installed.
