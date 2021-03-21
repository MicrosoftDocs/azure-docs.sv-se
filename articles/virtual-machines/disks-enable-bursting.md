---
title: Aktivera disk burst på begäran
description: Aktivera disk burst på begäran på den hanterade disken.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 733d441705c7c77f0667f88151e96f76975ee0b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596407"
---
# <a name="enable-on-demand-bursting"></a>Aktivera burst-överföring på begäran

Premium-enheter med solid state (SSD) har två tillgängliga burst-modeller; kredit baserad burst-överföring och överföring på begäran. Den här artikeln beskriver hur du växlar till burst-överföring på begäran. Diskar som använder modellen på begäran kan överföras bortom de ursprungliga etablerade målen. Burst-överföring på begäran sker så ofta som det behövs av arbets belastningen, upp till maximalt burst-mål. Burst-överföring på begäran medför ytterligare kostnader.

Mer information om disk burst finns i [Managed disk bursting](disk-bursting.md).

> [!IMPORTANT]
> Du behöver inte följa stegen i den här artikeln för att använda en kredit baserad burst-överföring. Som standard aktive ras krediterad burst-överföring på alla berättigade diskar.

Innan du aktiverar burst på begäran kan du förstå följande:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Kom igång

Burst-överföring på begäran kan aktive ras med antingen Azure PowerShell-modulen, Azure CLI-eller Azure Resource Manager-mallarna. Följande exempel beskriver hur du skapar en ny disk med burst-överföring aktiverat på begäran och aktiverar burst-överföring på begäran på befintliga diskar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdletar för burst-överföring på begäran är tillgängliga i version 5.5.0 och senare av AZ-modulen. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Skapa en tom datadisk med burst-överföring på begäran

En hanterad disk måste vara större än 512 GiB för att aktivera burst på begäran. Ersätt `<myResourceGroupDisk>` parametrarna och `<myDataDisk>` kör sedan följande skript för att skapa en Premium SSD med burst-överföring på begäran:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Aktivera burst-överföring på begäran på en befintlig disk

En hanterad disk måste vara större än 512 GiB för att aktivera burst på begäran. Ersätt `<myResourceGroupDisk>` parametrarna, `<myDataDisk>` och kör det här kommandot för att aktivera burst-överföring på begäran på en befintlig disk:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdletar för burst-överföring på begäran finns i version 2.19.0 och senare av [Azure CLI-modulen](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Skapa och koppla en datadisk för data överföring på begäran

En hanterad disk måste vara större än 512 GiB för att aktivera burst på begäran. Ersätt `<yourDiskName>` parametrarna, `<yourResourceGroup>` , och och `<yourVMName>` kör sedan följande kommandon för att skapa en Premium SSD med burst på begäran:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Aktivera burst-överföring på begäran på en befintlig disk-CLI

En hanterad disk måste vara större än 512 GiB för att aktivera burst på begäran. Ersätt `<myResourceGroupDisk>` parametrarna och `<yourDiskName>` och kör det här kommandot för att aktivera burst-överföring på begäran på en befintlig disk:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Med `2020-09-30` disk-API: et kan du aktivera burst-överföring på begäran på nyligen skapade eller befintliga Premium-SSD som är större än 512 GIB. `2020-09-30`API: t introducerade en ny egenskap, `burstingEnabled` . Som standard är den här egenskapen inställd på falskt. Följande exempel-mall skapar en 1TiB Premium SSD i USA, västra centrala, med disk burst aktive rad:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Nästa steg

Information om hur du får inblick i dina burst-resurser finns i [mått för disk burst](disks-metrics.md).