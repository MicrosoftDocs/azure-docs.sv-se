---
title: Distribuera på dedikerad värd
description: Använd en dedikerad värd för att uppnå sann isolering på värdnivå för dina Azure Container Instances arbets belastningar
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 68b9b31cdfb55e8150b05e3efd35389320905cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034279"
---
# <a name="deploy-on-dedicated-hosts"></a>Distribuera på dedikerade värdar

"Dedikerad" är en Azure Container Instances-SKU (ACI) som tillhandahåller en isolerad och dedikerad beräknings miljö för att köra behållare på ett säkert sätt. Genom att använda dedikerade SKU-resultat i varje behållar grupp som har en dedikerad fysisk server i ett Azure-datacenter kan du se till att fullständig arbets belastnings isolering uppfyller organisationens krav på säkerhet och efterlevnad. 

Den dedikerade SKU: n är lämplig för behållar arbets belastningar som kräver arbets belastnings isolering från ett fysiskt Server perspektiv.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> På grund av vissa aktuella begränsningar garanteras inte alla gräns öknings begär Anden att godkännas.

* Standard gränsen för alla prenumerationer som ska använda den dedikerade SKU: n är 0. Om du vill använda den här SKU: n för distributioner av produktions behållare skapar du ett [Azure-supportbegäran][azure-support] för att öka gränsen.

## <a name="use-the-dedicated-sku"></a>Använd dedikerad SKU

> [!IMPORTANT]
> Användning av dedikerade SKU är bara tillgänglig i den senaste API-versionen (2019-12-01) som för närvarande är distribuerad. Ange den här API-versionen i distributions mal len.
>

Från och med API version 2019-12-01 finns en `sku` egenskap under avsnittet Egenskaper för behållar grupp i en distributions mall, vilket krävs för en ACI-distribution. För närvarande kan du använda den här egenskapen som en del av en mall för Azure Resource Manager distribution för ACI. Lär dig mer om att distribuera ACI-resurser med en mall i [självstudien: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](./container-instances-multi-container-group.md). 

`sku`Egenskapen kan ha ett av följande värden:
* `Standard` – standard alternativet för ACI distribution, som fortfarande garanterar säkerhet på hypervisor-nivå 
* `Dedicated` – används för isolering av arbets belastnings nivå med dedikerade fysiska värdar för behållar gruppen

## <a name="modify-your-json-deployment-template"></a>Ändra mallen för JSON-distribution

Ändra eller Lägg till följande egenskaper i distributions mal len:
* Under `resources` , anger `apiVersion` du `2019-12-01` .
* Lägg till en egenskap med värde under egenskaper för behållar grupp `sku` `Dedicated` .

Här är ett exempel-kodfragment för avsnittet resurser i en distributions mall för container grupper som använder den dedikerade SKU: n:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Följande är en komplett mall som distribuerar en exempel behållar grupp som kör en enda behållar instans:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Distribuera din behållar grupp

Om du har skapat och redigerat distributions mal len på Skriv bordet kan du överföra den till din Cloud Shell katalog genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. En lyckad distribution sker på en dedikerad värd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
