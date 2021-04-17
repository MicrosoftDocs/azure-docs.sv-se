---
title: Redundansalternativ för Azure Managed Disks
description: Lär dig mer om zonredundant lagring och lokalt redundant lagring för Azure-hanterade diskar.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588096"
---
# <a name="redundancy-options-for-managed-disks"></a>Redundansalternativ för hanterade diskar

Azure Managed Disks erbjuder två alternativ för lagringsredundans, zonredundant lagring (ZRS) som förhandsversion och lokalt redundant lagring. ZRS ger högre tillgänglighet för hanterade diskar än lokalt redundant lagring (LRS). Skrivfördröjningen för LRS-diskar är dock bättre än ZRS-diskar eftersom LRS-diskar synkront skriver data till tre kopior i ett enda datacenter.

## <a name="locally-redundant-storage-for-managed-disks"></a>Lokalt redundant lagring för hanterade diskar

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom ett enda datacenter i den valda regionen. LRS skyddar dina data mot serverrack och enhetsfel. 

Det finns några olika sätt att skydda ditt program med LRS-diskar från ett helt zonfel som kan inträffa på grund av naturkatastrofer eller maskinvaruproblem:
- Använd ett program som SQL Server AlwaysOn, som kan skriva data synkront till två zoner och automatiskt redundans till en annan zon under en katastrof.
- Gör frekventa säkerhetskopieringar av LRS-diskar med ZRS-ögonblicksbilder.
- Aktivera haveriberedskap mellan zoner för LRS-diskar via [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Haveriberedskap mellan zoner ger dock inte noll mål för återställningspunkt (RPO).

Om arbetsflödet inte stöder synkrona skrivningar på programnivå mellan zoner, eller om programmet måste uppfylla noll RPO, skulle ZRS-diskar vara idealiska.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zonredundant lagring för hanterade diskar (förhandsversion)

Zonredundant lagring (ZRS) replikerar din Azure-hanterade disk synkront över tre Azure-tillgänglighetszoner i den valda regionen. Varje tillgänglighetszon är en separat fysisk plats med fristående strömförsörjning, nedkylning och nätverk. 

Med ZRS-diskar kan du återställa från fel i tillgänglighetszoner. Om en hel zon har gått ned kan en ZRS-disk kopplas till en virtuell dator i en annan zon. Du kan också använda ZRS-diskar som en delad disk för att ge förbättrad tillgänglighet för klustrade eller distribuerade program som SQL FCI, SAP ASCS/SCS eller GFS2. Du kan koppla en delad ZRS-disk till primära och sekundära virtuella datorer i olika zoner för att dra nytta av både ZRS och [Tillgänglighetszoner](../availability-zones/az-overview.md). Om den primära zonen misslyckas kan du snabbt växla över till den sekundära virtuella datorn med hjälp av [SCSI-beständig reservation.](disks-shared-enable.md#supported-scsi-pr-commands)

### <a name="limitations"></a>Begränsningar

I förhandsversionen har ZRS för hanterade diskar följande begränsningar:

- Stöds endast med Premium SSD-enheter (Solid State Drives) och Standard SSD.
- För närvarande endast tillgängligt i regionen EastUS2EUAP.
- ZRS-diskar kan bara skapas med Azure Resource Manager med hjälp av `2020-12-01` API:et.

Registrera dig för förhandsversionen [här.](https://aka.ms/ZRSDisksPreviewSignUp)

### <a name="billing-implications"></a>Faktureringskonsekvenser

Mer information finns på [sidan med priser för Azure.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="comparison-with-other-disk-types"></a>Jämförelse med andra disktyper

Förutom mer skrivfördröjning är diskar som använder ZRS identiska med diskar som använder LRS. De har samma prestandamål. Vi rekommenderar att du utför [disktestning](disks-benchmarks.md) för att simulera programmets arbetsbelastning för att jämföra svarstiden mellan LRS- och ZRS-diskarna. 

### <a name="create-zrs-managed-disks"></a>Skapa ZRS-hanterade diskar

Använd `2020-12-01` API:et med din Azure Resource Manager för att skapa en ZRS-disk.

#### <a name="create-a-vm-with-zrs-disks"></a>Skapa en virtuell dator med ZRS-diskar

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Skapa virtuella datorer med en delad ZRS-disk som är ansluten till de virtuella datorerna i olika zoner

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Skapa en VM-skalningsuppsättning med ZRS-diskar

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Nästa steg

- Använd dessa [exempelmallar Azure Resource Manager för att skapa en virtuell dator med ZRS-diskar](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).
