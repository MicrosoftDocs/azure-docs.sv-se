---
title: Konvertera Managed disks Storage mellan olika disk typer med hjälp av Azure CLI
description: Så här konverterar du Azure Managed disks mellan olika disk typer med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558465"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konvertera Azure Managed disks Storage från standard till Premium eller Premium till standard

Det finns fyra disk typer av Azure Managed disks: Azure Ultra disks, Premium SSD, standard SSD och standard-HDD. Du kan växla mellan Premium SSD, standard SSD och standard-HDD baserat på dina prestanda behov. Du kan inte byta från eller till en Ultra disk, du måste distribuera en ny.

Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disk typer.

Den här artikeln visar hur du konverterar hanterade diskar från en disk typ till en annan med hjälp av Azure CLI. Information om hur du installerar eller uppgraderar verktyget finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

* Disk konvertering kräver omstart av den virtuella datorn (VM), så schemalägger migreringen av disk lagringen under ett redan befintligt underhålls fönster.
* För ohanterade diskar måste du först [konvertera till Managed disks](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagrings alternativen.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Växla alla hanterade diskar för en virtuell dator mellan från ett konto till ett annat

Det här exemplet visar hur du konverterar alla diskar i en virtuell dator till Premium Storage. Men genom att ändra SKU-variabeln i det här exemplet kan du konvertera den virtuella datorns disk typer till standard SSD eller standard-HDD. Observera att om du använder Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. Det här exemplet växlar också till en storlek som har stöd för Premium Storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Växla enskilda hanterade diskar från en disktyp till en annan

För din arbets belastning för utveckling/testning kanske du vill ha en blandning av standard-och Premium-diskar för att minska kostnaderna. Du kan välja att bara uppgradera de diskar som behöver bättre prestanda. Det här exemplet visar hur du konverterar en enskild virtuell dator disk från standard till Premium Storage. Men genom att ändra SKU-variabeln i det här exemplet kan du konvertera den virtuella datorns disk typer till standard SSD eller standard-HDD. Om du vill använda Premium Managed disks måste den virtuella datorn använda en [VM-storlek](../sizes.md) som har stöd för Premium Storage. Det här exemplet växlar också till en storlek som har stöd för Premium Storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Växla Managed disks från en typ av disk till en annan

Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över **virtuella datorer**.
3. Om den virtuella datorn inte är stoppad väljer du **stanna** överst i **översikts** fönstret för den virtuella datorn och väntar på att den virtuella datorn ska stoppas.
4. I fönstret för den virtuella datorn väljer du **diskar** på menyn.
5. Välj den disk som du vill konvertera.
6. Välj **konfiguration** på menyn.
7. Ändra **konto typen** från den ursprungliga disk typen till den önskade disk typen.
8. Välj **Spara** och Stäng disk fönstret.

Uppdatering av disk typen är omedelbar. Du kan starta om den virtuella datorn efter konverteringen.

## <a name="next-steps"></a>Nästa steg

Gör en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicks bilder](snapshot-copy-managed-disk.md).