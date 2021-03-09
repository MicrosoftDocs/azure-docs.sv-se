---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b4af7c8a02a1059e56bb2f709e3a4d1a9924662e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511055"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regioner som stöds
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell för att skapa en stegvis ögonblicks bild. Du behöver den senaste versionen av Azure PowerShell, följande kommando kommer antingen att installera den eller uppdatera den befintliga installationen till senaste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

När den är installerad loggar du in på PowerShell-sessionen med `Connect-AzAccount` .

Om du vill skapa en stegvis ögonblicks bild med Azure PowerShell anger du konfigurationen med [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) med `-Incremental` parametern och skickar den som en variabel till [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) via `-Snapshot` parametern.

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

## <a name="resource-manager-template"></a>Resource Manager-mall

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

## <a name="next-steps"></a>Nästa steg

Om du vill se exempel kod som demonstrerar den differentiella kapaciteten för stegvisa ögonblicks bilder med hjälp av .NET, se [Kopiera Azure-Managed disks säkerhets kopior till en annan region med differentiella funktioner i stegvisa ögonblicks bilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).