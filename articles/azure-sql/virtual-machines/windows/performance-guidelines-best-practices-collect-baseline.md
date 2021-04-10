---
title: 'Samla in bas linje: metod tips för prestanda & rikt linjer'
description: Innehåller steg för att samla in en prestanda bas linje som rikt linjer för att optimera prestanda för dina SQL Server på virtuella Azure-datorer (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572452"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Samla in bas linje: metod tips för prestanda för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller information om hur du samlar in en prestanda bas linje som en serie bästa praxis och rikt linjer för att optimera prestanda för dina SQL Server på Azure Virtual Machines (VM).

Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här prestanda serien för bästa metoder fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje rekommenderad optimering. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

## <a name="overview"></a>Översikt

För en strategi kan du samla in prestanda räknare med PerfMon/LogMan och Capture SQL Server vänta statistik för att bättre förstå allmänna belastningar och potentiella Flask halsar i käll miljön. 

Börja med att samla in processor, minne, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [data flöde](../../../virtual-machines/premium-storage-performance.md#throughput)och [svars tid](../../../virtual-machines/premium-storage-performance.md#latency) för käll arbets belastningen vid hög belastnings tider enligt [Check listan för program prestanda](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Samla in data under perioder med hög belastning, till exempel arbets belastningar under din normala arbets dag, men även andra hög belastnings processer, till exempel arbets belastningen på arbets dagen och eventuella ETL-arbetsbelastningar. Överväg att skala upp dina resurser för atypically stora arbets belastningar, t. ex. slut på kvartals bearbetning, och skala sedan klart när arbets belastningen har slutförts. 

Använd prestanda analysen för att välja den [VM-storlek](../../../virtual-machines/sizes-memory.md) som kan skalas till arbets Belastningens prestanda krav.


## <a name="storage"></a>Storage

SQL Server prestanda beror kraftigt på i/O-undersystemet och lagrings prestandan mäts av IOPS och data flödet. Om din databas inte passar in i fysiskt minne, SQL Server ständigt databas sidor in och ut ur bufferten. Datafilerna för SQL Server bör behandlas på olika sätt. Åtkomst till loggfiler är sekventiella, förutom när en transaktion måste återställas där datafiler, inklusive tempdb, är slumpmässigt använda. Om du har ett långsamt I/O-undersystem kan användarna uppleva prestanda problem som långsamma svars tider och uppgifter som inte slutförs på grund av timeout. 

Virtuella Azure Marketplace-datorer har loggfiler på en fysisk disk som är separat från datafilerna som standard. Antalet tempdb-datafiler och storlek uppfyller bästa praxis och är riktade till den tillfälliga `D:\` enheten. 

Följande PerfMon-räknare kan hjälpa dig att verifiera det IO-flöde som krävs av din SQL Server: 
* **\LogicalDisk\Disk läsningar/s** (Läs IOPS)
* **\LogicalDisk\Disk skrivningar/SEK** (Skriv-IOPS) 
* **\LogicalDisk\Disk lästa byte/s** (Läs data flödes krav för data-, logg-och tempdb-filer)
* **\LogicalDisk\Disk skrivna byte/s** (Skriv data flödes krav för data-, logg-och tempdb-filer)

Använd IOPS-och data flödes krav på högsta nivå för att utvärdera VM-storlekar som matchar kapaciteten från dina mått. 

Om din arbets belastning kräver 20 000 Read IOPS och 10K Write IOPS, kan du antingen välja E16s_v3 (med upp till 32K cachelagrad och 25600 Uncached IOPS) eller M16_s (med upp till 20 000 cachelagrat och 10 000 Uncached IOPS) med 2 P30 diskar stripade med lagrings utrymmen. 

Se till att förstå både genomflödet och IOPS-krav för arbets belastningen eftersom de virtuella datorerna har olika skalnings gränser för IOPS och data flöde.

## <a name="memory"></a>Minne

Spåra både det externa minnet som används av operativ systemet och det minne som används internt av SQL Server. Att identifiera trycket för en komponent bidrar till att öka storleken på virtuella datorer och identifiera möjligheter för justering. 

Följande PerfMon-räknare kan hjälpa dig att validera minnes hälsan för en SQL Server virtuell dator: 
* [\Memory\Available MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: minne Manager\Target server minne (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: minne Manager\Total server minne (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy skrivningar/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer bufferthanteraren Life förväntad](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Compute i Azure hanteras annorlunda än lokalt. Lokala servrar är byggda för de senaste flera åren utan en uppgradering på grund av hanterings kostnader och kostnader för att förvärva ny maskin vara. Virtualiseringen minskar vissa av dessa problem, men programmen är optimerade för att dra största möjliga nytta av den underliggande maskin varan, vilket innebär att en betydande förändring av resursanvändningen kräver en ombalansering av hela den fysiska miljön. 

Detta är inte en utmaning i Azure där en ny virtuell dator på en annan maskin varu serie, och även i en annan region, är lätt att uppnå. 

I Azure vill du dra nytta av så mycket av de virtuella dator resurserna som möjligt, och därför bör virtuella Azure-datorer konfigureras för att hålla den genomsnittliga CPU: n så hög som möjligt utan att arbets belastningen påverkas. 

Följande PerfMon-räknare kan hjälpa till att verifiera beräknings hälsan för en SQL Server virtuell dator:
* **\Processor information (_Total) \% processor tid**
* **\Process (Sqlservr) \% processor tid**

> [!NOTE] 
> Vi rekommenderar att du strävar efter att använda 80% av din beräkning, med toppar över 90% men inte till 100% under en varaktig tids period. För det grundläggande vill du bara tillhandahålla den beräkning som krävs för programmet och sedan planera för att skala upp eller ned efter behov. 


## <a name="next-steps"></a>Nästa steg

Mer information finns i de andra artiklarna i den här serien:
- [Snabb check lista](performance-guidelines-best-practices-checklist.md)
- [VM-storlek](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).
