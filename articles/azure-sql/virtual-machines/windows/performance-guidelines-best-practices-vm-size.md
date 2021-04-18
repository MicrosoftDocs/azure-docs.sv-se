---
title: 'VM-storlek: Metodtips för prestanda & riktlinjer'
description: Innehåller riktlinjer för VM-storlek och metodtips för att optimera prestanda för SQL Server på virtuella Azure-datorer (VM).
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
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600904"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM-storlek: Metodtips för prestanda för SQL Server virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller riktlinjer för VM-storlek och innehåller en serie metodtips och riktlinjer för att optimera prestanda för dina SQL Server på Azure Virtual Machines (VM).

Det finns vanligtvis en avvägning mellan kostnadsoptimering och prestandaoptimering. Den här serien med metodtips för prestanda fokuserar *på* att få bästa prestanda för SQL Server i Azure Virtual Machines. Om din arbetsbelastning är mindre krävande kanske du inte behöver alla rekommenderade optimeringar. Överväg dina prestandabehov, kostnader och arbetsbelastningsmönster när du utvärderar dessa rekommendationer.


## <a name="checklist"></a>Checklista

Läs följande checklista för en kort översikt över metodtipsen för VM-storlek som resten av artikeln beskriver i detalj: 

- Använd VM-storlekar med 4 eller fler virtuella processor, till [exempel Standard_M8-4ms,](/azure/virtual-machines/m-series) [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series), [eller DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) eller högre. 
- Använd [minnesoptimerade](../../../virtual-machines/sizes-memory.md) virtuella datorstorlekar för bästa prestanda för SQL Server arbetsbelastningar. 
- [DSv2 11-15-,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4-serien,](../../../virtual-machines/edv4-edsv4-series.md) [M-](/azure/virtual-machines/m-series)och [Mv2-serien](../../../virtual-machines/mv2-series.md) erbjuder det optimala förhållandet mellan minne och virtuella kärnor som krävs för OLTP-arbetsbelastningar. Båda de virtuella datorerna i M-serien erbjuder det högsta förhållandet mellan minne och vCore som krävs för verksamhetskritiska arbetsbelastningar och är också idealiska för arbetsbelastningar i informationslager. 
- Överväg ett högre förhållande mellan minne och vCore för verksamhetskritiska arbetsbelastningar och informationslagerarbetsbelastningar. 
- Använd Marketplace-avbildningarna för virtuella Azure-datorer när SQL Server och lagringsalternativen konfigureras för optimala SQL Server prestanda. 
- Samla in målarbetsbelastningens prestandaegenskaper och använd dem för att fastställa lämplig VM-storlek för ditt företag.

