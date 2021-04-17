---
title: Aktivera disk bursting på begäran
description: Aktivera bursting på begäran-disk på den hanterade disken.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483154"
---
# <a name="enable-on-demand-bursting"></a>Aktivera bursting på begäran

Premium SSD (Solid State Drives) har två tillgängliga burst-modeller: kreditbaserad bursting och bursting på begäran. Den här artikeln beskriver hur du växlar till burst-ning på begäran. Diskar som använder modellen på begäran kan burst-brytas bortom sina ursprungliga etablerade mål. Burst-belastningar på begäran sker så ofta arbetsbelastningen behöver, upp till det högsta burst-målet. Burst-ning på begäran medför ytterligare avgifter.

Mer information om disk bursting finns i [Bursting för hanterade diskar.](disk-bursting.md)

> [!IMPORTANT]
> Du behöver inte följa stegen i den här artikeln för att använda kreditbaserad bursting. Som standard är kreditbaserad bursting aktiverat på alla berättigade diskar.

Innan du aktiverar burst-åtgärder på begäran bör du förstå följande:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Kom igång

Burst-ning på begäran kan aktiveras med antingen Azure PowerShell-modulen, Azure CLI eller Azure Resource Manager mallar. I följande exempel visas hur du skapar en ny disk med burst-funktionen på begäran aktiverad och aktiverar bursting på begäran på befintliga diskar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Burst-cmdlets på begäran är tillgängliga i version 5.5.0 och senare av Az-modulen. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Skapa en tom datadisk med burst-data på begäran

En hanterad disk måste vara större än 512 GiB för att aktivera bursting på begäran. Ersätt `<myResourceGroupDisk>` parametrarna `<myDataDisk>` och och kör sedan följande skript för att skapa en Premium SSD med bursting på begäran:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Aktivera bursting på begäran på en befintlig disk

En hanterad disk måste vara större än 512 GiB för att aktivera burst-ning på begäran. Ersätt `<myResourceGroupDisk>` parametrarna `<myDataDisk>` , och kör det här kommandot för att aktivera bursting på begäran på en befintlig disk:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för burst på begäran är tillgängliga i version 2.19.0 och senare av [Azure CLI-modulen](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Skapa och koppla en datadisk med burst-data på begäran

En hanterad disk måste vara större än 512 GiB för att aktivera bursting på begäran. Ersätt `<yourDiskName>` parametrarna `<yourResourceGroup>` , och och kör sedan följande kommandon för att skapa en Premium SSD med `<yourVMName>` bursting på begäran:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Aktivera bursting på begäran på en befintlig disk – CLI

En hanterad disk måste vara större än 512 GiB för att aktivera burst-ning på begäran. Ersätt `<myResourceGroupDisk>` parametrarna `<yourDiskName>` och och kör det här kommandot för att aktivera bursting på begäran på en befintlig disk:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Med disk-API:et kan du aktivera bursting på begäran på nyligen skapade eller befintliga Premium-HÅRDDISKAR som är `2020-09-30` större än 512 GiB. `2020-09-30`API:et introducerade en ny egenskap, `burstingEnabled` . Som standard är den här egenskapen inställd på falskt. Följande exempelmall skapar en 1TiB Premium SSD i USA, västra centrala med disk bursting aktiverat:

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

Information om hur du får insyn i dina burst-resurser finns i [Mätvärden för disk bursting.](disks-metrics.md)
