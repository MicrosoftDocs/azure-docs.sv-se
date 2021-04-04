---
title: Uppgradera konfigurationen av ett Azure Service Fabric-kluster
description: Lär dig hur du uppgraderar konfigurationen som kör ett Service Fabric kluster i Azure med hjälp av en Resource Manager-mall.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842724"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uppgradera konfigurationen av ett kluster i Azure 

I den här artikeln beskrivs hur du anpassar de olika infrastruktur inställningarna för ditt Service Fabric-kluster. För kluster som finns i Azure kan du anpassa inställningarna via [Azure Portal](https://portal.azure.com) eller genom att använda en Azure Resource Manager mall.

> [!NOTE]
> Alla inställningar är inte tillgängliga i portalen och det är en [bra idé att anpassa den med hjälp av en Azure Resource Manager mall](./service-fabric-best-practices-infrastructure-as-code.md). Portalen är endast för Service Fabric Dev\Test-scenario.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassa kluster inställningar med Resource Manager-mallar
Azure-kluster kan konfigureras via JSON Resource Manager-mallen. Mer information om de olika inställningarna finns i [konfigurations inställningar för kluster](service-fabric-cluster-fabric-settings.md). Som exempel visar stegen nedan hur du lägger till en ny inställning *MaxDiskQuotaInMB* i avsnittet *diagnostik* med Azure Resource Explorer.

1. Gå till https://resources.azure.com
2. Navigera till din prenumeration genom att expandera **prenumerationer**  ->  **\<Your Subscription>**  ->  **resourceGroups**-  ->  **\<Your Resource Group>**  ->  **providers**  ->  **Microsoft. ServiceFabric**-  ->  **kluster** -> **\<Your Cluster Name>**
3. I det övre högra hörnet väljer du **Läs/skriv.**
4. Välj **Redigera** och uppdatera `fabricSettings` JSON-elementet och Lägg till ett nytt element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Du kan också anpassa kluster inställningarna på något av följande sätt med Azure Resource Manager:

- Använd [Azure Portal](../azure-resource-manager/templates/export-template-portal.md) för att exportera och uppdatera Resource Manager-mallen.
- Använd [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) för att exportera och uppdatera Resource Manager-mallen.
- Använd [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) för att exportera och uppdatera Resource Manager-mallen.
- Använd Azure PowerShell [set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) och [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) för att ändra inställningen direkt.
- Använd Azure CLI [AZ SF Cluster Setting](/cli/azure/sf/cluster/setting) commands för att ändra inställningen direkt.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [inställningarna för Service Fabric klustret](service-fabric-cluster-fabric-settings.md).
* Lär dig hur du [skalar upp och ut ditt kluster](service-fabric-cluster-scale-in-out.md).
