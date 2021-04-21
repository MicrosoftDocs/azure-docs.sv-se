---
title: Använda sekvensering av tillägg med skalningsuppsättningar för virtuella Azure-datorer
description: Lär dig att sekvensera tilläggsetablering när du distribuerar flera tillägg på VM-skalningsuppsättningar.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788165"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Etablering av sekvenstillägg i VM-skalningsuppsättningar
Tillägg för virtuella Azure-datorer ger funktioner som konfiguration och hantering efter distribution, övervakning, säkerhet med mera. I produktionsdistributioner används vanligtvis en kombination av flera tillägg som konfigurerats för de virtuella datorinstanserna för att uppnå önskade resultat.

När du använder flera tillägg på en virtuell dator är det viktigt att se till att tillägg som kräver samma OS-resurser inte försöker hämta dessa resurser samtidigt. Vissa tillägg är också beroende av andra tillägg för att tillhandahålla nödvändiga konfigurationer, till exempel miljöinställningar och hemligheter. Utan rätt ordning och ordningsföljd på plats kan beroende tilläggdistributioner misslyckas.

Den här artikeln beskriver hur du kan sekvenstillägg som ska konfigureras för VM-instanser i VM-skalningsuppsättningar.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med:
-   Tillägg för virtuella [Azure-datorer](../virtual-machines/extensions/overview.md)
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) VM-skalningsuppsättningar

## <a name="when-to-use-extension-sequencing"></a>När du ska använda sekvensering av tillägg
Sekvenseringstillägg i inte obligatoriskt för skalningsuppsättningar, och om inget annat anges kan tillägg etableras på en skalningsuppsättningsinstans i valfri ordning.

Om din skalningsuppsättningsmodell till exempel har två tillägg – ExtensionA och ExtensionB – som anges i modellen kan någon av följande etableringssekvenser inträffa:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Om ditt program kräver att tillägg A alltid etableras före tillägg B bör du använda sekvensering av tillägg enligt beskrivningen i den här artikeln. Med sekvensering av tillägg sker nu bara en sekvens:
-   ExtensionA – > ExtensionB

Tillägg som inte anges i en definierad etableringssekvens kan etableras när som helst, inklusive före, efter eller under en definierad sekvens. Sekvensering av tillägg anger endast att ett visst tillägg kommer att etableras efter ett annat specifikt tillägg. Det påverkar inte etableringen av något annat tillägg som definierats i modellen.

Om din skalningsuppsättningsmodell till exempel har tre tillägg – tillägg A, tillägg B och tillägg C – som anges i modellen och tillägg C är inställt på att etableras efter tillägg A, kan någon av följande etableringssekvenser inträffa:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Om du behöver se till att inget annat tillägg etableras medan den definierade tilläggssekvensen körs rekommenderar vi sekvensering av alla tillägg i din skalningsuppsättningsmodell. I exemplet ovan kan du ange att tillägg B ska etableras efter tillägg C så att endast en sekvens kan ske:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Så här använder du sekvensering av tillägg
Om du vill sekvensera tilläggetablering måste du uppdatera tilläggsdefinitionen i skalningsuppsättningsmodellen så att den inkluderar egenskapen "provisionAfterExtensions", som accepterar en matris med tilläggsnamn. Tilläggen som anges i egenskapens matrisvärde måste vara fullständigt definierade i skalningsuppsättningsmodellen.

### <a name="template-deployment"></a>Malldistribution
I följande exempel definieras en mall där skalningsuppsättningen har tre tillägg – ExtensionA, ExtensionB och ExtensionC – så att tillägg etableras i ordning:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Eftersom egenskapen "provisionAfterExtensions" accepterar en matris med tilläggsnamn kan exemplet ovan ändras så att ExtensionC etableras efter ExtensionA och ExtensionB, men ingen ordning krävs mellan ExtensionA och ExtensionB. Följande mall kan användas för att uppnå det här scenariot:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST-API
I följande exempel läggs ett nytt tillägg med namnet ExtensionC till i en skalningsuppsättningsmodell. ExtensionC har beroenden på ExtensionA och ExtensionB, som redan har definierats i skalningsuppsättningsmodellen.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Om ExtensionC definierades tidigare i skalningsuppsättningsmodellen och du nu vill lägga till dess beroenden kan du köra en för att redigera egenskaperna för det redan `PATCH` distribuerade tillägget.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Ändringar av befintliga skalningsuppsättningsinstanser tillämpas vid nästa [uppgradering.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till tillägget Application Health till skalningsuppsättningens modelldefinition. Sekvensering av tillägg kräver att Az PowerShell 1.2.0 eller senare används.

I följande exempel läggs [tillägget Application Health till](virtual-machine-scale-sets-health-extension.md) i en `extensionProfile` skalningsuppsättningsmodell för en Windows-baserad skalningsuppsättning. Tillägget för programhälsa etableras efter etablering av det anpassade [skripttillägget](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalningsuppsättningen.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss extension set för](/cli/azure/vmss/extension#az_vmss_extension_set) att lägga till tillägget Application Health till skalningsuppsättningens modelldefinition. Sekvensering av tillägg kräver användning av Azure CLI 2.0.55 eller senare.

I följande exempel läggs tillägget [Application Health till](virtual-machine-scale-sets-health-extension.md) i skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning. Tillägget för programhälsa etableras efter etablering av det anpassade [skripttillägget](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalningsuppsättningen.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Felsöka

### <a name="not-able-to-add-extension-with-dependencies"></a>Kan du inte lägga till tillägg med beroenden?
1. Kontrollera att tilläggen som anges i provisionAfterExtensions har definierats i skalningsuppsättningsmodellen.
2. Se till att inga cirkulära beroenden introduceras. Följande sekvens tillåts till exempel inte: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Se till att alla tillägg som du använder beroenden på har en inställningsegenskap under tillägget "properties". Om ExtentionB till exempel måste etableras efter ExtensionA måste ExtensionA ha fältet "settings" under ExtensionA "properties". Du kan ange en tom inställningsegenskap om tillägget inte kräver några obligatoriska inställningar.

### <a name="not-able-to-remove-extensions"></a>Kan du inte ta bort tillägg?
Kontrollera att tilläggen som tas bort inte visas under provisionAfterExtensions för andra tillägg.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på VM-skalningsuppsättningar.
