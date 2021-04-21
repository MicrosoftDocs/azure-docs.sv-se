---
title: Använda Application Health-tillägg med skalningsuppsättningar för virtuella Azure-datorer
description: Lär dig hur du använder application health-tillägget för att övervaka hälsotillståndet för dina program som distribueras på VM-skalningsuppsättningar.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762901"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Använda tillägget för programmets hälsotillstånd med VM-skalningsuppsättningar
Övervakning av programmets hälsotillstånd är en viktig signal för att hantera och uppgradera distributionen. Skalningsuppsättningar för virtuella [](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) [Azure-datorer](virtual-machine-scale-sets-automatic-upgrade.md)har stöd för löpande uppgraderingar, inklusive automatiska uppgraderingar av operativsystemavbildningar, som förlitar sig på hälsoövervakning av de enskilda instanserna för att uppgradera distributionen. Du kan också använda hälsotillägg för att övervaka programhälsan för varje instans i skalningsuppsättningen och utföra instansreparationer [med hjälp av automatiska instansreparationer.](virtual-machine-scale-sets-automatic-instance-repairs.md)

Den här artikeln beskriver hur du kan använda tillägget För programhälsa för att övervaka hälsotillståndet för dina program som distribueras på VM-skalningsuppsättningar.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med:
-   Tillägg för virtuella [Azure-datorer](../virtual-machines/extensions/overview.md)
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) VM-skalningsuppsättningar

## <a name="when-to-use-the-application-health-extension"></a>När du ska använda tillägget för programhälsa
Tillägget För programhälsa distribueras inuti en instans av en VM-skalningsuppsättning och rapporterar om VM-hälsan inifrån skalningsuppsättningsinstansen. Du kan konfigurera tillägget för att söka efter en programslutpunkt och uppdatera status för programmet på den instansen. Den här instansstatusen kontrolleras av Azure för att avgöra om en instans är berättigad till uppgraderingsåtgärder.

Eftersom tillägget rapporterar hälsotillstånd inifrån en virtuell dator kan tillägget användas i situationer där externa avsökningar, till exempel programhälsoavsökningar (som använder anpassade [Azure Load Balancer-avsökningar)](../load-balancer/load-balancer-custom-probe-overview.md)inte kan användas.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för application health-tillägget. Tillägget kräver minst en begäran om "tcp", "http" eller "https" med en associerad port eller sökväg för begäran.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Egenskapsvärden

| Name | Värde/exempel | Datatyp
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| utgivare | `Microsoft.ManagedServices` | sträng |
| typ | `ApplicationHealthLinux` (Linux) `ApplicationHealthWindows` (Windows) | sträng |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Inställningar

| Name | Värde/exempel | Datatyp
| ---- | ---- | ----
| Protokollet | `http` eller `https` eller `tcp` | sträng |
| port | Valfritt när protokollet `http` är `https` eller , obligatoriskt när protokollet är `tcp` | int |
| requestPath | Obligatoriskt när protokoll `http` är `https` eller , tillåts inte när protokollet är `tcp` | sträng |

## <a name="deploy-the-application-health-extension"></a>Distribuera tillägget för programhälsa
Det finns flera sätt att distribuera application health-tillägget till dina skalningsuppsättningar enligt beskrivningen i exemplen nedan.

### <a name="rest-api"></a>REST-API

I följande exempel läggs tillägget Application Health (med namnet myHealthExtension) till i extensionProfile i skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Använd `PATCH` för att redigera ett redan distribuerat tillägg.

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till tillägget Application Health till skalningsuppsättningens modelldefinition.

I följande exempel läggs tillägget Application Health till i `extensionProfile` skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning. I exemplet används den nya Az PowerShell-modulen.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss extension set för](/cli/azure/vmss/extension#az_vmss_extension_set) att lägga till tillägget Application Health i modelldefinitionen för skalningsuppsättningen.

I följande exempel läggs tillägget Application Health till i skalningsuppsättningsmodellen för en Linux-baserad skalningsuppsättning.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Filen extension.jsfilinnehåll.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Felsöka
Utdata för tilläggskörning loggas till filer som finns i följande kataloger:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

Loggarna samlar också regelbundet in programmets hälsostatus.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på VM-skalningsuppsättningar.
