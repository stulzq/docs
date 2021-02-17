---
type: docs
title: "分布式跟踪"
linkTitle: "Distributed tracing"
weight: 1000
description: "使用 Dapr 跟踪获取分布式应用程序的可见性"
---

Dapr 使用 OpenTelemetry（以前称为 OpenCensus）进行分布式跟踪和指标收集。 OpenTelemetry 支持各种后端，包括 [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor/)、 [Datadog](https://www.datadoghq.com)、 [Instana](https://www.instana.com)、 [Jaeger](https://www.jaegertracing.io/)、 [Signal Fx](https://www.signalfx.com/)、 [Stackdriver](https://cloud.google.com/stackdriver)、 [Zipkin](https://zipkin.io) 等。

<img src="/images/tracing.png" width=600>

## 跟踪设计

Dapr 将 HTTP/GRPC Middleware 添加到 Dapr sidecar。 Middleware 拦截所有 Dapr 和应用程序流量，并自动注入关联ID以跟踪分布式事务。 此设计有如下优点：

* 无需代码检测。 自动跟踪所有流量（跟踪级别可配置）。
* 跨微服务的一致跟踪行为。 跟踪是在 Dapr sidecar 上进行配置和管理的，因此它可以在服务之间保持一致，这些服务由不同的团队提供，并可能以不同的编程语言编写。
* 可配置和可扩展。 通过利用 OpenTelmetry，可以将 Dapr 追踪配置为与流行的追踪后端一起使用，包括客户可能具有的自定义后端。
* OpenTelemetry Exporter被定义为 Dapr 组件的一等公民。 可以同时定义和启用多个Exporter。

## W3C Correlation ID

Dapr 使用标准的 W3C 跟踪上下文标头。 对于 HTTP 请求，Dapr 使用 `traceparent` 标头。 对于 gRPC 请求，Dapr 使用 `grpc-trace-bin` 标头。   当请求到达时，如果没有跟踪 ID ，Dapr 将创建一个新的跟踪 ID。 否则，它将沿调用链传递跟踪 ID。

阅读 [W3C 分布式跟踪]({{< ref w3c-tracing >}}) ，了解更多关于 W3C Trace Context 的背景.

## 配置

Dapr 使用 openCensus 定义的 [概率采样](https://opencensus.io/tracing/sampling/probabilistic/) 。 采样率定义跟踪 Span 采样的概率，其值可以在0和1之间（包括）。 默认采样率是0.0001（即每10000个span中采样一个）。

若要更改默认的跟踪行为，请使用配置文件（在自托管模式下）或 Kubernetes 配置对象（在 Kubernetes 模式下）。 例如，以下配置对象将采样率更改为 1（即每个Span都采样），并使用 Zipkin 协议将跟踪发送到位于 http://zipkin.default.svc.cluster.local 的 Zipkin 服务器：

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: tracing
  namespace: default
spec:
  tracing:
    samplingRate: "1"
    zipkin:
      endpointAddress: "http://zipkin.default.svc.cluster.local:9411/api/v2/spans"
```

将 ` samplingRate ` 更改为 0 将完全禁用追踪。

关于如何在本地环境和 Kubernetes 环境中配置追踪的更多细节，请参阅 [参考文档](#references) 部分。

## 参考文档

- [操作方法：使用 OpenTelemetry Collector 为分布式跟踪安装应用程序洞察器]({{< ref open-telemetry-collector.md >}})
- [操作方法: 为分布式跟踪安装 Zipkin]({{< ref zipkin.md >}})
- [W3C 分布式跟踪]({{< ref w3c-tracing >}})