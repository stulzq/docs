---
type: docs
title: "Memcached"
linkTitle: "Memcached"
description: Detailed information on the Memcached state store component
---

## Component format

To setup Memcached state store create a component of type `state.memcached`. To setup SQL Server state store create a component of type `state.sqlserver`. See [this guide]({{< ref "howto-get-save-state.md#step-1-setup-a-state-store" >}}) on how to create and apply a state store configuration.

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: <NAME>
  namespace: <NAMESPACE>
spec:
  type: state.memcached
  version: v1
  metadata:
  - name: hosts
    value: <REPLACE-WITH-COMMA-DELIMITED-ENDPOINTS> # Required. Example: "memcached.default.svc.cluster.local:11211"
  - name: maxIdleConnections
    value: <REPLACE-WITH-MAX-IDLE-CONNECTIONS> # Optional. default: "2"
  - name: timeout
    value: <REPLACE-WITH-TIMEOUT> # Optional. default: "1000ms" Example: "memcached.default.svc.cluster.local:11211"
  - name: maxIdleConnections
    value: <REPLACE-WITH-MAX-IDLE-CONNECTIONS> # Optional. default: "2"
  - name: timeout
    value: <REPLACE-WITH-TIMEOUT> # Optional. default: "1000ms"
```

{{% alert title="Warning" color="warning" %}}
以上示例将 Secret 明文存储。 The example configuration shown above, contain a username and password as plain-text strings. 更推荐的方式是使用 Secret 组件， [here]({{< ref component-secrets.md >}}})。
{{% /alert %}}

## Spec metadata fields

| 字段                 | Required | Details                                                                      | Example                                       |
| ------------------ |:--------:| ---------------------------------------------------------------------------- | --------------------------------------------- |
| hosts              |    Y     | Comma delimited endpoints                                                    | `"memcached.default.svc.cluster.local:11211"` |
| maxIdleConnections |    N     | The max number of idle connections. Defaults to `"2"` Defaults to `"2"`      | `"3"`                                         |
| timeout            |    N     | The timeout for the calls. The timeout for the calls. Defaults to `"1000ms"` | `"1000ms"`                                    |

## Setup Memcached

{{< tabs "Self-Hosted" "Kubernetes" >}}

{{% codetab %}}
You can run Memcached locally using Docker:

```
docker run --name my-memcache -d memcached
```

You can then interact with the server using `localhost:11211`.
{{% /codetab %}}

{{% codetab %}}
The easiest way to install Memcached on Kubernetes is by using the [Helm chart](https://github.com/helm/charts/tree/master/stable/memcached):

```
helm install memcached stable/memcached
```

This installs Memcached into the `default` namespace. This installs Memcached into the `default` namespace. To interact with Memcached, find the service with: `kubectl get svc memcached`.

For example, if installing using the example above, the Memcached host address would be:

`memcached.default.svc.cluster.local:11211`
{{% /codetab %}}

{{< /tabs >}}

## 相关链接
- [Basic schema for a Dapr component]({{< ref component-schema >}})
- Read [this guide]({{< ref "howto-get-save-state.md#step-2-save-and-retrieve-a-single-state" >}}) for instructions on configuring state store components
- [State management building block]({{< ref state-management >}})