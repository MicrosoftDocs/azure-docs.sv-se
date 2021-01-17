---
title: Skapa en inkrementell ögonblicksbild
description: Lär dig mer om stegvisa ögonblicks bilder för hanterade diskar, inklusive hur du skapar dem med hjälp av Azure Portal, Azure PowerShell modul och Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a1cfabf6ccbb5d580bcad36694cd2e1a74155714
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540768"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Skapa en stegvis ögonblicks bild för hanterade diskar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda Azure PowerShell för att skapa en stegvis ögonblicks bild. Du behöver den senaste versionen av Azure PowerShell, följande kommando kommer antingen att installera den eller uppdatera den befintliga installationen till senaste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

När den är installerad loggar du in på PowerShell-sessionen med `Connect-AzAccount` .

Om du vill skapa en stegvis ögonblicks bild med Azure PowerShell anger du konfigurationen med [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) med `-Incremental` parametern och skickar den som en variabel till [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) via `-Snapshot` parametern.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Du kan identifiera stegvisa ögonblicks bilder från samma disk med `SourceResourceId` `SourceUniqueId` egenskaperna och för ögonblicks bilder. `SourceResourceId` är Azure Resource Manager resurs-ID för den överordnade disken. `SourceUniqueId` är värdet som ärvs från `UniqueId` diskens egenskap. Om du skulle ta bort en disk och sedan skapa en ny disk med samma namn, `UniqueId` ändras egenskapens värde.

Du kan använda `SourceResourceId` och `SourceUniqueId` för att skapa en lista över alla ögonblicks bilder som är associerade med en viss disk. Ersätt `<yourResourceGroupNameHere>` med ditt värde och Använd sedan följande exempel för att visa en lista över dina befintliga stegvisa ögonblicks bilder:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager-mall](#tab/azure-resource-manager)

Du kan också använda Azure Resource Manager mallar för att skapa en stegvis ögonblicks bild. Du måste kontrol lera att API version är inställt på **2019-03-01** och att den stegvisa egenskapen också har angetts till true. Följande fragment är ett exempel på hur du skapar en stegvis ögonblicks bild med Resource Manager-mallar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>Nästa steg

Om du vill se exempel kod som demonstrerar den differentiella kapaciteten för stegvisa ögonblicks bilder med hjälp av .NET, se [Kopiera Azure-Managed disks säkerhets kopior till en annan region med differentiella funktioner i stegvisa ögonblicks bilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).