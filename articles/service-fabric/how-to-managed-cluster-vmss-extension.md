---
title: Lägg till ett tillägg för skalnings uppsättning för virtuell dator till en Service Fabric hanterad kluster nodtyp (för hands version)
description: Så här lägger du till ett tillägg för skalnings uppsättning för virtuella datorer Service Fabric hanterad kluster nodtyp
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: a47908b511f79c18482e9d21e623f1cb4dc70ed1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737774"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Lägg till ett tillägg för skalnings uppsättning för virtuell dator till en Service Fabric hanterad kluster nodtyp (för hands version)

Varje nodtyp i ett Service Fabric hanterat kluster backas upp av en skalnings uppsättning för virtuella datorer. På så sätt kan du lägga till [tillägg för skalnings uppsättningar för virtuella datorer](../virtual-machines/extensions/overview.md) i Service Fabric hanterade klusternoder.

Du kan lägga till ett tillägg för skalnings uppsättning för virtuell dator till en nodtyp med PowerShell [-kommandot Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) .

Alternativt kan du använda ett tillägg för skalnings uppsättning för virtuella datorer på en Service Fabric hanterad klusternod i din Azure Resource Manager-mall, till exempel:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Mer information om hur du konfigurerar Service Fabric hanterade klusternoder finns i avsnittet [hanterad kluster](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)-nodtyp.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric hanterade kluster finns i:

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om Service Fabric hanterade kluster](./faq-managed-cluster.md)
