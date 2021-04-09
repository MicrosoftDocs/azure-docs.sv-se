---
title: Hitta och ta bort icke anslutna Azure-hanterade diskar och ohanterade diskar
description: Hur du hittar och tar bort frånkopplade Azure-hanterade och ohanterade diskar (VHD/Page blobbar) med hjälp av Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554386"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Hitta och ta bort icke anslutna Azure-hanterade diskar och ohanterade diskar

När du tar bort en virtuell dator (VM) i Azure tas som standard alla diskar som är kopplade till den virtuella datorn bort. Den här funktionen hjälper till att förhindra data förlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort fortsätter du att betala för ej anslutna diskar. Den här artikeln visar hur du hittar och tar bort alla frånkopplade diskar och minskar onödiga kostnader.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: hitta och ta bort ej anslutna diskar

Följande skript söker efter ej anslutna [hanterade diskar](../managed-disks-overview.md) genom att undersöka värdet för egenskapen **ManagedBy** . När en hanterad disk är ansluten till en virtuell dator innehåller egenskapen **ManagedBy** resurs-ID för den virtuella datorn. När en hanterad disk är frånkopplad är egenskapen **ManagedBy** null. Skriptet undersöker alla hanterade diskar i en Azure-prenumeration. När skriptet hittar en hanterad disk med egenskapen **ManagedBy** inställt på null, fastställer skriptet att disken är frånkopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange **deleteUnattachedDisks** -variabeln till 0. Med den här åtgärden kan du söka efter och Visa alla ej anslutna hanterade diskar.
>
>När du har granskat alla de ej anslutna diskarna kör du skriptet igen och anger variabeln **deleteUnattachedDisks** till 1. Med den här åtgärden kan du ta bort alla frånkopplade hanterade diskar.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: hitta och ta bort ej anslutna diskar

Ohanterade diskar är VHD-filer som lagras som [Page blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure Storage-konton](../../storage/common/storage-account-overview.md). Följande skript söker efter ej anslutna ohanterade diskar (Page blobbar) genom att undersöka värdet för egenskapen **LeaseStatus** . När en ohanterad disk är ansluten till en virtuell dator är egenskapen **LeaseStatus** inställd på **låst**. När en ohanterad disk är frånkopplad är egenskapen **LeaseStatus** inställd på **olåst**. Skriptet undersöker alla ohanterade diskar i alla Azure Storage-konton i en Azure-prenumeration. När skriptet hittar en ohanterad disk med egenskapen **LeaseStatus** inställd på **olåst**, fastställer skriptet att disken är frånkopplad.

>[!IMPORTANT]
>Kör först skriptet genom att ange **deleteUnattachedVHDs** -variabeln till `$false` . Med den här åtgärden kan du söka efter och Visa alla frånkopplade ohanterade virtuella hård diskar.
>
>När du har granskat alla de ej anslutna diskarna kör du skriptet igen och anger **deleteUnattachedVHDs** -variabeln till `$true` . Med den här åtgärden kan du ta bort alla frånkopplade ohanterade virtuella hård diskar.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [ta bort ett lagrings konto](../../storage/common/storage-account-create.md#delete-a-storage-account) och [identifiera överblivna diskar med PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)