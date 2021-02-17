---
type: docs
title: "Supported secret stores"
linkTitle: "Supported secret stores"
weight: 30000
description: List of all the supported secret stores that can interface with Dapr
no_list: true
---

Table captions:

> `Status`: [Component certification]({{X28X}}) status
  - [Alpha]({{X17X}})
  - [Beta]({{X19X}})
  - [GA]({{X21X}}) > `Since`: defines from which Dapr Runtime version, the component is in the current status

> `Component version`: defines the version of the component

### Generic

| Name                                                              | Status                     | Component version | Since |
| ----------------------------------------------------------------- | -------------------------- | ----------------- | ----- |
| [Local environment variables]({{< ref envvar-secret-store.md >}}) | GA (For local development) | v1                | 1.0   |
| [Local file]({{< ref file-secret-store.md >}})                    | GA (For local development) | v1                | 1.0   |
| [HashiCorp Vault]({{< ref hashicorp-vault.md >}})                 | Alpha                      | v1                | 1.0   |
| [Kubernetes secrets]({{< ref kubernetes-secret-store.md >}})      | GA                         | v1                | 1.0   |

### Amazon Web Services (AWS)

| 名称                                                       | Status | Component version | Since |
| -------------------------------------------------------- | ------ | ----------------- | ----- |
| [AWS Secrets Manager]({{< ref aws-secret-manager.md >}}) | Alpha  | v1                | 1.0   |

### Google Cloud Platform (GCP)

| 名称                                                      | Status | Component version | Since |
| ------------------------------------------------------- | ------ | ----------------- | ----- |
| [GCP Secret Manager]({{< ref gcp-secret-manager.md >}}) | Alpha  | v1                | 1.0   |

### Microsoft Azure

| 名称                                                                                    | Status | Component version | Since |
| ------------------------------------------------------------------------------------- | ------ | ----------------- | ----- |
| [Azure Key Vault w/ Managed Identity]({{< ref azure-keyvault-managed-identity.md >}}) | Alpha  | v1                | 1.0   |
| [Azure Key Vault]({{< ref azure-keyvault.md >}})                                      | GA     | v1                | 1.0   |