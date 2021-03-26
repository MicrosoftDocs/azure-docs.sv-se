---
title: 'VM-storlek: metod tips för prestanda & rikt linjer'
description: Innehåller rikt linjer för VM-storlek och metod tips för att optimera prestanda för dina SQL Server på Azure Virtual Machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572504"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM-storlek: metod tips för prestanda för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller en serie bästa metoder och rikt linjer för att optimera prestanda för dina SQL Server på Azure Virtual Machines (VM).

Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här prestanda serien för bästa metoder fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje rekommenderad optimering. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.


## <a name="checklist"></a>Checklista

Läs igenom följande check lista för en kort översikt över de bästa metoder för VM-storlek som resten av artikeln omfattar i större detalj: 

- Använd VM-storlekar med 4 eller fler vCPU som [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)eller [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) eller högre. 
- Använd [minnesoptimerade](../../../virtual-machines/sizes-memory.md) storlekar på virtuella datorer för att få bästa möjliga prestanda för SQL Server arbets belastningar. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) -serien, [M-](../../../virtual-machines/m-series.md)och [Mv2-](../../../virtual-machines/mv2-series.md) serien erbjuder optimalt minne-till-vCore-förhållande som krävs för OLTP-arbetsbelastningar. Båda virtuella datorerna i M-serien erbjuder det högsta vCore-förhållandet som krävs för verksamhets kritiska arbets belastningar och är också idealiska för arbets belastningar för data lager. 
- Överväg ett högre förhållandet mellan minne och vCore för verksamhets kritiska och informations lager arbets belastningar. 
- Använd Azures Marketplace-avbildningar för virtuella datorer som SQL Server inställningar och lagrings alternativ har kon figurer ATS för optimal SQL Server prestanda. 
- Samla in mål arbets Belastningens prestanda egenskaper och Använd dem för att fastställa lämplig VM-storlek för ditt företag.

Information om hur du jämför check listan för VM-storlekar med de andra finns i [Check lista för övergripande prestanda för bästa praxis](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Översikt

När du skapar en SQL Server på en virtuell Azure-dator bör du noga överväga vilken typ av arbets belastning som behövs. Om du migrerar en befintlig miljö [samlar du in en bas linje för prestanda](performance-guidelines-best-practices-collect-baseline.md) för att fastställa SQL Server på krav för virtuella Azure-datorer. Om det här är en ny virtuell dator kan du skapa din nya SQL Server VM utifrån dina leverantörs krav. 

Om du skapar en ny SQL Server VM med ett nytt program som skapats för molnet kan du enkelt ändra din SQL Server VM när dina data-och användnings krav utvecklas.
Starta utvecklings miljöerna med den lägre nivån D-serien, B-serien eller AV2-serien och utveckla din miljö över tid. 

Använd SQL Server VM Marketplace-avbildningar med lagrings konfigurationen i portalen. Detta gör det enklare att skapa lagringspooler som krävs för att få den storlek, IOPS och data flöde som krävs för dina arbets belastningar. Det är viktigt att välja SQL Server virtuella datorer som har stöd för Premium Storage och Premium Storage-cachelagring. Mer information finns i [lagrings](performance-guidelines-best-practices-storage.md) artikeln. 

Det rekommenderade minimivärdet för en produktions-OLTP-miljö är 4 vCore, 32 GB minne och ett förhållandet mellan minne och vCore på 8. För nya miljöer börjar du med 4 vCore-datorer och skalar till 8, 16, 32 virtuella kärnor eller mer när dina data-och beräknings krav har ändrats. För OLTP-genomflöde är målet SQL Server virtuella datorer som har 5000 IOPS för varje vCore. 

SQL Server data lager och verksamhets kritiska miljöer behöver ofta skalas bortom 8-till-vCore-förhållandet. För medel stora miljöer kanske du vill välja ett 16-till-vCore-förhållande och ett 32-förhållande mellan minne och vCore för större data lager miljöer. 

SQL Server data lager miljöer har ofta nytta av parallell bearbetning av större datorer. Av den anledningen är M-serien och Mv2-serien starka alternativ för större data lager miljöer.

Använd vCPU och minnes konfiguration från käll datorn som en bas linje för att migrera en aktuell lokal SQL Server databas till SQL Server på virtuella Azure-datorer. Ta din kärn licens till Azure för att dra nytta av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) och spara på SQL Server licens kostnader.

