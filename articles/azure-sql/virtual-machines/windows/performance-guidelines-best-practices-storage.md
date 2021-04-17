---
title: 'Lagring: Metodtips för prestanda & riktlinjer'
description: Innehåller metodtips och riktlinjer för lagring för att optimera prestanda för dina SQL Server på virtuella Azure-datorer (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389731"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Lagring: Metodtips för prestanda för SQL Server virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller metodtips och riktlinjer för lagring för att optimera prestanda för SQL Server på Azure Virtual Machines (VM).

Det finns vanligtvis en avvägning mellan kostnadsoptimering och prestandaoptimering. Den här serien med metodtips för prestanda fokuserar på *att få bästa* prestanda för SQL Server på Azure Virtual Machines. Om din arbetsbelastning är mindre krävande kanske du inte behöver alla rekommenderade optimeringar. Överväg dina prestandabehov, kostnader och arbetsbelastningsmönster när du utvärderar dessa rekommendationer.

Mer information finns i de andra artiklarna i den här serien: Checklista [för](performance-guidelines-best-practices-checklist.md)prestanda, [VM-storlek](performance-guidelines-best-practices-vm-size.md)och Samla in [baslinje.](performance-guidelines-best-practices-collect-baseline.md) 

## <a name="checklist"></a>Checklista

Läs följande checklista för en kort översikt över metodtipsen för lagring som beskrivs i resten av artikeln i detalj: 

- Övervaka programmet och fastställa [krav på lagringsbandbredd](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) och svarstid för SQL Server, loggfiler och tempdb-filer innan du väljer disktyp. 
- För att optimera [lagringsprestanda](../../../virtual-machines/premium-storage-performance.md#throttling)bör du planera för högsta tillgängliga IOPS och använda cachelagring av data som en prestandafunktion för dataläsningar samtidigt som du undviker virtuella datorer och diskar med ett tak för .
- Placera data, loggfiler och tempdb-filer på separata enheter.
    - För dataenheten använder du endast [Premium P30- och P40-diskar](../../../virtual-machines/disks-types.md#premium-ssd) för att säkerställa tillgängligheten för cachestöd
    - För loggenhetsplanen för kapacitet och testprestanda jämfört med kostnad vid utvärdering av [Premium P30–P80-diskarna](../../../virtual-machines/disks-types.md#premium-ssd)
      - Om lagringsfördröjning för under millisekunder krävs använder du [Azure Ultra-diskar](../../../virtual-machines/disks-types.md#ultra-disk) för transaktionsloggen. 
      - För distributioner av virtuella datorer i M-serien bör du [överväga att skriva accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) över med Hjälp av Azure Ultra-diskar.
    - Placera [tempdb](/sql/relational-databases/databases/tempdb-database) på den lokala tillfälliga SSD-enheten för de flesta SQL Server `D:\` efter att du har valt den optimala VM-storleken. 
      - Om kapaciteten för den lokala enheten inte är tillräcklig för tempdb bör du överväga att ändra storlek på den virtuella datorn. Mer information [finns i Cachelagringsprinciper](#data-file-caching-policies) för datafiler.
- Stripe multiple Azure data disks using [Storage Spaces](/windows-server/storage/storage-spaces/overview) to increase I/O bandwidth up to the target virtual machine's IOPS and throughput limits .
- Ange [värdcachelagring](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) till skrivskyddade för datafildiskar.
- Ange [värdcachelagring](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) till ingen för loggfilsdiskar.
    - Aktivera inte cachelagring av läsning/skrivning på diskar som innehåller SQL Server filer. 
    - Stoppa alltid SQL Server innan du ändrar diskens cacheinställningar.
- För arbetsbelastningar för utveckling och testning och långsiktig säkerhetskopiering bör du överväga att använda standardlagring. Vi rekommenderar inte att du använder Standard HDD/SDD för produktionsarbetsbelastningar.
- [Kreditbaserad disk bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) bör endast övervägas för mindre dev/test-arbetsbelastningar och avdelningssystem.
- Etablera lagringskontot i samma region som SQL Server VM. 
- Inaktivera geo-redundant lagring i Azure (geo-replikering) och använd LRS (lokal redundant lagring) på lagringskontot.
- Formatera datadisken så att den använder blockstorleken 64 KB (storlek på allokeringsenhet) för alla datafiler som placeras på en annan enhet än den tillfälliga enheten (som har standardinställningen `D:\` 4 KB). SQL Server virtuella datorer som distribueras via Azure Marketplace med datadiskar formaterade med en blockstorlek och överlagring för lagringspoolen inställda på 64 kB. 

Om du vill jämföra checklistan för lagring med de andra kan du se den omfattande [checklistan med metodtips för prestanda.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Översikt

För att hitta den mest effektiva konfigurationen SQL Server arbetsbelastningar på en virtuell Azure-dator börjar du med att mäta lagringsprestanda [för ditt affärsprogram.](performance-guidelines-best-practices-collect-baseline.md#storage) När lagringskraven är kända väljer du en virtuell dator som stöder nödvändig IOPS och dataflöde med rätt förhållande mellan minne och virtuell kärnor. 

Välj en VM-storlek med tillräcklig lagringsskalbarhet för din arbetsbelastning och en blandning av diskar (vanligtvis i en lagringspool) som uppfyller företagets kapacitets- och prestandakrav. 

Typen av disk beror på både filtypen som finns på disken och dina högsta prestandakrav.

> [!TIP]
> Etablering av en SQL Server VM via Azure Portal hjälper dig att gå igenom lagringskonfigurationsprocessen och implementerar de flesta metodtips för lagring, till exempel att skapa separata lagringspooler för dina data och loggfiler, rikta tempdb till enheten och aktivera `D:\` optimal cachelagringsprincip. Mer information om hur du etablerar och konfigurerar lagring finns i [SQL VM-lagringskonfiguration.](storage-configuration.md) 

## <a name="vm-disk-types"></a>VM-disktyper

Du kan välja prestandanivå för dina diskar. De typer av hanterade diskar som är tillgängliga som underliggande lagring (visas genom att öka prestandafunktionerna) är standardhårddiskar (HDD), standard SSD,Premium SOLID State-hårddiskar (SSD) och ultradiskar. 

Diskens prestanda ökar med kapaciteten, grupperad efter [premiumdisketiketter](../../../virtual-machines/disks-types.md#premium-ssd) som P1 med 4 GiB utrymme och 120 IOPS till P80 med 32 TiB lagringsutrymme och 20 000 IOPS. Premium Storage stöder en lagringscache som hjälper till att förbättra läs- och skrivprestanda för vissa arbetsbelastningar. Mer information finns i [Översikt över hanterade diskar.](../../../virtual-machines/managed-disks-overview.md) 

Det finns också tre huvudsakliga [disktyper](../../../virtual-machines/managed-disks-overview.md#disk-roles) att tänka på SQL Server virtuella Azure-datorer – en OS-disk, en tillfällig disk och dina datadiskar. Välj noggrant vad som lagras på operativsystemenheten `(C:\)` och den tillfälliga tillfälliga enheten `(D:\)` . 

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell hårddisk som kan startas och monteras som en version av ett operativsystem som körs och som är märkt som `C:\` enheten. När du skapar en virtuell Azure-dator ansluter plattformen minst en disk till den virtuella datorn för operativsystemdisken. Enheten `C:\` är standardplatsen för programinstallationer och filkonfiguration. 

För produktionsmiljöer SQL Server bör du inte använda operativsystemdisken för datafiler, loggfiler eller felloggar. 

### <a name="temporary-disk"></a>Tillfällig disk

Många virtuella Azure-datorer innehåller en annan disktyp som kallas den tillfälliga disken (märkt som `D:\` enheten). Beroende på den virtuella datorserien och storleken varierar diskens kapacitet. Den tillfälliga disken är tillfällig, vilket innebär att disklagringen återskapas (som i, den frisätts och allokeras igen), när den [](/troubleshoot/azure/virtual-machines/understand-vm-reboot)virtuella datorn startas om eller flyttas till en annan värd (till exempel för tjänstläsning). 

Den tillfälliga lagringsenheten bevaras inte i fjärrlagring och bör därför inte lagra användardatabasfiler, transaktionsloggfiler eller något annat som måste bevaras. 

Placera tempdb på den lokala tillfälliga `D:\` SSD-enheten för SQL Server arbetsbelastningar om inte förbrukning av lokal cache är ett problem. Om du använder en virtuell dator som inte har en tillfällig [disk](../../../virtual-machines/azure-vms-no-temp-disk.md) rekommenderar vi att du placerar tempdb på en egen isolerad disk eller lagringspool med cachelagring inställd på skrivskyddat. Mer information finns i [tempdb-principer för cachelagring av data.](performance-guidelines-best-practices-storage.md#data-file-caching-policies)

### <a name="data-disks"></a>Datadiskar

Datadiskar är fjärrlagringsdiskar som [](/windows-server/storage/storage-spaces/overview) ofta skapas i lagringspooler för att överskrida den kapacitet och prestanda som en enskild disk kan erbjuda den virtuella datorn.

Koppla det minsta antalet diskar som uppfyller IOPS-, dataflödes- och kapacitetskraven för din arbetsbelastning. Överskrid inte det maximala antalet datadiskar för den minsta virtuella dator som du planerar att ändra storlek till.

Placera data och loggfiler på datadiskar som etablerats så att de bäst passar prestandakraven. 

Formatera datadisken så att allokeringsenhetens storlek på 64 KB används för alla datafiler som placeras på en annan enhet än den temporära enheten (som har standardinställningen `D:\` 4 KB). SQL Server virtuella datorer som distribueras via Azure Marketplace med datadiskar formaterade med allokeringsenhetsstorlek och överlagring för lagringspoolen inställda på 64 kB. 

## <a name="premium-disks"></a>Premiumdiskar

Använd Premium SSD-diskar för data och loggfiler för SQL Server arbetsbelastningar. Premium SSD IOPS och bandbredd varierar beroende på [diskstorleken och typen](../../../virtual-machines/disks-types.md). 

För produktionsarbetsbelastningar använder du P30- och/eller P40-diskarna för SQL Server-datafiler för att säkerställa stöd för cachelagring och använda P30 upp till P80 för SQL Server-transaktionsloggfiler.  För den bästa totala ägandekostnaden börjar du med P30s (5 000 IOPS/200 Mbit/s) för data- och loggfiler och väljer bara högre kapaciteter när du behöver styra antalet virtuella diskar.

För OLTP-arbetsbelastningar matchar du mål-IOPS per disk (eller lagringspool) med dina prestandakrav med hjälp av arbetsbelastningar vid hög belastning och `Disk Reads/sec`  +  `Disk Writes/sec` prestandaräknare. För arbetsbelastningar för informationslager och rapportering matchar du måldataflödet med hjälp av arbetsbelastningar vid hög belastning och `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Använd Lagringsutrymmen för att uppnå optimala prestanda, konfigurera två pooler, en för loggfilerna och den andra för datafilerna. Om du inte använder diskstrimning använder du två Premium SSD-diskar som är mappade till separata enheter, där den ena enheten innehåller loggfilen och den andra innehåller data.

Etablerat [IOPS och dataflöde per](../../../virtual-machines/disks-types.md#premium-ssd) disk som används som en del av lagringspoolen. De kombinerade IOPS- och dataflödesfunktionerna på diskarna är den maximala kapaciteten upp till dataflödesgränserna för den virtuella datorn.

Det bästa sättet är att använda så få diskar som möjligt samtidigt som minimikraven för IOPS (och dataflöde) och kapacitet uppfyllas. Balansen mellan pris och prestanda brukar dock vara bättre med ett stort antal små diskar i stället för ett litet antal stora diskar.

### <a name="scaling-premium-disks"></a>Skala premiumdiskar

När en Azure Managed Disk först distribueras baseras prestandanivån för den disken på den etablerade diskstorleken. Ange prestandanivån vid distributionen eller ändra den efteråt, utan att ändra diskens storlek. Om efterfrågan ökar kan du öka prestandanivån för att uppfylla dina affärsbehov. 

Genom att ändra prestandanivån kan administratörer förbereda sig för och uppfylla högre krav utan att förlita sig på [disk bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Använd högre prestanda så länge det behövs där faktureringen är utformad för att uppfylla lagringsprestandanivån. Uppgradera nivån så att den matchar prestandakraven utan att öka kapaciteten. Gå tillbaka till den ursprungliga nivån när den extra prestandan inte längre krävs.

Den här kostnadseffektiva och tillfälliga utökningen av prestanda är ett starkt användningsfall för riktade händelser som shopping, prestandatestning, träningshändelser och andra korta fönster där bättre prestanda endast behövs på kort sikt. 

Mer information finns i [Prestandanivåer för hanterade diskar.](../../../virtual-machines/disks-change-performance.md) 

## <a name="azure-ultra-disk"></a>Azure Ultra Disk

Om det finns ett behov av svarstider under millisekunder med kortare svarstider bör du överväga att använda [Azure Ultra Disk](../../../virtual-machines/disks-types.md#ultra-disk) för SQL Server-loggenheten, eller till och med dataenheten för program som är mycket känsliga för I/O-svarstider. 

Ultradisk kan konfigureras där kapacitet och IOPS kan skalas oberoende av varandra. Med ultradiskadministratörer kan etablera en disk med kapacitets-, IOPS- och dataflödeskrav baserat på programbehov. 

Ultradisk stöds inte i alla VM-serier och har andra begränsningar, till exempel regionstillgänglighet, redundans och stöd för Azure Backup. Mer information finns i [Använda Azure Ultra-diskar för](../../../virtual-machines/disks-enable-ultra-ssd.md) en fullständig lista över begränsningar. 

## <a name="standard-hdds-and-ssds"></a>Standard HDD och STANDARD HDD

[Standard HDD och](../../../virtual-machines/disks-types.md#standard-hdd) STANDARD HDD har varierande svarstider och bandbredd och rekommenderas endast för dev/test-arbetsbelastningar. Produktionsarbetsbelastningar bör använda Premium-SSD:er. Om du använder Standard SSD (dev/test-scenarier) rekommenderar vi att du lägger till [](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) det maximala antalet datadiskar som stöds av din VM-storlek och använder diskstrimning med Lagringsutrymmen för bästa prestanda.

## <a name="caching"></a>Cachelagring

Virtuella datorer som stöder premiumlagringscachelagring kan dra nytta av ytterligare en funktion som kallas Azure BlobCache eller värdcachelagring för att utöka IOPS- och dataflödesfunktionerna i en virtuell dator. Virtuella datorer som är aktiverade för både Premium Storage- och Premium Storage-cachelagring har dessa två olika begränsningar för lagringsbandbredd som kan användas tillsammans för att förbättra lagringsprestanda.

IOPS- och MBps-dataflödet utan cachelagring räknas mot en virtuell dators dataflödesgränser för okorkopplad disk. De maximala cachelagrade gränserna ger ytterligare en buffert för läsningar som hjälper till att hantera tillväxt och oväntade toppar.

Aktivera Premium-cachelagring när alternativet stöds för att avsevärt förbättra prestanda för läsningar mot dataenheten utan extra kostnad. 

Läsningar och skrivningar till Azure BlobCache (cachelagrad IOPS och dataflöde) räknas inte mot den virtuella datorns gränser för ej cachelagrad IOPS och dataflöde.

> [!NOTE]
> Diskcachelagring stöds inte för diskar 4 TiB och större (P50 och större). Om flera diskar är anslutna till den virtuella datorn stöder varje disk som är mindre än 4 TiB cachelagring. Mer information finns i [Diskcachelagring.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 

### <a name="uncached-throughput"></a>Okopplad dataflöde

Det maximala antalet IOPS och dataflöde för disk som inte är frånkopplad är den maximala gränsen för fjärrlagring som den virtuella datorn kan hantera. Den här gränsen definieras på den virtuella datorn och är inte en gräns för den underliggande disklagringen. Den här gränsen gäller endast för I/O mot dataenheter som är fjärranslutna till den virtuella datorn, inte den lokala I/O:n mot den tillfälliga enheten `D:\` (enheten) eller OS-enheten.

Mängden frikopplad IOPS och dataflöde som är tillgängligt för en virtuell dator kan verifieras i dokumentationen för den virtuella datorn.

M-seriens dokumentation visar till exempel att det maximala icke-frånkopplade dataflödet för den virtuella [Standard_M8ms-datorn](../../../virtual-machines/m-series.md) är 5 000 IOPS och 125 Mbps för icke-frånkopplad disk. 

![Skärmbild som visar dokumentation om icke-frånkopplad diskgenomflöde i M-serien.](./media/performance-guidelines-best-practices/m-series-table.png)

På samma sätt kan du se att Standard_M32ts stöder 20 000 IOPS-diskar och 500 MBps utan diskgenomflöde. Den här gränsen styrs på nivån för virtuella datorer oavsett den underliggande premiumdisklagringen.

Mer information finns i [begränsningar som inte är frånkopplade och cachelagrade.](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)


### <a name="cached-and-temp-storage-throughput"></a>Cachelagrat och tillfälligt dataflöde för lagring

Dataflödesgränsen för maximalt cachelagrat och temporärt lagringsutrymme är en separat gräns från gränsen för okopplade dataflöden på den virtuella datorn. Azure BlobCache består av en kombination av den virtuella datorvärdens slumpmässiga åtkomstminne och lokalt anslutna SSD. Den temporära `D:\` enheten ( enheten) på den virtuella datorn finns också på denna lokala SSD.

Maxgränsen för cachelagrat och temporärt lagringsflöde styr I/O mot den lokala temporära enheten ( enhet) och Azure BlobCache endast om `D:\` värdcachelagring är aktiverat.  

När cachelagring har aktiverats för Premium Storage kan virtuella datorer skalas utanför begränsningarna för IOPS och dataflödesgränser för virtuella datorer som inte är frånkopplade för lagring.  

Endast vissa virtuella datorer stöder både premiumlagring och premiumlagringscachelagring (som måste verifieras i dokumentationen för virtuella datorer). M-seriens [dokumentation visar till exempel](../../../virtual-machines/m-series.md) att både Premium Storage och Cachelagring för Premium-lagring stöds: 

![Skärmbild som visar stöd Premium Storage M-serien.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Gränserna för cacheminnet varierar beroende på storleken på den virtuella datorn. Till exempel stöder den virtuella Standard_M8ms 1 0000 cachelagrade disk-IOPS och 1 000 MBps cachelagrat diskgenomflöde med en total cachestorlek på 793 GiB. På samma sätt stöder den virtuella Standard_M32ts-datorn 4 0000 cachelagrade disk-IOPS och 400 MBps cachelagrat diskgenomflöde med en total cachestorlek på 3174 GiB. 

![Skärmbild som visar dokumentation om cachelagrat diskgenomflöde i M-serien.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Du kan aktivera värdcachelagring manuellt på en befintlig virtuell dator. Stoppa alla programarbetsbelastningar och SQL Server tjänster innan några ändringar görs i den virtuella datorns cachelagringsprincip. Om du ändrar någon av inställningarna för den virtuella datorns cacheminne så kopplas måldisken från och återansluts när inställningarna har tillämpats.

### <a name="data-file-caching-policies"></a>Cachelagringsprinciper för datafiler

Cachelagringsprincipen för lagring varierar beroende på vilken typ SQL Server som finns på enheten. 

Följande tabell innehåller en sammanfattning av de rekommenderade cachelagringsprinciperna baserat på typen av SQL Server data: 

|SQL Server disk |Rekommendation |
|---------|---------|
| **Datadisk** | Aktivera `Read-only` cachelagring för diskar som är värdar SQL Server datafiler. <br/> Läsningar från cachen blir snabbare än de frånkopplade läsningarna från datadisken. <br/> Frikopplad IOPS och dataflöde plus cachelagrad IOPS och dataflöde ger den totala möjliga prestanda som är tillgänglig från den virtuella datorn inom de virtuella datorernas gränser, men den faktiska prestandan varierar beroende på arbetsbelastningens möjlighet att använda cachen (cache-träffförhållande). <br/>|
|**Transaktionsloggdisk**|Ange cachelagringsprincipen till för `None` diskar som är värdar för transaktionsloggen.  Det finns ingen prestandaförmån för att aktivera cachelagring för transaktionsloggdisken, och faktum är att antingen eller cachelagring är aktiverat på loggenheten kan försämra skrivprestanda för skrivningar mot enheten och minska mängden tillgängligt cacheminne för läsningar på `Read-only` `Read/Write` dataenheten.  |
|**Operativsystemdisk** | Standardprincipen för cachelagring kan `Read-only` vara `Read/write` eller för OS-enheten. <br/> Vi rekommenderar inte att du ändrar cachelagringsnivån för OS-enheten.  |
| **tempdb**| Om tempdb inte kan placeras på den tillfälliga enheten på grund av kapacitetsskäl kan du antingen ändra storlek på den virtuella datorn för att få en större tillfällig enhet eller placera tempdb på en separat dataenhet med `D:\` `Read-only` konfigurerad cachelagring. <br/> Både den virtuella datorns cacheminne och tillfälliga enhet använder den lokala SSD:n, så tänk på detta när storleksändring som tempdb I/O räknas mot de cachelagrade IOPS- och dataflödesgränserna för virtuella datorer när de finns på den tillfälliga enheten.| 
| | | 


Mer information finns i [Diskcachelagring.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Diskstrimning

Analysera dataflödet och bandbredden som krävs för dina SQL-datafiler för att fastställa antalet datadiskar, inklusive loggfilen och tempdb. Dataflödes- och bandbreddsbegränsningar varierar beroende på VM-storlek. Mer information finns i [VM-storlek](../../../virtual-machines/sizes.md)

Lägg till ytterligare datadiskar och använd diskstrimning för mer dataflöde. Ett program som behöver 12 000 IOPS och 180 MB/s dataflöde kan till exempel använda tre stripe P30-diskar för att leverera 15 000 IOPS- och 600 MB/s-dataflöde. 

Information om hur du konfigurerar diskstrimning finns [i diskstrimning.](storage-configuration.md#disk-striping) 

## <a name="disk-capping"></a>Diskbegränsning 

Det finns dataflödesgränser på både disk- och vm-nivå. De maximala IOPS-gränserna per virtuell dator och per disk skiljer sig åt och är oberoende av varandra.

Program som förbrukar resurser utöver dessa gränser kommer att begränsas (även kallat begränsade). Välj en virtuell dator och diskstorlek i en diskstrimm som uppfyller programkraven och inte kommer att uppfylla begränsningar. För att åtgärda begränsningen använder du cachelagring eller finjusterar programmet så att mindre dataflöde krävs.

Ett program som behöver 12 000 IOPS och 180 MB/s kan till exempel: 
- Använd den [Standard_M32ms](../../../virtual-machines/m-series.md) som har ett maximalt dataflöde för obekopplad disk på 20 000 IOPS och 500 MBps.
- Stripe tre P30-diskar för att leverera 15 000 IOPS och 600 MB/s dataflöde.
- Använd en [Standard_M16ms](../../../virtual-machines/m-series.md) dator och använd värdcachelagring för att använda lokal cache över förbrukande dataflöde. 

Virtuella datorer som konfigurerats för att skala upp under tider med hög användning bör etablera lagring med tillräckligt med IOPS och dataflöde för att stödja den maximala VM-storleken samtidigt som det totala antalet diskar är mindre än eller lika med det maximala antal som stöds av den minsta VM SKU som ska användas.

Mer information om begränsningar för diskbegränsningar och hur du använder cachelagring för att undvika begränsningar finns i [Disk-I/O-begränsning.](../../../virtual-machines/disks-performance.md)

> [!NOTE] 
> Vissa diskbegränsningar kan fortfarande resultera i tillfredsställande prestanda för användarna. finjustera och underhålla arbetsbelastningar i stället för att ändra storlek till en större virtuell dator för att balansera hanteringen av kostnader och prestanda för verksamheten. 


## <a name="write-acceleration"></a>Skrivningsacceleration

Skrivningsacceleration är en diskfunktion som endast är tillgänglig för [M-seriens](https://docs.microsoft.com/azure/virtual-machines/m-series) Virtual Machines (VM). Syftet med skrivningsacceleration är att förbättra I/O-svarstiden för skrivningar mot Azure Premium Storage när du behöver ensiffrig I/O-svarstid på grund av verksamhetskritiska OLTP-arbetsbelastningar eller informationslagermiljöer. 

Använd skrivningsacceleration för att förbättra skrivfördröjningen till den enhet som är värd för loggfilerna. Använd inte skrivacceleration för SQL Server datafiler. 

Skrivningsaccelerator diskar delar samma IOPS-gräns som den virtuella datorn. Anslutna diskar får inte överskrida den Skrivningsaccelerator IOPS-gränsen för en virtuell dator.  

I följande tabell beskrivs antalet datadiskar och IOPS som stöds per virtuell dator: 

| VM-SKU  | Antal Skrivningsaccelerator diskar  | Skrivningsaccelerator disk-IOPS per virtuell dator  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Det finns ett antal begränsningar för att använda skrivningsacceleration. Mer information finns i Begränsningar [när du använder Skrivningsaccelerator](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Jämföra med Azure Ultra Disk

Den största skillnaden mellan skrivningsacceleration och Azure Ultra-diskar är att skrivacceleration är en funktion för virtuella datorer som endast är tillgänglig för M-serien och Azure Ultra-diskar är ett lagringsalternativ. Skrivningsacceleration är en skrivoptimerad cache med egna begränsningar baserat på storleken på den virtuella datorn. Azures ultradiskar är ett alternativ för disklagring med kort svarstid för Azure Virtual Machines. 

Om möjligt använder du skrivningsacceleration över ultradiskar för transaktionsloggdisken. För virtuella datorer som inte stöder skrivningsacceleration men som kräver kort svarstid till transaktionsloggen använder du Azure Ultra-diskar. 

## <a name="monitor-storage-performance"></a>Övervaka lagringsprestanda

För att utvärdera lagringsbehov och avgöra hur bra lagringen fungerar måste du förstå vad som ska mätas och vad dessa indikatorer innebär. 

[IOPS (indata/utdata per sekund)](../../../virtual-machines/premium-storage-performance.md#iops) är antalet begäranden som programmet gör till lagring per sekund. Mät IOPS med prestandaövervakarräknare `Disk Reads/sec` och `Disk Writes/sec` . [OLTP-program (onlinetransaktionsbearbetning)](/azure/architecture/data-guide/relational-data/online-transaction-processing) måste köra högre IOPS för att uppnå optimala prestanda. Program som betalningsbearbetningssystem, onlineshopping och butiksförsäljningssystem är alla exempel på OLTP-program.

[Dataflöde](../../../virtual-machines/premium-storage-performance.md#throughput) är den datavolym som skickas till den underliggande lagringen, som ofta mäts per megabyte per sekund. Mät dataflöde med prestandaövervakarräknarna `Disk Read Bytes/sec` och `Disk Write Bytes/sec` . [Informationslager är optimerat](/azure/architecture/data-guide/relational-data/data-warehousing) för att maximera dataflödet över IOPS. Program som datalager för analys, rapportering, ETL-arbetsströmmar och andra business intelligence mål är exempel på datalagerprogram.

I/O-enhetsstorlekar påverkar IOPS- och dataflödesfunktioner eftersom mindre I/O-storlekar ger högre IOPS och större I/O-storlekar ger högre dataflöde. SQL Server automatiskt den optimala I/O-storleken. Mer information om finns i [Optimera IOPS, dataflöde och svarstid för dina program.](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance) 

Det finns Azure Monitor mått som är ovärderliga för att identifiera ett tak på den virtuella datorns och diskens nivå, samt förbrukningen och hälsotillståndet för AzureBlob-cachen. Information om hur du identifierar viktiga räknare som du kan lägga till i din övervakningslösning Azure Portal instrumentpanelen finns i [Mått för lagringsanvändning.](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics) 

> [!NOTE]
> Azure Monitor erbjuder för närvarande inte mått på disknivå för den tillfälliga temp-enheten `(D:\)` . IOPS-förbrukad procentandel för vm-cache och förbrukad bandbredd i procent för virtuella datorer återspeglar IOPS och dataflöde från både den tillfälliga temp-enheten och `(D:\)` värdcachelagringen tillsammans.


## <a name="next-steps"></a>Nästa steg

Mer information om metodtips för prestanda finns i de andra artiklarna i den här serien:
- [Snabb checklista](performance-guidelines-best-practices-checklist.md)
- [VM-storlek](performance-guidelines-best-practices-vm-size.md)
- [Samla in baslinje](performance-guidelines-best-practices-collect-baseline.md)

Metodtips för säkerhet finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

Detaljerad testning av prestanda SQL Server virtuella Azure-datorer med TPC-E och TPC_C-prestandatest finns i bloggen Optimize OLTP performance (Optimera [OLTP-prestanda).](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)

Läs andra SQL Server Virtual Machine på [sidan SQL Server Översikt Virtual Machines Azure Virtual Machines Azure.](sql-server-on-azure-vm-iaas-what-is-overview.md) Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).
