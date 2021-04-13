---
title: Registrera en dator för att Azure Automanage med en ARM-mall
description: Lär dig hur du skapar en dator för Azure Automanage med en Azure Resource Manager mall.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368706"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Registrera en dator för att hantera automatiskt med en Azure Resource Manager (ARM)-mall


## <a name="overview"></a>Översikt
Följ stegen nedan för att publicera en dator med bästa praxis för automatisk hanering. ARM-mallen nedan skapar ett -objekt, som är den Azure-resurs som representerar en dator som har `configurationProfileAssignment` publiceras för automatisk hantering.

## <a name="prerequisites"></a>Förutsättningar
* Du måste ha skapat ett befintligt automanagekonto. I [det här](./automanage-account.md) dokumentet finns mer information om det automatiska kontot och hur du skapar ett.
* Du måste ha rollen **Deltagare** i resursgruppen som innehåller de datorer som du vill publicera till Automanage

## <a name="arm-template-overview"></a>Översikt över ARM-mall
Följande ARM-mall kommer att publicera den angivna datorn Azure Automanage bästa praxis. Information om ARM-mallen och hur du distribuerar finns i avsnittet arm-malldistribution [nedan.](#arm-template-deployment)
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Distribution av ARM-mall
ARM-mallen ovan skapar en tilldelning av konfigurationsprofilen för den angivna datorn med hjälp av ett angivet automanagekonto. Om du inte har skapat ett automanagekonto kan du läsa mer i [det här dokumentet.](./automanage-account.md)

Värdet `configurationProfileAssignment` kan vara något av följande värden:
* "Produktion"
* "DevTest"

Följ dessa steg för att distribuera ARM-mallen:
1. Spara ARM-mallen nedan som `azuredeploy.json`
1. Kör ARM-malldistributionen med `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Ange värden för machineName, automanageAccountName och configurationProfileAssignment när du tillfrågas
1. Nu är du klar!

Precis som med alla ARM-mallar är det möjligt att räkna ut parametrarna i en separat fil och använda `azuredeploy.parameters.json` den som ett argument vid distribution.

## <a name="next-steps"></a>Nästa steg
Läs mer om automanage för [Linux](./automanage-linux.md) och [Windows](./automanage-windows-server.md)