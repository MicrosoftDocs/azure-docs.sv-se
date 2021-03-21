---
title: Aktivera kryptering från slut punkt till slut punkt med kryptering på värd-Azure CLI-hanterade diskar
description: Använd kryptering på värden för att aktivera kryptering från slut punkt till slut punkt på dina Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3eecb584f468bc170f0325da8d734a1890691483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601779"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Använd Azure CLI för att aktivera kryptering från slut punkt till slut punkt med kryptering på värd

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. För konceptuell information om kryptering på värden och andra typer av hanterade disk krypterings typer, se [kryptering på värd-till-slutpunkt-kryptering för dina VM-data](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Du kan också hitta VM-storlekar program mässigt. Information om hur du hämtar dem program mässigt finns i avsnittet [hitta stödda VM-storlekar](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Förutsättningar

Du måste aktivera funktionen för din prenumeration innan du använder egenskapen EncryptionAtHost för din VM/VMSS. Följ stegen nedan för att aktivera funktionen för din prenumeration:

1.  Kör följande kommando för att registrera funktionen för din prenumeration

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Kontrol lera att registrerings statusen är registrerad (tar några minuter) med kommandot nedan innan du testar funktionen.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Skapa en Azure Key Vault-och DiskEncryptionSet

När funktionen är aktive rad måste du konfigurera en Azure Key Vault och en DiskEncryptionSet, om du inte redan gjort det.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Exempel

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Skapa en virtuell dator med kryptering på värden som är aktiverade med Kundhanterade nycklar. 

Skapa en virtuell dator med hanterade diskar med hjälp av resurs-URI: n för DiskEncryptionSet som skapades tidigare för att kryptera cacheminnet för operativ system och data diskar med Kundhanterade nycklar. Temporära diskar är krypterade med plattforms hanterade nycklar. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Skapa en virtuell dator med kryptering på värden som är aktiverade med plattforms hanterade nycklar. 

Skapa en virtuell dator med kryptering på värden som är aktive rad för att kryptera cacheminnet för OS/data diskar och temporära diskar med plattforms hanterade nycklar. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Uppdatera en virtuell dator för att aktivera kryptering på värden. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Kontrol lera statusen för kryptering på värd för en virtuell dator

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Skapa en skalnings uppsättning för virtuella datorer med kryptering på värden som är aktiverade med Kundhanterade nycklar. 

Skapa en skalnings uppsättning för virtuella datorer med hanterade diskar med hjälp av resurs-URI för DiskEncryptionSet som skapades tidigare för att kryptera cacheminnet för OS-och data diskar med Kundhanterade nycklar. Temporära diskar är krypterade med plattforms hanterade nycklar. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Skapa en skalnings uppsättning för virtuella datorer med kryptering på värden som är aktiverade med plattforms hanterade nycklar. 

Skapa en skalnings uppsättning för virtuell dator med kryptering på värddatorn som är aktive rad för att kryptera cacheminnet för OS/data diskar och temporära diskar med plattforms hanterade nycklar 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Uppdatera en skalnings uppsättning för virtuella datorer för att aktivera kryptering på värden. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Kontrol lera statusen för kryptering på värden för en skalnings uppsättning för virtuella datorer

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Söker efter VM-storlekar som stöds

Äldre VM-storlekar stöds inte. Du hittar listan över virtuella dator storlekar som stöds genom att antingen:

Anropar [resurs-SKU API](/rest/api/compute/resourceskus/list) och kontrollerar att `EncryptionAtHostSupported` funktionen har angetts till **Sant**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Eller, anropar PowerShell-cmdleten [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) .

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande länk innehåller exempel skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