Om du vill jämföra checklistan för VM-storlek med de andra kan du se den omfattande [checklistan med metodtips för prestanda.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Översikt

När du skapar en SQL Server virtuell Azure-dator bör du noga överväga vilken typ av arbetsbelastning som krävs. Om du migrerar en befintlig miljö samlar du in en [prestandabaslinje för](performance-guidelines-best-practices-collect-baseline.md) att fastställa SQL Server krav för virtuella Azure-datorer. Om det här är en ny virtuell dator skapar du ditt nya SQL Server VM baserat på dina leverantörskrav. 

Om du skapar en ny SQL Server VM med ett nytt program som skapats för molnet kan du enkelt ändra storlek på SQL Server VM allt eftersom dina data- och användningskrav utvecklas.
Starta utvecklingsmiljöerna med D-serien, B-serien eller Av2-serien på lägre nivå och utöka din miljö med tiden. 

Använd SQL Server VM Marketplace-avbildningar med lagringskonfigurationen i portalen. Detta gör det enklare att skapa de lagringspooler som behövs för att få den storlek, IOPS och det dataflöde som krävs för dina arbetsbelastningar. Det är viktigt att välja SQL Server virtuella datorer som stöder premiumlagring och premiumlagringscachelagring. Mer information [finns i](performance-guidelines-best-practices-storage.md) lagringsartikeln. 

Det rekommenderade minimikravet för en OLTP-produktionsmiljö är 4 virtuella kärnor, 32 GB minne och ett förhållande mellan minne och vCore på 8. För nya miljöer börjar du med 4 virtuella kärnor och skalar till 8, 16, 32 virtuella kärnor eller mer när dina data- och beräkningskrav ändras. För OLTP-dataflöde SQL Server virtuella datorer som har 5 000 IOPS för varje vCore. 

SQL Server datalager- och verksamhetskritiska miljöer behöver ofta skalas bortom förhållandet mellan minne och vCore med 8. För medelstora miljöer kanske du vill välja ett förhållande på 16 minnes-till-v-kärnor och ett förhållande mellan minne och vCore för större informationslagermiljöer. 

SQL Server datalagermiljöer drar ofta nytta av parallell bearbetning av större datorer. Därför är M-serien och Mv2-serien starka alternativ för större informationslagermiljöer.

Använd vCPU:n och minneskonfigurationen från källdatorn som baslinje för att migrera en aktuell lokal SQL Server-databas för att SQL Server på virtuella Azure-datorer. Ta din kärnlicens till Azure för att dra nytta [av Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) och spara SQL Server dina licenskostnader.

## <a name="memory-optimized"></a>Minnesoptimerad

Storlekar [på minnesoptimerade virtuella datorer är](../../../virtual-machines/sizes-memory.md) ett primärt mål för virtuella SQL Server och det rekommenderade valet av Microsoft. De minnesoptimerade virtuella datorerna erbjuder starkare förhållanden mellan minne och cpu och mellanstora cachealternativ. 

### <a name="m-mv2-and-mdsv2-series"></a>M-, Mv2- och Mdsv2-serien

[M-serien erbjuder](/azure/virtual-machines/m-series) antal virtuella kärnor och minne för några av de största SQL Server arbetsbelastningar.  

[Mv2-serien har](../../../virtual-machines/mv2-series.md) högst antal virtuella kärnor och minne och rekommenderas för verksamhetskritiska arbetsbelastningar och arbetsbelastningar i informationslager. Instanser i Mv2-serien är minnesoptimerade VM-storlekar som ger oöverträffad beräkningsprestanda för att stödja stora minnesintensiva databaser och arbetsbelastningar med ett högt förhållande mellan minne och CPU som är perfekt för relationsdatabasservrar, stora cacheminnen och minnesbaserad analys.

Den [Standard_M64ms](/azure/virtual-machines/m-series) har till exempel ett förhållande på 28 mellan minne och virtuella kärnor.

[Mdsv2 Medium Memory-serien](../../..//virtual-machines/msv2-mdsv2-series.md) är en ny [](https://aka.ms/Mv2MedMemoryPreview) M-serie som för närvarande är en förhandsversion som erbjuder en mängd virtuella Azure-datorer på M-serienivå med en medelhög minnesanvändning. Dessa datorer passar bra för SQL Server arbetsbelastningar med minst 10 stöd för minne till virtuella kärnor på upp till 30.

Några av funktionerna i M- och Mv2-serien som är snygga för SQL Server-prestanda är [premiumlagring](../../../virtual-machines/premium-storage-performance.md) och cachelagringsstöd för [premiumlagring,](../../../virtual-machines/premium-storage-performance.md#disk-caching) [ultradiskstöd](../../../virtual-machines/disks-enable-ultra-ssd.md) och [skrivacceleration](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4-serien

[Edsv4-serien är](../../../virtual-machines/edv4-edsv4-series.md) utformad för minnesintensiva program. Dessa virtuella datorer har en stor lokal lagrings-SSD-kapacitet, stark lokal disk-IOPS, upp till 504 GiB RAM. Det finns en nästan konsekvent 8 GiB minne per virtuell kärnor på de flesta av dessa virtuella datorer, vilket är idealiskt för standard SQL Server arbetsbelastningar. 

Det finns en ny virtuell dator i den här gruppen [med Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) som erbjuder 80 virtuella kärnor, 504 GB minne, med ett förhållande mellan minne och virtuell kärnor på 6. Den här virtuella datorn är [](../../../virtual-machines/isolation.md) märkbar eftersom den är isolerad, vilket innebär att den garanterat är den enda virtuella datorn som körs på värden och därför är isolerad från andra kundarbetsbelastningar. Detta har ett förhållande mellan minne och vCore som är lägre än vad som rekommenderas för SQL Server, så det bör endast användas om isolering krävs.

De virtuella datorerna i Edsv4-serien har stöd [för Premium Storage](../../../virtual-machines/premium-storage-performance.md)och [cachelagring i Premium Storage.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

### <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

[DSv2-serien 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har samma minnes- och diskkonfigurationer som den tidigare D-serien. Den här serien har ett konsekvent förhållande mellan minne och cpu på 7 för alla virtuella datorer. Detta är den minsta av de minnesoptimerade serierna och är ett bra alternativ till låg kostnad för SQL Server arbetsbelastningar.

[DSv2-serien 11–15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) har stöd för [premiumlagring](../../../virtual-machines/premium-storage-performance.md) och [cachelagring](../../../virtual-machines/premium-storage-performance.md#disk-caching)för premiumlagring, vilket rekommenderas starkt för optimala prestanda.

## <a name="general-purpose"></a>Generellt syfte

Storlekar [på virtuella](../../../virtual-machines/sizes-general.md) datorer för generell användning är utformade för att ge balanserade förhållanden mellan minne och virtuell kärnor för mindre arbetsbelastningar på ingångsnivå, till exempel utveckling och testning, webbservrar och mindre databasservrar. 

På grund av de mindre förhållandet mellan minne och virtuell kärnor med virtuella datorer för generell användning är det viktigt att noggrant övervaka minnesbaserade prestandaräknare för att säkerställa att SQL Server kan få det buffertcacheminne som behövs. Mer information [finns i Baslinje](performance-guidelines-best-practices-collect-baseline.md#memory) för minnesprestanda. 

Eftersom startrekommendationen för produktionsarbetsbelastningar är ett förhållande mellan minne och virtuell kärnor på 8 är den minsta rekommenderade konfigurationen för en virtuell dator för generell användning som kör SQL Server 4 virtuella processor och 32 GB minne. 

### <a name="ddsv4-series"></a>Ddsv4-serien

[Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) erbjuder en rättvis kombination av vCPU, minne och temporär disk, men med mindre stöd för minne till vCore. 

De virtuella Ddsv4-datorerna har kortare svarstid och lokal lagring med högre hastighet.

De här datorerna är idealiska för SQL-distributioner sida vid sida och appdistributioner som kräver snabb åtkomst till temporär lagring och avdelningsrelationella databaser. Det finns ett standardförhållande mellan minne och virtuell kärnor på 4 för alla virtuella datorer i den här serien. 

Därför rekommenderar vi att du använder D8ds_v4 som den virtuella startdatorn i den här serien, som har 8 virtuella kärnor och 32 GB minne. Den största datorn är D64ds_v4, som har 64 virtuella kärnor och 256 GB minne.

Virtuella [datorer i Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har stöd för [premiumlagring och](../../../virtual-machines/premium-storage-performance.md) [cachelagring i Premium Storage.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> [Ddsv4-serien](../../../virtual-machines/ddv4-ddsv4-series.md) har inte förhållandet mellan minne och vCore på 8 som rekommenderas för SQL Server arbetsbelastningar. Därför bör du överväga att använda dessa virtuella datorer endast för mindre program- och utvecklingsarbetsbelastningar.

### <a name="b-series"></a>B-serien

Storlekar på burstbara virtuella datorer i [B-serien](../../../virtual-machines/sizes-b-series-burstable.md) är idealiska för arbetsbelastningar som inte behöver konsekventa prestanda, till exempel konceptbevis och mycket små program- och utvecklingsservrar. 

De flesta av [storlekarna för burst-able B-seriens](../../../virtual-machines/sizes-b-series-burstable.md) virtuella datorer har ett förhållande mellan minne och virtuell kärnor på 4. Den största av dessa datorer är [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) med 20 virtuella kärnor och 80 GB minne.

Den här serien är unik eftersom  apparna kan burst-bearbetas under arbetstid med burstable-krediter som varierar beroende på datorstorlek. 

När krediterna är slut återgår den virtuella datorn till baslinjedatorns prestanda.

Fördelen med B-serien är de beräkningsbesparingar du kan uppnå jämfört med de andra VM-storlekarna i andra serier, särskilt om du behöver bearbetningskraften sparsamt under dagen.

Den här serien stöder [Premium Storage,](../../../virtual-machines/premium-storage-performance.md)men **har inte stöd för** [cachelagring i Premium Storage.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> Burstable [B-serien](../../../virtual-machines/sizes-b-series-burstable.md) har inte förhållandet mellan minne och vCore på 8 som rekommenderas för SQL Server arbetsbelastningar. Därför bör du överväga att använda dessa virtuella datorer endast för mindre program, webbservrar och utvecklingsarbetsbelastningar.

### <a name="av2-series"></a>Av2-serien

De [virtuella datorerna i Av2-serien](../../../virtual-machines/av2-series.md) passar bäst för arbetsbelastningar på ingångsnivå som utveckling och testning, webbservrar med låg trafik, små till medelstora appdatabaser och konceptbevis.

Endast [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 virtuella kärnor och 16 GB minne), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 virtuella kärnor och 32 GB minne) och [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 virtuella kärnor och 64 GB minne) har ett bra förhållande mellan minne och virtuell kärnor på 8 för de tre främsta virtuella datorerna. 

Dessa virtuella datorer är båda bra alternativ för mindre utveckling och testning SQL Server datorer. 

8 virtuella [kärnor Standard_A8m_v2](../../../virtual-machines/av2-series.md) också vara ett bra alternativ för små program- och webbservrar.

> [!NOTE] 
> Av2-serien stöder inte premiumlagring och rekommenderas därför inte för arbetsbelastningar i produktions- SQL Server även med de virtuella datorer som har ett förhållande mellan minne och virtuell kärnor på 8.

## <a name="storage-optimized"></a>Lagringsoptimerad

De [lagringsoptimerade VM-storlekarna](../../../virtual-machines/sizes-storage.md) är för specifika användningsfall. De här virtuella datorerna är särskilt utformade med optimerat diskgenomflöde och I/O. 

### <a name="lsv2-series"></a>Lsv2-serien

[Lsv2-serien har](../../../virtual-machines/lsv2-series.md) högt dataflöde, låg latens och lokal NVMe-lagring. De virtuella datorerna i Lsv2-serien är optimerade för att använda den lokala disken på noden som är direkt kopplad till den virtuella datorn i stället för att använda beständiga datadiskar. 

Dessa virtuella datorer är starka alternativ för stordata, informationslager, rapportering och ETL-arbetsbelastningar. Det höga dataflödet och IOPS för den lokala NVMe-lagringen är ett bra användningsfall för bearbetning av filer som läses in i databasen och andra scenarier där data kan återskapas från källsystemet eller andra lagringslager som Azure Blob Storage eller Azure Data Lake. [Lsv2-serien](../../../virtual-machines/lsv2-series.md) Virtuella datorer kan också burst-förbättra diskprestanda i upp till 30 minuter i taget.

Dessa virtuella datorer har storleken 8 till 80 virtuella processorer med 8 GiB minne per virtuell processor och för varje 8 virtuella processorer finns det 1,92 TB NVMe SSD. Det innebär att för den största virtuella datorn i den här [serien, L80s_v2,](../../../virtual-machines/lsv2-series.md)finns det 80 vCPU och 640 BiB minne med 10 x 1,92 TB NVMe-lagring.  Det finns ett konsekvent förhållande mellan minne och virtuell kärnor på 8 för alla dessa virtuella datorer.

NVMe-lagringen är tillfälliga, vilket innebär att data går förlorade på dessa diskar om du frisläpper den virtuella datorn eller om den flyttas till en annan värd för tjänstläsning.

Lsv2- och Ls-serien stöder [premiumlagring,](../../../virtual-machines/premium-storage-performance.md)men inte cachelagring i premiumlagring. Det finns inte stöd för att skapa en lokal cache för att öka IOPs. 

> [!WARNING]
> Lagring av dina datafiler på den tillfälliga NVMe-lagringen kan leda till dataförlust när den virtuella datorn frisläpps. 

## <a name="constrained-vcores"></a>Begränsade virtuella kärnor

Högpresterande SQL Server arbetsbelastningar behöver ofta större mängder minne, I/O och dataflöde utan de högre antalet virtuella kärnor. 

De flesta OLTP-arbetsbelastningar är programdatabaser som drivs av ett stort antal mindre transaktioner. Med OLTP-arbetsbelastningar läses eller ändras bara en liten mängd data, men volymerna av transaktioner som drivs av antalet användare är mycket högre. Det är viktigt att ha SQL Server tillgängligt minne för cacheplaner, lagra nyligen använda data för prestanda och se till att fysiska läsningar snabbt kan läsas in i minnet. 

Dessa OLTP-miljöer behöver högre mängder minne, snabb lagring och den I/O-bandbredd som krävs för att fungera optimalt. 

För att upprätthålla den här prestandanivån utan de högre SQL Server licenskostnaderna erbjuder Azure VM-storlekar med begränsade [vCPU-antal.](../../../virtual-machines/constrained-vcpu.md) 

På så sätt kan du kontrollera licenskostnaderna genom att minska de tillgängliga virtuella kärnorna samtidigt som du behåller samma minne, lagring och I/O-bandbredd för den överordnade virtuella datorn.

Antalet vCPU:er kan begränsas till en halv till en fjärdedel av den ursprungliga VM-storleken. Om du minskar de virtuella kärnorna som är tillgängliga för den virtuella datorn får du högre förhållande mellan minne och virtuell kärnor, men beräkningskostnaden förblir densamma.

Dessa nya VM-storlekar har ett suffix som anger antalet aktiva virtuella processorer så att de blir lättare att identifiera. 

[M64–32](../../../virtual-machines/constrained-vcpu.md) ms kräver till exempel endast licensiering av 32 SQL Server virtuella kärnor med minnet, I/O och dataflödet för [M64:erna,](/azure/virtual-machines/m-series) och [M64–16 ms](../../../virtual-machines/constrained-vcpu.md) kräver endast licensiering av 16 virtuella kärnor.  Även om [M64-16ms](../../../virtual-machines/constrained-vcpu.md) har ett kvartal av SQL Server-licensieringskostnaden för M64ms är beräkningskostnaden för den virtuella datorn densamma.

> [!NOTE] 
> - Arbetsbelastningar för medelstora till stora informationslager kan fortfarande dra nytta av begränsade virtuella datorer med virtuella [kärnor,](../../../virtual-machines/constrained-vcpu.md)men arbetsbelastningar i informationslagret kännetecknas ofta av färre användare och processer som hanterar större mängder data via frågeplaner som körs parallellt. 
> - Beräkningskostnaden, som omfattar operativsystemlicensiering, förblir densamma som den överordnade virtuella datorn. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i de andra artiklarna i den här serien:
- [Snabb checklista](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Samla in baslinje](performance-guidelines-best-practices-collect-baseline.md)

Metodtips för säkerhet finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Läs andra SQL Server virtual machine-artiklar [SQL Server på Azure Virtual Machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md). 
