---
title: Aktivera kryptering från slutpunkt till slutpunkt med kryptering på värden – Azure CLI – hanterade diskar
description: Använd kryptering på värden för att aktivera kryptering från slutpunkt till slutpunkt på dina Azure-hanterade diskar.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533613"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Använd Azure CLI för att aktivera kryptering från slutpunkt till slutpunkt med hjälp av kryptering på värden

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella datorvärden i vila och flöden krypteras till lagringstjänsten. Konceptuell information om kryptering på värden samt andra typer av kryptering av hanterade diskar finns i Kryptering på värd [– slutpunkt-till-slutpunkt-kryptering för dina VM-data.](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Den fullständiga listan över VM-storlekar som stöds kan hämtas programmatiskt. Information om hur du hämtar dem programmässigt finns i avsnittet [Hitta VM-storlekar som stöds.](#finding-supported-vm-sizes)
Uppgradering av VM-storleken resulterar i validering för att kontrollera om den nya VM-storleken stöder encryptionAtHost-funktionen.

## <a name="prerequisites"></a>Förutsättningar

Du måste aktivera funktionen för din prenumeration innan du använder egenskapen EncryptionAtHost för din virtuella dator/VMSS. Följ stegen nedan för att aktivera funktionen för din prenumeration:

1.  Kör följande kommando för att registrera funktionen för din prenumeration

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Kontrollera att registreringstillståndet är Registrerad (tar några minuter) med hjälp av kommandot nedan innan du provar funktionen.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Skapa en Azure Key Vault och DiskEncryptionSet

När funktionen har aktiverats måste du konfigurera en Azure Key Vault och en DiskEncryptionSet, om du inte redan har gjort det.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Exempel

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Skapa en virtuell dator med kryptering på värden aktiverad med kund hanterade nycklar. 

Skapa en virtuell dator med hanterade diskar med resurs-URI för Den DiskEncryptionSet som skapades tidigare för att kryptera cachelagringen av operativsystem och datadiskar med kund hanterade nycklar. Temporära diskar krypteras med plattform hanterade nycklar. 

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

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Skapa en virtuell dator med kryptering på värden aktiverad med plattform hanterade nycklar. 

Skapa en virtuell dator med kryptering på värden aktiverad för att kryptera cachelagring av OS/datadiskar och temporära diskar med plattform hanterade nycklar. 

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

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Kontrollera krypteringsstatusen på värden för en virtuell dator

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Skapa en VM-skalningsuppsättning med kryptering på värden aktiverad med kund hanterade nycklar. 

Skapa en VM-skalningsuppsättning med hanterade diskar med resurs-URI för den DiskEncryptionSet som skapades tidigare för att kryptera cachelagringen av operativsystem och datadiskar med kund hanterade nycklar. Temporära diskar krypteras med plattform hanterade nycklar. 

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

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Skapa en VM-skalningsuppsättning med kryptering på värden aktiverad med plattformsbaserade nycklar. 

Skapa en VM-skalningsuppsättning med kryptering på värden aktiverad för att kryptera cachelagring av OS/datadiskar och temporära diskar med plattformsbaserade nycklar. 

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

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Uppdatera en VM-skalningsuppsättning för att aktivera kryptering på värden. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Kontrollera statusen för kryptering på värden för en VM-skalningsuppsättning

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Hitta VM-storlekar som stöds

Äldre VM-storlekar stöds inte. Du hittar listan över VM-storlekar som stöds genom att antingen:

Anropa [RESOURCE Skus-API:et](/rest/api/compute/resourceskus/list) och `EncryptionAtHostSupported` kontrollera att funktionen är inställd på **True**.

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

Eller anropa [PowerShell-cmdleten Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

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

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande länk innehåller exempelskript, vart och ett med ett respektive scenario, som du kan använda för att skydda dina hanterade diskar.

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
