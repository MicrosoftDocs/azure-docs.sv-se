---
title: Konfigurera lagring för SQL Server virtuella datorer | Microsoft Docs
description: I det här avsnittet beskrivs hur Azure konfigurerar lagring för SQL Server virtuella datorer under etablering (Azure Resource Manager distributions modell). Det förklarar också hur du kan konfigurera lagring för befintliga SQL Server virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565434"
---
# <a name="configure-storage-for-sql-server-vms"></a>Konfigurera lagring för SQL Server virtuella datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln lär dig hur du konfigurerar din lagring för din SQL Server på Azure Virtual Machines (VM).

SQL Server virtuella datorer som distribueras via Marketplace-avbildningar följer automatiskt standard [metod tips för lagring](performance-guidelines-best-practices-storage.md) som kan ändras under distributionen. Vissa av dessa konfigurations inställningar kan ändras efter distributionen. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill använda de automatiserade konfigurations inställningarna för lagring måste den virtuella datorn ha följande egenskaper:

* Etablerad med en [SQL Server Galleri avbildning](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) eller registrerad med [SQL IaaS-tillägget]().
* Använder [distributions modellen för Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Använder [Premium-SSD](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nya virtuella datorer

I följande avsnitt beskrivs hur du konfigurerar lagring för nya SQL Server virtuella datorer.

### <a name="azure-portal"></a>Azure Portal

När du konfigurerar en virtuell Azure-dator med hjälp av en SQL Server Galleri avbildning väljer du **ändra konfiguration** på fliken **SQL Server inställningar** för att öppna konfigurations sidan Prestandaoptimerad lagring. Du kan antingen lämna värdena som standard eller ändra vilken typ av disk konfiguration som passar dina behov bäst utifrån din arbets belastning. 

![Skärm bild som visar fliken SQL Server inställningar och alternativet ändra konfiguration.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Välj den typ av arbets belastning som du distribuerar SQL Server för under **lagrings optimering**. Med alternativet **allmän** optimering får du som standard en datadisk med 5000 högsta IOPS och du kommer att använda samma enhet för dina data, transaktions logg och tempdb-lagring. 

Om du väljer antingen transaktions **bearbetning** (OLTP) eller **data lager** skapas en separat disk för data, en separat disk för transaktions loggen och Använd lokal SSD för tempdb. Det finns inga lagrings skillnader mellan **transaktions bearbetning** och **data lager** hantering, men det ändrar [stripe-konfigurationen och spårnings flaggor](#workload-optimization-settings). Om du väljer Premium Storage konfigureras cachelagringen till *ReadOnly* för data enheten, och *ingen* för logg enheten enligt [SQL Server VM bästa metoder för prestanda](performance-guidelines-best-practices.md). 

![SQL Server VM lagrings konfiguration under etableringen](./media/storage-configuration/sql-vm-storage-configuration.png)

Disk konfigurationen är helt anpassningsbar så att du kan konfigurera lagrings miljön, disktyp och IOPs som du behöver för din SQL Server VM arbets belastning. Du kan också använda UltraSSD (för hands version) som ett alternativ för **disk typen** om din SQL Server VM finns i någon av de regioner som stöds (USA, östra 2, Sydostasien och Europa) och du har aktiverat [Ultra disks för din prenumeration](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Dessutom kan du ange cachelagring för diskarna. Virtuella Azure-datorer har en teknik för cachelagring på flera nivåer som kallas [BLOB-cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) vid användning med [Premium-diskar](../../../virtual-machines/disks-types.md#premium-ssd). BLOB-cachen använder en kombination av den virtuella datorns RAM-minne och lokal SSD för cachelagring. 

Diskcachelagring för Premium SSD kan vara *ReadOnly*, *readwrite* eller *none*. 

- *ReadOnly* -cachelagring är mycket bra för SQL Server datafiler som lagras på Premium Storage. *ReadOnly* -cachelagring ger låg Läs latens, hög Läs-IOPS och data flöde eftersom läsningar utförs från cache, som är inom det virtuella dator minnet och lokal SSD. Dessa läsningar är mycket snabbare än läsningar från datadisk, som är från Azure Blob Storage. Premium Storage räknar inte vilka läsningar som hanteras från cachen mot disken IOPS och data flödet. Därför kan din lämplighet uppnå högre total IOPS och data flöde. 
- *Ingen* cache-konfiguration ska användas för diskarna som är värd för SQL Server logg filen eftersom logg filen skrivs sekventiellt och inte drar nytta av *ReadOnly* -cachelagring. 
- *Readwrite* -cachelagring ska inte användas som värd för SQL Server filer eftersom SQL Server inte stöder data konsekvens med *readwrite* -cachen. Skriver avfalls kapaciteten för den *ReadOnly* BLOB-cachen och fördröjningen ökar om skrivningar går genom *ReadOnly* BLOB cache-lager. 


   > [!TIP]
   > Se till att lagrings konfigurationen matchar de begränsningar som angetts av den valda virtuella dator storleken. Att välja lagrings parametrar som överskrider prestanda gränsen för VM-storleken resulterar i varning: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Sänk antingen IOPs genom att ändra disk typen eller öka begränsningen för prestanda begränsning genom att öka storleken på den virtuella datorn. Det går inte att stoppa etableringen. 


Baserat på dina val utför Azure följande konfigurations åtgärder för lagring när du har skapat den virtuella datorn:

* Skapar och kopplar Premium-SSD till den virtuella datorn.
* Konfigurerar data diskarna så att de kan nås SQL Server.
* Konfigurerar data diskarna i en lagringspool baserat på de angivna kraven för storlek och prestanda (IOPS och data flöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.
* Optimerar den här nya enheten baserat på din angivna arbets belastnings typ (data lager, transaktionell bearbetning eller allmänt).

En fullständig genom gång av hur du skapar en SQL Server VM i Azure Portal finns i [vägledningen för etablering](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Om du använder följande Resource Manager-mallar bifogas två Premium-datadiskar som standard, utan konfiguration av lagringspool. Du kan dock anpassa dessa mallar om du vill ändra antalet Premium-datadiskar som är anslutna till den virtuella datorn.

* [Skapa en virtuell dator med automatisk säkerhets kopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa en virtuell dator med automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa en virtuell dator med AKV-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Snabbstartsmall

Du kan använda följande snabb starts mall för att distribuera en SQL Server VM med lagrings optimering. 

* [Skapa en virtuell dator med lagrings optimering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Skapa en virtuell dator med UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer kan du ändra vissa lagrings inställningar i Azure Portal. Öppna din [resurs för virtuella SQL-datorer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)och välj **Översikt**. På sidan SQL Server översikt visas den aktuella lagrings användningen för den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visas lagrings utrymmet i fyra delar:

* SQL-data
* SQL-logg
* Annat (icke-SQL-lagring)
* Tillgänglig

Om du vill ändra lagrings inställningarna väljer du **Konfigurera** under **Inställningar**. 

![Skärm bild som markerar alternativet Konfigurera och lagrings användning.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Du kan ändra disk inställningarna för de enheter som konfigurerades under SQL Server VM skapande processen. Om du väljer **utöka enhet** öppnas sidan ändring av enhet, så att du kan ändra disk typen, samt lägga till ytterligare diskar. 

![Konfigurera lagring för befintliga SQL Server VM](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatiserade ändringar

Det här avsnittet innehåller en referens för de lagrings konfigurations ändringar som Azure utför automatiskt under SQL Server VM etablering eller konfiguration i Azure Portal.

* Azure konfigurerar en lagringspool från det lagrings utrymme som valts från den virtuella datorn. Nästa avsnitt av det här avsnittet innehåller information om konfigurationen av lagringspoolen.
* Automatisk lagrings konfiguration använder alltid [Premium SSD](../../../virtual-machines/disks-types.md) P30-datadiskar. Det finns därför en 1:1-mappning mellan det valda antalet terabyte och antalet data diskar som är anslutna till den virtuella datorn.

Information om priser finns på sidan för [lagrings priser](https://azure.microsoft.com/pricing/details/storage) på fliken **disklagring** .

### <a name="creation-of-the-storage-pool"></a>Skapandet av lagringspoolen

Azure använder följande inställningar för att skapa lagringspoolen på SQL Server virtuella datorer.

| Inställning | Värde |
| --- | --- |
| Rand storlek |256 KB (data lager hantering); 64 KB (transaktion) |
| Disk storlekar |1 TB varje |
| Cache |Läs |
| Fördelnings storlek |storlek för 64 KB NTFS-allokeringsenhet |
| Återställning | Enkel återställning (ingen återhämtning) |
| Antal kolumner |Antal data diskar upp till 8<sup>1</sup> |


<sup>1</sup> när lagringspoolen har skapats kan du inte ändra antalet kolumner i lagringspoolen.


### <a name="workload-optimization-settings"></a>Inställningar för arbets belastnings optimering

I följande tabell beskrivs de tre tillgängliga alternativen för arbets belastnings typer och deras motsvarande optimeringar:

| Arbets belastnings typ | Description | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställning som stöder de flesta arbets belastningar |Inget |
| **Transaktionell bearbetning** |Optimerar lagringen för traditionella databas OLTP-arbetsbelastningar |Spårnings flagga 1117<br/>Spårnings flagga 1118 |
| **Data lager hantering** |Optimerar lagringen för analys-och rapporterings arbets belastningar |Spårnings flagga 610<br/>Spårnings flagga 1117 |

> [!NOTE]
> Du kan bara ange arbets belastnings typen när du etablerar en SQL Server virtuell dator genom att välja den i steget lagrings konfiguration.

## <a name="enable-caching"></a>Enable caching 

Ändra principen för cachelagring på disk nivå. Du kan göra det med hjälp av Azure Portal, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk)eller [Azure CLI](/cli/azure/vm/disk). 

Följ dessa steg om du vill ändra principen för cachelagring i Azure Portal:

1. Stoppa din SQL Server-tjänst. 
1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till den virtuella datorn och välj **diskar** under **Inställningar**. 
   
   ![Skärm bild som visar bladet för disk konfiguration för virtuell dator i Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Välj lämplig princip för cachelagring för disken i list rutan. 

   ![Skärm bild som visar konfigureringen av diskcachelagring i Azure Portal.](./media/storage-configuration/azure-disk-config.png)

1. När ändringen träder i bruk startar du om SQL Server VM och startar tjänsten SQL Server. 


## <a name="enable-write-accelerator"></a>Aktivera Skrivningsaccelerator

Skriv acceleration är en disk funktion som endast är tillgänglig för M-seriens Virtual Machines (VM). Syftet med att skriva acceleration är att förbättra I/O-svars tiden för skrivningar mot Azure Premium Storage när du behöver I/O-svars tid med enkel volym på grund av hög verksamhets kritiska OLTP-arbetsbelastningar eller data lager miljöer. 

Stoppa alla SQL Server-aktiviteter och Stäng av tjänsten SQL Server innan du gör ändringar i Skriv accelerations principen. 

Om diskarna är stripe aktiverar du Skriv acceleration för varje disk individuellt, och din virtuella Azure-dator bör stängas av innan du gör några ändringar. 

Följ dessa steg om du vill aktivera Skriv acceleration med Azure Portal:

1. Stoppa din SQL Server-tjänst. Om diskarna är stripe stänger du av den virtuella datorn. 
1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till den virtuella datorn och välj **diskar** under **Inställningar**. 
   
   ![Skärm bild som visar bladet för disk konfiguration för virtuell dator i Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Välj alternativet cache med **Skrivningsaccelerator** för disken i list rutan. 

   ![Skärm bild som visar cache-principen för Write Accelerator.](./media/storage-configuration/write-accelerator.png)

1. När ändringen träder i bruk startar du den virtuella datorn och SQL Server tjänsten. 

## <a name="disk-striping"></a>Disk randning

Du kan lägga till ytterligare data diskar och använda disk ränder för mer data flöde. Om du vill fastställa antalet data diskar analyserar du det data flöde och den bandbredd som krävs för dina SQL Server datafiler, inklusive loggen och tempdb. Data flödes-och bandbredds gränserna varierar beroende på VM-storlek. Läs mer i VM- [storlek](../../../virtual-machines/sizes.md)


* För Windows 8/Windows Server 2012 eller senare använder du [lagrings utrymmen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) med följande rikt linjer:

  1. Ange överlagring (stripe-storlek) till 64 KB (65 536 byte) för att undvika prestanda påverkan på grund av en feljustering av partitionen. Detta måste anges med PowerShell.

  2. Ange kolumn antal = antal fysiska diskar. Använd PowerShell när du konfigurerar fler än 8 diskar (inte Serverhanteraren UI).

Följande PowerShell skapar till exempel en ny lagringspool med Överlagrings storleken till 64 KB och antalet kolumner som motsvarar mängden fysisk disk i lagringspoolen:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * För Windows 2008 R2 eller tidigare kan du använda dynamiska diskar (OS Striped-volymer) och stripe-storleken är alltid 64 KB. Det här alternativet är föråldrat från och med Windows 8/Windows Server 2012. Mer information finns i support policyn på [Virtual Disk Service över gång till Windows Storage Management API](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Om du använder [Lagringsdirigering (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server kluster instanser för växling vid fel](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure)måste du konfigurera en enda pool. Även om olika volymer kan skapas på samma pool, kommer alla att dela samma egenskaper, till exempel samma princip för cachelagring.
 
  * Fastställ antalet diskar som är kopplade till lagringspoolen baserat på dina belastnings förväntningar. Tänk på att olika storlekar på virtuella datorer tillåter olika antal anslutna data diskar. Mer information finns i [storlekar för virtuella datorer](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Nästa steg

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).