## <a name="memory-optimized"></a>Minnesoptimerad

De [minnesoptimerade storlekarna för virtuella](../../../virtual-machines/sizes-memory.md) datorer är ett primärt mål för SQL Server virtuella datorer och det rekommenderade valet av Microsoft. De minnesoptimerade virtuella datorerna ger starkare minnes-till-CPU-förhållande och cache-alternativ mellan medel och stora. 

### <a name="m-mv2-and-mdsv2-series"></a>M, Mv2 och Mdsv2-serien

[M-serien](../../../virtual-machines/m-series.md) erbjuder vCore-räkningar och-minne för några av de största SQL Server arbets belastningarna.  

[Mv2-serien](../../../virtual-machines/mv2-series.md) har högsta antal vCore och minne och rekommenderas för verksamhets kritiska och informations lager arbets belastningar. Mv2-seriens instanser är minnesoptimerade VM-storlekar som ger oöverträffade data prestanda för att stödja stora minnes databaser och arbets belastningar med ett högt förhållande mellan minne och CPU som är perfekt för Relations databas servrar, stora cacheminnen och minnes intern analys.

[Standard_M64ms](../../../virtual-machines/m-series.md) har ett 28-till-vCore-förhållande till exempel.

[Mdsv2 medelhög minnes serie](../../..//virtual-machines/msv2-mdsv2-series.md) är en ny M-serien som för närvarande finns i en för [hands version](https://aka.ms/Mv2MedMemoryPreview) som erbjuder ett utbud av virtuella Azure-datorer i m-serien med ett midtier minnes erbjudande. De här datorerna lämpar sig väl för SQL Server arbets belastningar med minst 10 minnes-till-vCore-stöd upp till 30.

Några av funktionerna i M-och Mv2-serien är attraktiva för SQL Server prestanda, till exempel [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage caching](../../../virtual-machines/premium-storage-performance.md#disk-caching) support, stöd för [mycket disk](../../../virtual-machines/disks-enable-ultra-ssd.md) och [Skriv acceleration](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4-serien

[Edsv4-serien](../../../virtual-machines/edv4-edsv4-series.md) är utformad för minnes intensiva program. De här virtuella datorerna har en stor lokal Storage SSD-kapacitet, stark lokal disk-IOPS, upp till 504 GiB RAM-minne. Det finns nästan konsekvent 8 GiB minne per vCore över de flesta av de virtuella datorerna, vilket är idealiskt för standard SQL Server-arbetsbelastningar. 

Det finns en ny virtuell dator i den här gruppen med [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) som erbjuder 80-virtuella kärnor, 504-GB, med ett minnes-till-vCore-förhållande på 6. Den här virtuella datorn är viktig eftersom den är [isolerad](../../../virtual-machines/isolation.md) , vilket innebär att den är den enda virtuella dator som körs på värden och därför är isolerad från andra kund arbets belastningar. Detta har en vCore-kvot som är lägre än vad som rekommenderas för SQL Server, så den bör endast användas om isolering krävs.

De virtuella datorerna i Edsv4-serien har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md)och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

[DSv2-serien 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har samma minnes-och diskkonfigurationer som tidigare D-serien. Den här serien har ett konsekvent minnes-till-CPU-förhållande på 7 över alla virtuella datorer. Det här är den minsta av de minnesoptimerade serierna och är ett utmärkt alternativ för låg kostnad för SQL Server arbets belastningar på ingångs nivå.

[DSv2-serien 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching), vilket rekommenderas för optimala prestanda.

## <a name="general-purpose"></a>Generellt syfte

Storleken på den [virtuella datorn för generell användning](../../../virtual-machines/sizes-general.md) är utformad för att tillhandahålla balanserade vCore-förhållande för mindre ingångs nivå arbets belastningar som utveckling och testning, webb servrar och mindre databas servrar. 

På grund av de mindre förhållandet mellan vCore och de flesta virtuella datorer är det viktigt att noggrant övervaka minnesbaserade prestanda räknare för att säkerställa att SQL Server kan hämta det minne som krävs för buffertens cacheminne. Mer information finns i [bas linje för minnes prestanda](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Eftersom start rekommendationen för produktions arbets belastningar är en minnes-till-vCore-kvot på 8, är den minsta rekommenderade konfigurationen för en allmän virtuell dator som kör SQL Server 4 vCPU och 32 GB minne. 

### <a name="ddsv4-series"></a>Ddsv4-serien

[Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) erbjuder en rättvis kombination av vCPU, minne och temporär disk, men med mindre stöd för minne till vcore. 

De virtuella Ddsv4-datorerna omfattar lägre svars tid och lokal lagring med högre hastighet.

De här datorerna är idealiska för SQL-och app-distributioner sida vid sida som kräver snabb åtkomst till tillfälliga lagrings-och avdelnings Relations databaser. Det finns ett standard förhållandet mellan vCore och 4 för alla virtuella datorer i den här serien. 

Därför rekommenderar vi att du utnyttjar D8ds_v4 som den virtuella start datorn i den här serien, som har 8 virtuella kärnor och 32 GB minne. Den största datorn är D64ds_v4, som har 64 virtuella kärnor och 256 GB minne.

De virtuella datorerna i [Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har stöd för [Premium Storage](../../../virtual-machines/premium-storage-performance.md) och [Premium Storage-cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har inte vCore-förhållandet 8 som rekommenderas för SQL Server arbets belastningar. Därför bör du överväga att använda dessa virtuella datorer för mindre program-och utvecklings arbets belastningar.

### <a name="b-series"></a>B-serien

Storleken på virtuella datorer med [Burstable i B-serien](../../../virtual-machines/sizes-b-series-burstable.md) är idealisk för arbets belastningar som inte behöver konsekventa prestanda, till exempel POC-koncept och mycket små program-och utvecklings servrar. 

De flesta storlekar för virtuella datorer med [burst-serien](../../../virtual-machines/sizes-b-series-burstable.md) har ett vCore-förhållande på 4. Det största av de här datorerna är [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) med 20 virtuella kärnor och 80 GB minne.

Den här serien är unik eftersom apparna har möjlighet att använda **burst** under kontors tid med stöd för stora krediter baserat på dator storlek. 

När krediterna är uttömda återgår den virtuella datorn till bas linjens dator prestanda.

Fördelen med B-serien är de besparingar som du kan uppnå jämfört med andra VM-storlekar i andra serier, särskilt om du behöver processor kraft sparsamt under dagen.

Den här serien stöder [Premium Storage](../../../virtual-machines/premium-storage-performance.md), men stöder **inte** [cachelagring av Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> Den [Burstable B-serien](../../../virtual-machines/sizes-b-series-burstable.md) har inte vCore-förhållandet 8 som rekommenderas för SQL Server-arbetsbelastningar. Därför bör du överväga att använda dessa virtuella datorer för mindre program, webb servrar och utvecklings arbets belastningar.

### <a name="av2-series"></a>Av2-serien

De virtuella datorerna i [AV2-serien](../../../virtual-machines/av2-series.md) är bäst lämpade för arbets belastningar på ingångs nivå som utveckling och testning, webb servrar för låg trafik, små till medel stora app-databaser och proof-of-Concepts.

Endast [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuella kärnor och 16GBs minne), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuella kärnor och 32GBs) och [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuella kärnor och 64GBs) har ett bra minnes-till-vCore-förhållande på 8 för de tre översta virtuella datorerna. 

De här virtuella datorerna är både lämpliga alternativ för mindre utvecklings-och test SQL Server datorer. 

8-vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) kan också vara ett utmärkt alternativ för små program och webb servrar.

> [!NOTE] 
> AV2-serien har inte stöd för Premium Storage, vilket innebär att det inte rekommenderas för produktion SQL Server arbets belastningar även med de virtuella datorer som har en minnes-till-vCore-kvot på 8.

## <a name="storage-optimized"></a>Lagringsoptimerad

De [optimerade VM-storlekarna för lagring](../../../virtual-machines/sizes-storage.md) är för vissa användnings fall. Dessa virtuella datorer är särskilt utformade med optimerade disk data flöde och IO. 

### <a name="lsv2-series"></a>Lsv2-serien

[Lsv2-serien](../../../virtual-machines/lsv2-series.md) innehåller högt data flöde, låg latens och lokal NVMe-lagring. De virtuella datorerna i Lsv2-serien är optimerade för att använda den lokala disken på noden som är ansluten direkt till den virtuella datorn i stället för att använda varaktiga data diskar 

De här virtuella datorerna är starka alternativ för Big data-, data lager-, rapporterings-och ETL-arbetsbelastningar. Det stora genomflödet och IOPS i den lokala NVMe-lagringen är ett användbart användnings fall för bearbetning av filer som kommer att läsas in i databasen och andra scenarier där data kan återskapas från käll systemet eller andra databaser, till exempel Azure Blob Storage eller Azure Data Lake. [Lsv2-serien](../../../virtual-machines/lsv2-series.md) Virtuella datorer kan också överföra disk prestanda i upp till 30 minuter i taget.

De här virtuella datorernas storlek från 8 till 80 vCPU med 8 GiB minne per vCPU och för varje 8-virtuella processorer finns 1,92 TB NVMe SSD. Det innebär att den största virtuella datorn för den här serien, [L80s_v2](../../../virtual-machines/lsv2-series.md), det finns 80 vCPU och 640 BIB minne med 10X 1.92 TB för NVMe-lagring.  Det finns ett konsekvent minnes-till-vCore-förhållande på 8 över alla de virtuella datorerna.

NVMe-lagringen är tillfällig innebär att data går förlorade på diskarna om du frigör den virtuella datorn eller om den flyttas till en annan värd för tjänst återställning.

Lsv2-och LS-serien stöder [Premium Storage](../../../virtual-machines/premium-storage-performance.md), men inte Premium Storage-cachelagring. Det finns inte stöd för att skapa en lokal cache för att öka IOPs. 

> [!WARNING]
> Lagring av dina datafiler på den tillfälliga NVMe-lagringen kan leda till data förlust när den virtuella datorn frigörs. 

## <a name="constrained-vcores"></a>Begränsade virtuella kärnor

Hög prestanda SQL Server arbets belastningar behöver ofta större mängder minne, I/O och genom strömning utan att det högre antalet vCore. 

De flesta OLTP-arbetsbelastningar är program databaser som styrs av ett stort antal mindre transaktioner. Med OLTP-arbetsbelastningar är det bara en liten mängd data som läses eller ändras, men volymer med transaktioner som används av användar antal är mycket högre. Det är viktigt att SQL Server-minnet är tillgängligt för cachelagring av planer, lagra nyligen använda data för prestanda och se till att fysiska läsningar kan läsas snabbt i minnet. 

Dessa OLTP-miljöer behöver högre mängd minne, snabb lagring och den I/O-bandbredd som krävs för att utföra optimalt. 

För att upprätthålla den här prestanda nivån utan högre SQL Server licensierings kostnader erbjuder Azure VM-storlekar med [begränsade vCPU antal](../../../virtual-machines/constrained-vcpu.md). 

Detta hjälper dig att kontrol lera licensierings kostnaderna genom att minska de tillgängliga virtuella kärnor samtidigt som du behåller samma minne, lagring och I/O-bandbredd för den överordnade virtuella datorn.

Antalet vCPU kan begränsas till en halv fjärdedel av den ursprungliga storleken på den virtuella datorn. Om du minskar virtuella kärnor som är tillgängliga för den virtuella datorn uppnås högre förhållandet mellan minne och vCore, men beräknings kostnaden är oförändrad.

Dessa nya VM-storlekar har ett suffix som anger antalet aktiva virtuella processorer som gör det lättare att identifiera dem. 

Till exempel kräver [M64-32ms](../../../virtual-machines/constrained-vcpu.md) endast licensiering 32 SQL Server virtuella kärnor med minnet, I/O och genom strömningen av [M64ms](../../../virtual-machines/m-series.md) och [M64-16Ms](../../../virtual-machines/constrained-vcpu.md) kräver endast licensiering 16 virtuella kärnor.  Även om [M64-16ms](../../../virtual-machines/constrained-vcpu.md) har ett kvartal av SQL Server licens kostnaden för M64ms, kommer beräknings kostnaden för den virtuella datorn att vara densamma.

> [!NOTE] 
> - Medel stora till stora data lager arbets belastningar kan fortfarande dra nytta av [begränsade virtuella vCore-datorer](../../../virtual-machines/constrained-vcpu.md), men arbets belastningar för data lager kännetecknas ofta av färre användare och processer som kan hantera större data mängder med hjälp av fråge planer som körs parallellt. 
> - Beräknings kostnaden, som omfattar operativ Systems licensiering, är densamma som den överordnade virtuella datorn. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i de andra artiklarna i den här serien:
- [Snabb check lista](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Samla in bas linje](performance-guidelines-best-practices-collect-baseline.md)

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).
