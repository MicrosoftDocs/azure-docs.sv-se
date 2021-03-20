---
title: Använda önskad tillstånds konfiguration med Virtual Machine Scale Sets
description: Använd Virtual Machine Scale Sets med Azure Desired State Configuration-tillägget för att konfigurera virtuella datorer.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87080496"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Använda Virtual Machine Scale Sets med Azure DSC-tillägget
[Virtual Machine Scale Sets](./overview.md) kan användas med tilläggs hanteraren för [Azure Desired State Configuration (DSC)](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Skalnings uppsättningar för virtuella datorer ger ett sätt att distribuera och hantera ett stort antal virtuella datorer och kan skalas elastiskt och ut i svar på belastningen. DSC används för att konfigurera de virtuella datorerna när de är online så att de kör produktions program varan.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Skillnader mellan att distribuera till Virtual Machines och Virtual Machine Scale Sets
Den underliggande mallstrukturlistan för en skalnings uppsättning för en virtuell dator skiljer sig något från en enda virtuell dator. Mer specifikt distribuerar en enskild virtuell dator tillägg under noden "virtualMachines". Det finns en post av typen "Extensions" där DSC läggs till i mallen

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

En nod för skalnings uppsättning för virtuell dator har avsnittet "egenskaper" med attributet "VirtualMachineProfile", "extensionProfile". DSC har lagts till under "tillägg"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Beteende för en skalnings uppsättning för virtuell dator
Beteendet för en skalnings uppsättning för virtuella datorer är identiskt med beteendet för en enskild virtuell dator. När en ny virtuell dator skapas, tillhandahålls den automatiskt med DSC-tillägget. Om en nyare version av WMF krävs av tillägget måste den virtuella datorn startas om innan den kan anslutas. När den är online hämtas DSC-konfigurationen. zip och etablera den på den virtuella datorn. Mer information finns i [Översikt över Azure DSC-tillägget](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json).

## <a name="next-steps"></a>Nästa steg
Granska [Azure Resource Manager-mallen för DSC-tillägget](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json).

Lär dig hur [DSC-tillägget hanterar autentiseringsuppgifter på ett säkert sätt](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json). 

Mer information om tilläggs hanteraren för Azure DSC finns i [Introduktion till tillägget Azure Desired State Configuration](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json). 

Mer information om PowerShell DSC [finns i PowerShell-dokumentations centret](/powershell/scripting/dsc/overview/overview). 
