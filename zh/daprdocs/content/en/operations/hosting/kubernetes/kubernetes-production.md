---
type: docs
title: "Guidelines for production ready deployments on Kubernetes"
linkTitle: "Production guidelines"
weight: 10000
description: "Recommendations and practices for deploying Dapr to a Kubernetes cluster in a production ready configuration"
---

## Cluster capacity requirements

For a production ready Kubernetes cluster deployment, it is recommended you run a cluster of 3 worker nodes to support a highly-available setup of the control plane. The Dapr control plane pods are designed to be lightweight and require the following resources in a production-ready setup: Use the following resource settings might serve as a starting point. Requirements will vary depending on cluster size and other factors, so individual testing is needed to find the right values for your environment:

*Note: For more info on CPU and Memory resource units and their meaning, see [this](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes) link*

| Deployment       | CPU                       | Memory                       |
| ---------------- | ------------------------- | ---------------------------- |
| Operator         | Limit: 1, Request: 100m   | Limit: 200Mi, Request: 100Mi |
| Sidecar Injector | Limit: 1, Request: 100m   | Limit: 200Mi, Request: 30Mi  |
| Sentry           | Limit: 1, Request: 100m   | Limit: 200Mi, Request: 30Mi  |
| Placement        | Limit: 1, Request: 250m   | Limit: 500Mi, Request: 100Mi |
| Dashboard        | Limit: 200m, Request: 50m | Limit: 200Mi, Request: 20Mi  |

