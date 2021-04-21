---
title: Distribuera på dedikerad värd
description: Använd en dedikerad värd för att uppnå verklig isolering på värdnivå för dina Azure Container Instances arbetsbelastningar
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790841"
---
# <a name="deploy-on-dedicated-hosts"></a>Distribuera på dedikerade värdar

"Dedikerad" är en Azure Container Instances-SKU (ACI) som tillhandahåller en isolerad och dedikerad beräkningsmiljö för säker körning av containrar. Med de dedikerade SKU:erna får varje containergrupp en dedikerad fysisk server i ett Azure-datacenter, vilket säkerställer fullständig arbetsbelastningsisolering för att uppfylla organisationens krav på säkerhet och efterlevnad. 

Den dedikerade SKU:n är lämplig för containerarbetsbelastningar som kräver arbetsbelastningsisolering från ett fysiskt serverperspektiv.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> På grund av vissa aktuella begränsningar är det inte säkert att alla begäranden om begränsningsökning godkänns.

* Standardgränsen för alla prenumerationer som använder den dedikerade SKU:n är 0. Om du vill använda den här SKU:n för dina distributioner av produktionscontainer skapar du en [Azure Support-begäran][azure-support] för att öka gränsen.

## <a name="use-the-dedicated-sku"></a>Använda den dedikerade SKU:n

> [!IMPORTANT]
> Den dedikerade SKU:n är bara tillgänglig i den senaste API-versionen (2019-12-01) som håller på att lanseras. Ange den här API-versionen i distributionsmallen.
>

Från och med API-version 2019-12-01 finns det en egenskap under avsnittet egenskaper för containergrupp i en distributionsmall som krävs för en `sku` ACI-distribution. För närvarande kan du använda den här egenskapen som en del av Azure Resource Manager en distributionsmall för ACI. Läs mer om att distribuera ACI-resurser med en mall i [Självstudie: Distribuera](./container-instances-multi-container-group.md)en grupp med flera containrar med en Resource Manager mall . 

Egenskapen `sku` kan ha något av följande värden:
* `Standard` – standardvalet av ACI-distribution, som fortfarande garanterar säkerhet på hypervisornivå 
* `Dedicated` – används för isolering på arbetsbelastningsnivå med dedikerade fysiska värdar för containergruppen

## <a name="modify-your-json-deployment-template"></a>Ändra JSON-distributionsmallen

Ändra eller lägg till följande egenskaper i distributionsmallen:
* Under `resources` anger du till `apiVersion` `2019-12-01` .
* Under egenskaperna för containergruppen lägger du till en `sku` egenskap med värdet `Dedicated` .

Här är ett exempelfragment för resursavsnittet i en containergruppsdistributionsmall som använder den dedikerade SKU:n:

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

Följande är en fullständig mall som distribuerar en exempelcontainergrupp som kör en enda containerinstans:

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

## <a name="deploy-your-container-group"></a>Distribuera din containergrupp

Om du har skapat och redigerat distributionsmallfilen på skrivbordet kan du ladda upp den till Cloud Shell-katalogen genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. En lyckad distribution sker på en dedikerad värd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
