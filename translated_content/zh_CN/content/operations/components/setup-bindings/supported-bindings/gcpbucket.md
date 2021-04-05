---
type: docs
title: "GCP Storage Bucket binding spec"
linkTitle: "GCP Storage Bucket"
description: "Detailed documentation on the GCP Storage Bucket binding component"
---

## Component format

To setup GCP Storage Bucket binding create a component of type `bindings.gcp.bucket`. See [this guide]({{< ref "howto-bindings.md#1-create-a-binding" >}}) on how to create and apply a binding configuration. To setup Redis binding create a component of type `bindings.redis`. See [this guide]({{< ref "howto-bindings.md#1-create-a-binding" >}}) on how to create and apply a binding configuration.


```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: <NAME>
  namespace: <NAMESPACE>
spec:
  type: bindings.gcp.bucket
  version: v1
  metadata:
  - name: bucket
    value: mybucket
  - name: type
    value: service_account
  - name: project_id
    value: project_111
  - name: private_key_id
    value: *************
  - name: client_email
    value: name@domain.com
  - name: client_id
    value: '1111111111111111'
  - name: auth_uri
    value: https://accounts.google.com/o/oauth2/auth
  - name: token_uri
    value: https://oauth2.googleapis.com/token
  - name: auth_provider_x509_cert_url
    value: https://www.googleapis.com/oauth2/v1/certs
  - name: client_x509_cert_url
    value: https://www.googleapis.com/robot/v1/metadata/x509/<project-name>.iam.gserviceaccount.com
  - name: private_key
    value: PRIVATE KEY
```

{{% alert title="Warning" color="warning" %}}
以上示例将 Secret 明文存储。 The example configuration shown above, contain a username and password as plain-text strings. 更推荐的方式是使用 Secret 组件， [here]({{< ref component-secrets.md >}}})。
{{% /alert %}}

## Spec metadata fields

| 字段                              | Required | Binding support | Details                                                                    | Example                                                                                          |
| ------------------------------- |:--------:| --------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| bucket                          |    Y     | Output          | The bucket name                                                            | `"mybucket"`                                                                                     |
| type                            |    Y     | Output          | Tge GCP credentials type                                                   | `"service_account"`                                                                              |
| project_id                      |    Y     | Output          | GCP project id                                                             | `projectId`                                                                                      |
| private_key_id                |    Y     | Output          | GCP private key id                                                         | `"privateKeyId"`                                                                                 |
| private_key                     |    Y     | Output          | GCP credentials private key. Replace with x509 cert Replace with x509 cert | `12345-12345`                                                                                    |
| client_email                    |    Y     | Output          | GCP client email                                                           | `"client@email.com"`                                                                             |
| client_id                       |    Y     | Output          | GCP client id                                                              | `0123456789-0123456789`                                                                          |
| auth_uri                        |    Y     | Output          | Google account OAuth endpoint                                              | `https://accounts.google.com/o/oauth2/auth`                                                      |
| token_uri                       |    Y     | Output          | Google account token uri                                                   | `https://oauth2.googleapis.com/token`                                                            |
| auth_provider_x509_cert_url |    Y     | Output          | GCP credentials cert url                                                   | `https://www.googleapis.com/oauth2/v1/certs`                                                     |
| client_x509_cert_url          |    Y     | Output          | GCP credentials project x509 cert url                                      | `https://www.googleapis.com/robot/v1/metadata/x509/<PROJECT_NAME>.iam.gserviceaccount.com` |

## 相关链接

This component supports **output binding** with the following operations:

- `create`

## 相关链接

- [Basic schema for a Dapr component]({{< ref component-schema >}})
- [Bindings building block]({{< ref bindings >}})
- [如何通过 input binding 触发应用]({{< ref howto-triggers.md >}})
- [How-To：使用绑定与外部资源进行交互]({{< ref howto-bindings.md >}})
- [绑定API 参考]({{< ref bindings_api.md >}})