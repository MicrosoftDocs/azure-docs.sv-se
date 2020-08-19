---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37d9cf0b0ac7145e389f9fc768ff0b593b27ae1a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515231"
---
## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Delade diskar har stöd för flera operativ system. Se avsnittet [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) och [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) i den konceptuella artikeln för de operativ system som stöds.

## <a name="disk-sizes"></a>Disk storlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Distribuera delade diskar

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Distribuera en Premium SSD som en delad disk

Om du vill distribuera en hanterad disk med funktionen delad disk aktive rad använder du den nya egenskapen `maxShares` och anger ett värde som är större än 1. Detta gör disk delningen över flera virtuella datorer.

> [!IMPORTANT]
> Värdet för `maxShares` kan bara anges eller ändras när en disk demonteras från alla virtuella datorer. Se [disk storlekarna](#disk-sizes) för de tillåtna värdena för `maxShares` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager-mall](#tab/azure-resource-manager)

Innan du använder följande mall ersätter du `[parameters('dataDiskName')]` , `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` och `[parameters('maxShares')]` med dina egna värden.

[Mall för Premium SSD delad disk](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Distribuera en Ultra disk som en delad disk

Om du vill distribuera en hanterad disk med funktionen delad disk aktive rad, ändra `maxShares` parametern till ett värde som är större än 1. Detta gör disk delningen över flera virtuella datorer.

> [!IMPORTANT]
> Värdet för `maxShares` kan bara anges eller ändras när en disk demonteras från alla virtuella datorer. Se [disk storlekarna](#disk-sizes) för de tillåtna värdena för `maxShares` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Exempel på regional disk

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Exempel på zonindelade-disk

Det här exemplet är nästan samma som föregående, men det skapar en disk i tillgänglighets zon 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Exempel på regional disk

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Exempel på zonindelade-disk

Det här exemplet är nästan samma som föregående, men det skapar en disk i tillgänglighets zon 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager-mall](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Exempel på regional disk

Innan du använder följande mall ersätter du,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` och `[parameters('diskMBpsReadOnly')]` med dina egna värden.

[Regional, delad Ultra disks-mall](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Exempel på zonindelade-disk

Innan du använder följande mall ersätter du,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` och `[parameters('diskMBpsReadOnly')]` med dina egna värden.

[Zonindelade delade Ultra disks-mall](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Använda Azure delade diskar med dina virtuella datorer

När du har distribuerat en delad disk med `maxShares>1` kan du montera disken till en eller flera av dina virtuella datorer.

> [!NOTE]
> Om du distribuerar en Ultra disk måste du kontrol lera att den uppfyller de nödvändiga kraven. Mer information finns i avsnittet [PowerShell](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm-1) eller [CLI](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm) i artikeln Ultra disk.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>SCSI PR-kommandon som stöds

När du har monterat den delade disken till dina virtuella datorer i klustret kan du upprätta kvorum och läsa/skriva till disken med SCSI PR. Följande PR-kommandon är tillgängliga när du använder Azure delade diskar:

Om du vill interagera med disken börjar du med listan persistent-reservation-åtgärd:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

När du använder PR_RESERVE, PR_PREEMPT_RESERVATION eller PR_RELEASE_RESERVATION anger du någon av följande beständiga reservations typer:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Du måste också ange en permanent reservations nyckel när du använder PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION eller PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Nästa steg

Om du föredrar att använda Azure Resource Manager mallar för att distribuera disken är följande exempel mallar tillgängliga:
- [Premium SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Regionala Ultra-diskar](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Zonindelade ultra-diskar](https://aka.ms/SharedUltraDiskARMtemplateZonal)