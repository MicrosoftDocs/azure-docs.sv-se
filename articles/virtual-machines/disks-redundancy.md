---
title: Alternativ för redundans för Azure Managed disks
description: Lär dig mer om Zone-redundant lagring och lokalt redundant lagring för Azure Managed disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6cafbff86a55ad0bed7da17fcef1aea2b0a53d1b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680208"
---
# <a name="redundancy-options-for-managed-disks"></a>Alternativ för redundans för hanterade diskar

Azure Managed disks erbjuder två alternativ för lagring av redundans, zon redundant lagring (ZRS) som en för hands version och lokalt redundant lagring. ZRS ger högre tillgänglighet för hanterade diskar än lokalt redundant lagring (LRS). Skriv fördröjningen för LRS-diskar är dock bättre än ZRS-diskar eftersom LRS-diskar synkront skriver data till tre kopior i ett enda data Center.

## <a name="locally-redundant-storage-for-managed-disks"></a>Lokalt redundant lagring för hanterade diskar

Lokalt redundant lagring (LRS) replikerar dina data tre gånger inom ett enda data Center i den valda regionen. LRS skyddar dina data mot Server rack och enhets problem. 

Det finns några sätt som du kan använda för att skydda ditt program med LRS diskar från en hel zon fel som kan uppstå på grund av natur haverier eller maskin varu problem:
- Använd ett program som SQL Server AlwaysOn, som kan skriva data synkront till två zoner och automatiskt redundansväxla till en annan zon under en katastrof.
- Gör frekventa säkerhets kopieringar av LRS-diskar med ZRS-ögonblicksbilder.
- Aktivera haveri beredskap mellan zoner för LRS-diskar via [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Haveri beredskap mellan zoner ger dock ingen återställnings punkt mål.

Om arbets flödet inte stöder synkrona skrivningar på program nivå över zoner, eller om ditt program måste uppfylla noll, så är ZRS-diskar idealiska.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zon-redundant lagring för Managed disks (för hands version)

Zone-redundant lagring (ZRS) replikerar din Azure-hanterade disk synkront över tre tillgänglighets zoner i Azure i den valda regionen. Varje tillgänglighetszon är en separat fysisk plats med fristående strömförsörjning, nedkylning och nätverk. 

Med ZRS-diskar kan du återställa från fel i tillgänglighets zoner. Om en hel zon har gått ned kan en ZRS-disk kopplas till en virtuell dator i en annan zon. Du kan också använda ZRS diskar i kombination med delade diskar för att ge bättre tillgänglighet för klustrade eller distribuerade program som SQL FCI, SAP ASCS/SCS eller GFS2. Du kan koppla en delad ZRS-disk till primära och sekundära virtuella datorer i olika zoner för att dra nytta av både ZRS och [Tillgänglighetszoner](../availability-zones/az-overview.md). Om din primära zon Miss lyckas kan du snabbt redundansväxla till den sekundära virtuella datorn med hjälp av [SCSI-beständig reservation](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Begränsningar

Under för hands versionen har ZRS för Managed disks följande begränsningar:

- Stöds endast med Premium-enheter med solid state-typ (SSD) och standard SSD.
- För närvarande endast tillgängligt i regionen EastUS2EUAP.
- ZRS-diskar kan bara skapas med Azure Resource Manager mallar med hjälp av `2020-12-01` API: et.

Registrera dig för för hands versionen [här](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Debiterings konsekvenser

Mer information finns på [sidan med priser för Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Jämförelse med andra disk typer

Med undantag för mer Skriv fördröjning är diskar som använder ZRS identiska med diskar som använder LRS. De har samma prestanda mål.

### <a name="create-zrs-managed-disks"></a>Skapa ZRS Managed disks

Du måste använda `2020-12-01` API: et med din Azure Resource Manager-mall för att skapa en ZRS-disk.

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

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Skapa en skalnings uppsättning för virtuella datorer med ZRS-diskar

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
