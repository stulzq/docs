---
type: docs
title: "Name resolution component specs"
linkTitle: "Name resolution"
weight: 5000
description: The supported name resolution providers that interface with Dapr service invocation
no_list: true
---

## Supported name resolution components

The following components provide name resolution for the service invocation building block

### 通用

| Name                                               |  状态   | 组件版本 | 自从  |
| -------------------------------------------------- |:-----:|:----:|:---:|
| [HashiCorp Consul]({{< ref setup-nr-consul.md >}}) | Alpha |  v1  | 1.2 |

### 自托管

| Name | 状态 | 组件版本 | 自从  |
| ---- |:--:|:----:|:---:|
| mDNS | GA |  v1  | 1.0 |

### Kubernetes

| Name       | 状态 | 组件版本 | 自从  |
| ---------- |:--:|:----:|:---:|
| Kubernetes | GA |  v1  | 1.0 |


## Definitions

- **Status**: [component certification]({{<ref "certification-lifecycle.md">}}) status
   - [Alpha]({{<ref "certification-lifecycle.md#alpha">}})
   - [Beta]({{<ref "certification-lifecycle.md#beta">}})
   - [GA]({{<ref "certification-lifecycle.md#general-availability-ga">}})
- **Since**: defines from which Dapr Runtime version, the component is in the current status
- **组件版本**：代表组件的版本