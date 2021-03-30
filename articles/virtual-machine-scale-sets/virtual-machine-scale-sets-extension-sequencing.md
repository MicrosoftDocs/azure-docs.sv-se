---
title: Använd tilläggs-sekvenseringen med skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du sekvenserar tillägg när du distribuerar flera tillägg på virtuella datorers skalnings uppsättningar.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83121039"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Etablering av sekvens tillägg i skalnings uppsättningar för virtuella datorer
Tillägg för virtuella Azure-datorer innehåller funktioner som konfiguration och hantering av konfiguration och hantering, övervakning, säkerhet och mycket annat. Produktions distributioner använder vanligt vis en kombination av flera tillägg som kon figurer ATS för de virtuella dator instanserna för att uppnå önskade resultat.

När du använder flera tillägg på en virtuell dator är det viktigt att se till att tillägg som kräver samma OS-resurser inte försöker att förvärva resurserna på samma gång. Vissa tillägg är också beroende av andra tillägg för att tillhandahålla obligatoriska konfigurationer, till exempel miljö inställningar och hemligheter. Det går inte att utföra beroende tilläggs distributioner utan rätt ordning och sortering på plats.

Den här artikeln beskriver hur du kan sekvensera tillägg som ska konfigureras för de virtuella dator instanserna i den virtuella datorns skalnings uppsättningar.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med:
-   [Tillägg](../virtual-machines/extensions/overview.md) för virtuella Azure-datorer
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) skalnings uppsättningar för virtuella datorer

## <a name="when-to-use-extension-sequencing"></a>När du ska använda fil namns sekvenseringen
Sekvensering av tillägg i är inte obligatoriskt för skalnings uppsättningar och om inget annat anges kan tillägg tillhandahållas på en skalnings uppsättnings instans i vilken ordning som helst.

Exempel: om din skalnings uppsättnings modell har två tillägg – tillägg och ExtensionB – anges i modellen, kan någon av följande etablerings sekvenser inträffa:
-   Utöka-> ExtensionB
-   ExtensionB-> tillägg

Om ditt program kräver tillägg A för att alltid vara etablerad före tillägg B, bör du använda tilläggs ordningen enligt beskrivningen i den här artikeln. Vid inökning av sekvenser görs bara en sekvens:
-   Utöka-> ExtensionB

Alla tillägg som inte anges i en definierad etablerings ordning kan tillhandahållas när som helst, inklusive före, efter, eller under en definierad sekvens. För att utöka ordningsföljden anges endast att ett angivet tillägg kommer att tillhandahållas efter ett annat angivet tillägg. Det påverkar inte etableringen av andra tillägg som definierats i modellen.

Om din skalnings uppsättnings modell har tre tillägg – tillägg A, tillägg B och tillägg C – anges i modellen, och tillägg C är inställt på att tillhandahållas efter tillägg A, kan antingen följande etablerings sekvenser uppstå:
-   Utöka-> ExtensionC-> ExtensionB
-   ExtensionB-> tillägg-> ExtensionC
-   Utöka-> ExtensionB-> ExtensionC

Om du behöver se till att inga andra tillägg är etablerade medan den definierade tilläggs ordningen körs, rekommenderar vi att du sekvenserar alla tillägg i skalnings uppsättnings modellen. I ovanstående exempel kan tillägg B ställas in för att tillhandahållas efter tillägg C så att endast en sekvens kan inträffa:
-   Utöka-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Använda sekvenseringen av tillägg
Om du vill lägga till etablering av tillägg måste du uppdatera tilläggs definitionen i skalnings uppsättnings modellen för att inkludera egenskapen "provisionAfterExtensions", som godkänner en matris med tilläggs namn. De tillägg som anges i egenskaps mat ris svärdet måste anges fullständigt i skalnings uppsättnings modellen.

### <a name="template-deployment"></a>Mall distribution
I följande exempel definieras en mall där skalnings uppsättningen har tre tillägg – tillägga, ExtensionB och ExtensionC – så att tilläggen tillhandahålls i följande ordning:
-   Utöka-> ExtensionB-> ExtensionC

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

Eftersom egenskapen "provisionAfterExtensions" accepterar en matris med tilläggs namn, kan ovanstående exempel ändras så att ExtensionC tillhandahålls efter tillägg och ExtensionB, men ingen ordning krävs mellan tillägg och ExtensionB. Följande mall kan användas för att uppnå det här scenariot:

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
I följande exempel läggs ett nytt tillägg till med namnet ExtensionC i en skalnings uppsättnings modell. ExtensionC har beroenden för tillägg och ExtensionB, som redan har definierats i skalnings uppsättnings modellen.

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

Om ExtensionC definierades tidigare i skalnings uppsättnings modellen och du nu vill lägga till dess beroenden, kan du köra en `PATCH` för att redigera egenskaperna för redan distribuerade tillägg.

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
Ändringar i befintliga skalnings uppsättnings instanser tillämpas vid nästa [uppgradering](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till program hälso tillägget i modell definitionen för skalnings uppsättningen. För att kunna använda sekvenseringen krävs AZ PowerShell-1.2.0 eller senare.

I följande exempel läggs [program hälso tillägget](virtual-machine-scale-sets-health-extension.md) till `extensionProfile` i en skalnings uppsättnings modell i en Windows-baserad skalnings uppsättning. Program hälso tillägget kommer att tillhandahållas efter etableringen av det [anpassade skript tillägget](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalnings uppsättningen.

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
Använd [AZ VMSS Extension set](/cli/azure/vmss/extension#az-vmss-extension-set) för att lägga till program hälso tillägget i modell definitionen för skalnings uppsättningen. För att använda sekvensering måste du använda Azure CLI-2.0.55 eller senare.

I följande exempel läggs [program hälso tillägget](virtual-machine-scale-sets-health-extension.md) till i skalnings uppsättnings modellen för en Windows-baserad skalnings uppsättning. Program hälso tillägget kommer att tillhandahållas efter etableringen av det [anpassade skript tillägget](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalnings uppsättningen.

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
1. Se till att de tillägg som anges i provisionAfterExtensions definieras i skalnings uppsättnings modellen.
2. Se till att inga cirkulära beroenden har introducerats. Följande sekvens tillåts till exempel inte: tillägga-> ExtensionB-> ExtensionC-> tillägg
3. Se till att alla tillägg som du tar beroenden på har egenskapen "Inställningar" under tillägget "egenskaper". Om t. ex. ExtentionB behöver tillhandahållas efter tillägg måste de ha fältet "Inställningar" under tillägget "egenskaper". Du kan ange en tom egenskap "Inställningar" om tillägget inte definierar några nödvändiga inställningar.

### <a name="not-able-to-remove-extensions"></a>Kan du inte ta bort tillägg?
Se till att tilläggen som tas bort inte visas under provisionAfterExtensions för alla andra tillägg.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorers skalnings uppsättningar.