When installing Dapr using Helm, no default limit/request values are set. Each component has a `resources` option (for example, `dapr_dashboard.resources`), which you can use to tune the Dapr control plane to fit your environment. The [Helm chart readme](https://github.com/dapr/dapr/blob/master/charts/dapr/README) has detailed information and examples. For local/dev installations, you might simply want to skip configuring the `resources` options.

### Optional components

The following Dapr control plane deployments are optional:

* Placement - Needed for Dapr Actors
* Sentry - Needed for mTLS for service to service invocation
* Dashboard - Needed for operational view of the cluster

## Sidecar resource requirements

To change the resource assignments for the Dapr sidecar, see the annotations [here]({{< ref "kubernetes-annotations.md" >}}). The specific annotations related to resource constraints are: The specific annotations related to resource constraints are:

* `dapr.io/sidecar-cpu-limit`
* `dapr.io/sidecar-memory-limit`
* `dapr.io/sidecar-cpu-request`
* `dapr.io/sidecar-memory-request`

If not set, the dapr sidecar will run without resource settings, which may lead to issues. For a production-ready setup it is strongly recommended to configure these settings.

For more details on configuring resource in Kubernetes see [Assign Memory Resources to Containers and Pods](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/) and [Assign CPU Resources to Containers and Pods](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/)

The CPU and memory limits above account for the fact that Dapr is intended to do a lot of high performant I/O bound operations. Based on your app needs, you can increase or decrease those limits accordingly.

| CPU                     | Memory                        |
| ----------------------- | ----------------------------- |
| Limit: 4, Request: 100m | Limit: 4000Mi, Request: 250Mi |

*Note: Since Dapr is intended to do much of the I/O heavy lifting for your app, it's expected that the resources given to Dapr enable you to drastically reduce the resource allocations for the application*

The CPU and memory limits above account for the fact that Dapr is intended to a high number of I/O bound operations. It is strongly recommended that you use a tool monitoring tool to baseline the sidecar (and app) containers and tune these settings based on those baselines.

## Deploying Dapr with Helm

When deploying to a production cluster, it's recommended to use Helm. Although the Dapr CLI can install Dapr onto a Kubernetes cluster, it is intended for use in dev/test scenarios. You can find information [here]({{< ref "install-dapr-selfhost.md#using-helm-advanced" >}}) on how to deploy Dapr using Helm.

When deploying Dapr in a production-ready configuration, it's recommended to deploy with a highly available configuration of the control plane: It is recommended to create a values file instead of specifying parameters on the command-line. This file should be checked in to source control so that you can track changes made to it.


Dapr supports zero downtime upgrades. The upgrade path includes the following steps:

To upgrade the Dapr CLI, [download the latest version](https://github.com/dapr/cli/releases) of the CLI. After you downloaded the binary, it's recommended you put the CLI binary in your path.

```bash
helm install dapr dapr/dapr --version=<Dapr chart version> --namespace dapr-system --set global.ha.enabled=true
```

When upgrading to a new version of Dapr, it is recommended you carry over the root and issuer certificates instead of re-generating them, which might cause a downtime for applications that make use of service invocation or actors.

*Note: The Dapr Helm chart automatically deploys with affinity for nodes with the label `kubernetes.io/os=linux`. You can deploy the Dapr control plane to Windows nodes, but most users should not need to. For more information see [Deploying to a Hybrid Linux/Windows K8s Cluster]({{< ref "kubernetes-hybrid-clusters.md" >}})*

## Upgrading Dapr with Helm

Dapr supports zero downtime upgrades. The upgrade path includes the following steps:

1. Upgrading a CLI version (optional but recommended)
2. Updating the Dapr control plane
3. Updating the data plane (Dapr sidecars)

### Upgrading the CLI

To upgrade the Dapr CLI, [download the latest version](https://github.com/dapr/cli/releases) of the CLI. After you downloaded the binary, it's recommended you put the CLI binary in your path.

### Updating the control plane

Copy the contents of `ca.crt`, `issuer.crt` and `issuer.key` and base64 decode them. Save these certificates as files.

### Updating the data plane (sidecars)

The last step is to update pods that are running Dapr to pick up the new version of the Dapr runtime. To do that, simply issue a rollout restart command for any deployment that has the `dapr.io/enabled` annotation:

```
kubectl rollout restart deploy/<Application deployment name>
```

To see a list of all your Dapr enabled deployments, you can either use the [Dapr Dashboard](https://github.com/dapr/dashboard) or run the following command using the Dapr CLI:

```bash
dapr list -k

APP ID     APP PORT  AGE  CREATED
nodeapp    3000      16h  2020-07-29 17:16.22
```

## Recommended security configuration

When properly configured, Dapr ensures secure communication. It can also make your application more secure with a number of built-in features.

It is recommended that a production-ready deployment includes the following settings:

1. Mutual Authentication (mTLS) should be enabled. Note that Dapr has mTLS on by default. For details on how to bring your own certificates, see [here]({{< ref "mtls.md#bringing-your-own-certificates" >}})

2. App to Dapr API authentication is enabled. This is the communication between your application and the Dapr sidecar. To secure the Dapr API from unauthorized application access, it is recommended to enable Dapr's token based auth. Dapr API authentication is enabled (this is the between your application and the Dapr sidecar). To secure the Dapr API from unauthorized access, it is recommended to enable Dapr's token based auth. See [here]({{< ref "api-token.md" >}}) for details

3. Dapr to App API authentication is enabled. This is the communication between Dapr and your application. This ensures that Dapr knows that it is communicating with an authorized application. See [Authenticate requests from Dapr using token authentication]({{< ref "app-api-token.md" >}}) for details

4. All component YAMLs should have secret data configured in a secret store and not hard-coded in the YAML file. See [here]({{< ref "component-secrets.md" >}}) on how to use secrets with Dapr components

5. The Dapr control plane is installed on a separate namespace such as `dapr-system`, and never into the `default` namespace

6. Dapr also supports scoping components for certain applications. This is not a required practice, and can be enabled according to your security needs. See [here]({{< ref "component-scopes.md" >}}) for more info.


## Tracing and metrics configuration

Dapr has tracing and metrics enabled by default. To configure a tracing backend for Dapr visit [this]({{< ref "setup-tracing.md" >}}) link.

For metrics, Dapr exposes a Prometheus endpoint listening on port 9090 which can be scraped by Prometheus.

It is *recommended* that you set up distributed tracing and metrics for your applications and the Dapr control plane in production. If you already have your own observability set-up, you can disable tracing and metrics for Dapr.

To setup Prometheus, Grafana and other monitoring tools with Dapr, visit [this]({{< ref "monitoring" >}}) link.