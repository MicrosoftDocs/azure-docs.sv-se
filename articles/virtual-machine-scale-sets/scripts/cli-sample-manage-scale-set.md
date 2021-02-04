---
title: Azure CLI-exempel för hantering av skalnings uppsättningar för virtuella datorer
description: Det här exemplet visar hur du lägger till diskar i en skalnings uppsättning för virtuella datorer. Du kan uppgradera diskar och lägga till dina virtuella datorer i Azure AD-autentisering.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 493f479a09fc7b21bb857ebd98c35824c548b5d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539798"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Skapa och hantera skalnings uppsättning för virtuella datorer

Använd de här exempel kommandona för att skapa en skalnings uppsättning för en virtuell dator med hjälp av Azure CLI.

Följande exempel kommandon demonstrerar följande åtgärder:

* Skapar en VM-skalningsuppsättning.
* Lägg till och uppgradera nya eller befintliga diskar till en skalnings uppsättning eller till en instans av uppsättningen.
* Lägg till skalnings uppsättning i Azure Active Directory (Azure AD)-autentisering.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Exempel kommandon

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

När du har lagt till en ny datadisk, formatera och montera disken. För virtuella Windows-datorer, se [bifoga en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md). För virtuella Linux-datorer, se [lägga till en disk till en virtuell Linux-dator](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Om du vill använda den utökade disken expanderar du den underliggande partitionen. Mer information finns i [expandera en diskpartition och fil system](/azure/virtual-machines/linux/expand-disks#expand-a-disk-partition-and-filesystem).

I det här exemplet ändrades storleken på en datadisk. Du kan använda samma procedur för att uppdatera en OS-disk. Mer information om en virtuell Windows-dator finns i [så här expanderar du OS-enheten för en virtuell dator](../../virtual-machines/windows/expand-os-disk.md). Mer information om virtuella Linux-datorer finns i [expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Rensa resurser

När du har använt de här kommandona kör du följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Azure CLI-referenser som används i den här artikeln

* [ta bort AZ disk](/cli/azure/disk#az_disk_delete)
* [lista över AZ-diskar](/cli/azure/disk#az_disk_list)
* [AZ disk uppdatering](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [AZ virtuell dator skal uppsättning frigörning](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [AZ VMSS disk Detached](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [AZ VMSS-identitet tilldela](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [AZ VMSS List-instances](/cli/azure/vmss#az_vmss_list_instances)
* [AZ VMSS-omstart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)
