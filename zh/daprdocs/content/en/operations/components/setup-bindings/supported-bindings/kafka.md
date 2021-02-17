---
type: docs
title: "Kafka binding spec"
linkTitle: "Kafka"
description: "Detailed documentation on the kafka binding component"
---

## Setup Dapr component

To setup Kafka binding create a component of type `bindings.kafka`. See [this guide]({{< ref "howto-bindings.md#1-create-a-binding" >}}) on how to create and apply a binding configuration.


```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: <NAME>
  namespace: <NAMESPACE>
spec:
  type: bindings.kafka
  version: v1
  metadata:
  - name: topics # Optional. in use for input bindings
    value: topic1,topic2
  - name: brokers
    value: localhost:9092,localhost:9093
  - name: consumerGroup
    value: group1
  - name: publishTopic # Optional. in use for output bindings
    value: topic3
  - name: authRequired # Required. default: "true"
    value: "false"
  - name: saslUsername # Optional.
    value: "user"
  - name: saslPassword # Optional.
    value: "password"
  - name: maxMessageBytes # Optional.
    value: 1024
```

{{% alert title="Warning" color="warning" %}}
The above example uses secrets as plain strings. It is recommended to use a secret store for the secrets as described [here]({{< ref component-secrets.md >}}).
{{% /alert %}}
## Spec metadata fields

| Field           | Required | Binding support | Details                                                                                                                                         | Example                                                                                                   |
| --------------- |:--------:| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| topics          |    N     | Input           | `topics` is a comma separated string of topics for an input binding.                                                                            | `"mytopic1,topic2"`                                                                                       |
| brokers         |    Y     | Input/Output    | `brokers` is a comma separated string of kafka brokers.                                                                                         | `"localhost:9092,localhost:9093"`                                                                         |
| consumerGroup   |    N     | Input           | `consumerGroup` is a kafka consumer group to listen on.                                                                                         | `"group1"`                                                                                                |
| publishTopic    |    Y     | Output          | `publishTopic` is the topic to publish for an output binding.                                                                                   | `"mytopic"`                                                                                               |
| authRequired    |    Y     | Input/Output    | `authRequired` determines whether to use SASL authentication or not. Defaults to `"true"`                                                       | `saslPassword` is the SASL password for authentication. Only used if `authRequired` is set to - `"true"`. |
| saslUsername    |    N     | Input/Output    | The SASL username for authentication. `saslUsername` is the SASL username for authentication. Only used if `authRequired` is set to - `"true"`. | `"user"`                                                                                                  |
| saslPassword    |    N     | Input/Output    | The SASL password for authentication. Only used if `authRequired` is set to - `"true"`                                                          | `"password"`                                                                                              |
| maxMessageBytes |    N     | Input/Output    | The maximum size allowed for a single Kafka message. Defaults to 1024                                                                           | `2048`                                                                                                    |


## Output Binding Supported Operations

This component supports both **input and output** binding interfaces.

This component supports **output binding** with the following operations:

- `create`

## Specifying a partition key

When invoking the Kafka binding, its possible to provide an optional partition key by using the `metadata` section in the request body.

The field name is `partitionKey`.

Example:

```shell
curl -X POST http://localhost:3500/v1.0/bindings/myKafka \
  -H "Content-Type: application/json" \
  -d '{
        "data": {
          "message": "Hi"
        },
        "metadata": {
          "partitionKey": "key1"
        },
        "operation": "create"
      }'
```


## Related links

- [Basic schema for a Dapr component]({{< ref component-schema >}})
- [Bindings building block]({{< ref bindings >}})
- [How-To: Trigger application with input binding]({{< ref howto-triggers.md >}})
- [How-To: Use bindings to interface with external resources]({{< ref howto-bindings.md >}})
- [Bindings API reference]({{< ref bindings_api.md >}})