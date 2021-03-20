---
title: Använda tillägg med batch-pooler
description: Tillägg är små program som underlättar etablering av konfiguration och konfiguration av batch-datornoder.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417944"
---
# <a name="use-extensions-with-batch-pools"></a>Använda tillägg med batch-pooler

Tillägg är små program som underlättar etablering av konfiguration och konfiguration av batch-datornoder. Du kan välja vilka tillägg som helst som tillåts av Azure Batch och låta dem vara installerade på datornoderna när de är etablerade. Därefter kan tillägget utföra den avsedda åtgärden.

Du kan kontrol lera Live-statusen för de tillägg du använder och hämta den information som de returnerar för att kunna använda identifierings-, korrigerings-eller diagnostik-funktioner.

## <a name="prerequisites"></a>Förutsättningar

- Pooler med tillägg måste använda [konfiguration av virtuell dator](nodes-and-pools.md#virtual-machine-configuration).
- CustomScript-tilläggs typen är reserverad för den Azure Batch tjänsten och kan inte åsidosättas.

### <a name="supported-extensions"></a>Tillägg som stöds

Följande tillägg kan för närvarande installeras när du skapar en batch-pool. 

- Azure Key Vault-tillägg för både [Linux](../virtual-machines/extensions/key-vault-linux.md) och [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Logg analys och övervaknings tillägg för både [Linux](../virtual-machines/extensions/oms-linux.md) och [Windows](../virtual-machines/extensions/oms-windows.md)
- Azure Security Pack

Du kan begära stöd för ytterligare utgivare och/eller tilläggs typer genom att öppna en support förfrågan.

## <a name="create-a-pool-with-extensions"></a>Skapa en pool med tillägg

Exemplet nedan skapar en batch-pool med Linux-noder som använder Azure Key Vault-tillägget.

REST API-URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Begärandetext

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Hämta tilläggs data från en pool

I exemplet nedan hämtas data från Azure Key Vault-tillägget.

REST API-URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Svars text

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om olika sätt att [Kopiera program och data till pool-noder](batch-applications-to-pool-nodes.md).
- Lär dig mer om att arbeta med [noder och pooler](nodes-and-pools.md).
