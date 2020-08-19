---
title: Hanterade diskar – Azure Resource Manager mallar
description: Information om hur du använder hanterade diskar i Azure Resource Manager mallar för virtuella Azure-datorer.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 15c3f2de30509a7dc6836f5d39fac4c0ea8b7f25
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513275"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Använda Managed Disks i Azure Resource Manager mallar

Det här dokumentet vägleder dig genom skillnaderna mellan hanterade och ohanterade diskar när du använder Azure Resource Manager mallar för att etablera virtuella datorer. I exemplen kan du uppdatera befintliga mallar som använder ohanterade diskar till hanterade diskar. För referens använder vi mallen [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) som en guide. Du kan se mallen med hjälp av både [hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) och en tidigare version med [ohanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) om du vill jämföra dem direkt.

## <a name="unmanaged-disks-template-formatting"></a>Mall-formatering för ohanterade diskar

Vi börjar med att ta en titt på hur ohanterade diskar distribueras. När du skapar ohanterade diskar behöver du ett lagrings konto för att lagra VHD-filerna. Du kan skapa ett nytt lagrings konto eller använda ett som redan finns. Den här artikeln visar hur du skapar ett nytt lagrings konto. Skapa en lagrings konto resurs i resurs blocket som visas nedan.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

I objektet virtuell dator lägger du till ett beroende på lagrings kontot för att säkerställa att det skapas före den virtuella datorn. I `storageProfile` avsnittet anger du den fullständiga URI: n för den virtuella hård disk platsen, som refererar till lagrings kontot och behövs för operativ system disken och eventuella data diskar.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Mall för hanterade diskar

Med Azure Managed Disks blir disken en resurs på den översta nivån och kräver inte längre ett lagrings konto som skapas av användaren. Hanterade diskar exponerades först i `2016-04-30-preview` API-versionen, de är tillgängliga i alla efterföljande API-versioner och är nu standard disk typen. Följande avsnitt går igenom standardinställningarna och information om hur du anpassar dina diskar ytterligare.

> [!NOTE]
> Vi rekommenderar att du använder en API-version senare än `2016-04-30-preview` när det fanns ändringar mellan `2016-04-30-preview` och `2017-03-30` .
>
>

### <a name="default-managed-disk-settings"></a>Standardinställning för hanterade diskar

Om du vill skapa en virtuell dator med hanterade diskar behöver du inte längre skapa lagrings konto resursen. Om du refererar till mal Lav exemplet nedan finns det några skillnader jämfört med föregående ohanterade disk exempel att Observera:

- `apiVersion`Är en version som stöder hanterade diskar.
- `osDisk` och `dataDisks` refererar inte längre till en angiven URI för den virtuella hård disken.
- När du distribuerar utan att ange ytterligare egenskaper kommer disken att använda en lagrings typ baserat på den virtuella datorns storlek. Om du till exempel använder en VM-storlek som har stöd för Premium Storage (storlekar med "s" i namnet, till exempel Standard_D2s_v3), så konfigureras Premium diskar som standard. Du kan ändra detta genom att använda SKU-inställningen på disken för att ange en lagrings typ.
- Om inget namn har angetts för disken används formatet `<VMName>_OsDisk_1_<randomstring>` för operativ system disken och `<VMName>_disk<#>_<randomstring>` för varje data disk.
  - Om en virtuell dator skapas från en anpassad avbildning, hämtas standardinställningarna för lagrings konto typ och disk namn från disk egenskaperna som definierats i den anpassade avbildnings resursen. Dessa kan åsidosättas genom att ange värden för dessa i mallen.
- Som standard är Azure Disk Encryption inaktive rad.
- Som standard är diskcachelagring att läsa/skriva för OS-disken och ingen för data diskar.
- I exemplet nedan finns fortfarande ett lagrings konto beroende, men det är bara för lagring av diagnostik och behövs inte för disk lagring.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Använda en hanterad disk resurs på översta nivån

Som ett alternativ till att ange disk konfigurationen i objektet för virtuella datorer kan du skapa en disk resurs på den översta nivån och koppla den som en del av den virtuella datorn. Du kan till exempel skapa en disk resurs på följande sätt för att använda den som en data disk.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

I VM-objektet refererar du till det disk objekt som ska bifogas. Om du anger resurs-ID: t för den hanterade disken som skapas i `managedDisk` egenskapen tillåts den bifogade disken som den virtuella datorn skapas. `apiVersion`För den virtuella dator resursen är inställd på `2017-03-30` . Ett beroende av disk resursen har lagts till för att kontrol lera att den har skapats innan du skapar den virtuella datorn. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Skapa hanterade tillgänglighets uppsättningar med virtuella datorer med hanterade diskar

Om du vill skapa hanterade tillgänglighets uppsättningar med virtuella datorer med hanterade diskar lägger du till `sku` objektet i tillgänglighets uppsättnings resursen och anger `name` egenskapen till `Aligned` . Den här egenskapen säkerställer att diskarna för varje virtuell dator är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Observera också att `apiVersion` för tillgänglighets uppsättnings resursen är inställd på `2018-10-01` .

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standard SSD diskar

Nedan visas de parametrar som behövs i Resource Manager-mallen för att skapa Standard SSD diskar:

* *API version* för Microsoft. Compute måste anges som `2018-04-01` (eller senare)
* Ange *managedDisk. storageAccountType* som `StandardSSD_LRS`

I följande exempel visas avsnittet *Properties. storageProfile. osDisk* för en virtuell dator som använder standard SSD diskar:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Ett komplett mall exempel på hur du skapar en Standard SSD disk med en mall finns i [skapa en virtuell dator från en Windows-avbildning med standard SSD data diskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Ytterligare scenarier och anpassningar

Om du vill ha fullständig information om REST API-specifikationerna kan du läsa [dokumentationen skapa en hanterad disk REST API](/rest/api/manageddisks/disks/disks-create-or-update). Du hittar ytterligare scenarier, samt standard-och godtagbara värden som kan skickas till API: t via mallar distributioner. 

## <a name="next-steps"></a>Nästa steg

* För fullständiga mallar som använder hanterade diskar går du till följande länkar för Azure snabb starts lagrings platsen.
    * [Virtuell Windows-dator med hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Virtuell Linux-dator med hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Gå till [översikts dokumentet för Azure Managed disks](~/articles/virtual-machines/windows/managed-disks-overview.md) om du vill veta mer om hanterade diskar.
* Granska referens dokumentationen för mallen för virtuella dator resurser genom att gå till referens dokumentet för [Microsoft. Compute/virtualMachines-mallen](/azure/templates/microsoft.compute/virtualmachines) .
* Granska referens dokumentationen för mallen för disk resurser genom att gå till referens dokumentet för [Microsoft. Compute/disks-mallen](/azure/templates/microsoft.compute/disks) .
* Information om hur du använder hanterade diskar i skalnings uppsättningar för virtuella Azure-datorer finns i dokumentet [Använd data diskar med skalnings uppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) .

