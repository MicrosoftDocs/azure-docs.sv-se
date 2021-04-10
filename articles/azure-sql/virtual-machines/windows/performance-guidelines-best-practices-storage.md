---
title: 'Lagring: metod tips för prestanda & rikt linjer'
description: Tillhandahåller metod tips och rikt linjer för lagring för att optimera prestanda för dina SQL Server på Azure Virtual Machine (VM).
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
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572517"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Lagring: metod tips för prestanda för SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln innehåller metod tips och rikt linjer för lagring som optimerar prestanda för dina SQL Server på Azure Virtual Machines (VM).

Det finns vanligt vis en kompromiss mellan optimering av kostnader och optimering för prestanda. Den här prestanda serien för bästa metoder fokuserar på att få *bästa möjliga* prestanda för SQL Server på Azure Virtual Machines. Om din arbets belastning är mindre krävande kanske du inte behöver varje rekommenderad optimering. Överväg dina prestanda behov, kostnader och arbets belastnings mönster när du utvärderar dessa rekommendationer.

Mer information finns i de andra artiklarna i serien: [prestanda check lista](performance-guidelines-best-practices-checklist.md), [VM-storlek](performance-guidelines-best-practices-vm-size.md)och [insamling av bas linje](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Checklista

Läs igenom följande check lista för en kort översikt över metod tips för lagring som resten av artikeln omfattar i större detalj: 

- Övervaka programmet och [fastställa krav på lagrings bandbredd och latens](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) för SQL Server data, logg och tempdb-filer innan du väljer disk typen. 
- Du kan optimera lagrings prestanda genom att planera för högsta otillgängliga IOPS och använda Datacachen som en prestanda funktion för data läsningar samtidigt som du undviker [virtuella datorer och diskar capping](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placera data, logg och tempdb-filer på separata enheter.
    - För data enheten använder du endast [Premium-P30 och P40-diskar](../../../virtual-machines/disks-types.md#premium-ssd) för att säkerställa tillgängligheten för cache-stöd
    - För logg enhets planen för kapacitets-och test prestanda jämfört med kostnaderna vid utvärdering av [Premium P30-P80-diskar](../../../virtual-machines/disks-types.md#premium-ssd)
      - Om det krävs en lagrings fördröjning i millisekunder, använder du [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) för transaktions loggen. 
      - För distribution av virtuella datorer i M-serien kan du [skriva Accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) för att använda Azure Ultra disks.
    - Placera [tempdb](/sql/relational-databases/databases/tempdb-database) på den lokala tillfälliga SSD- `D:\` enheten för de flesta SQL Server arbets belastningar när du har valt den optimala storleken för virtuella datorer. 
      - Om den lokala enhetens kapacitet inte räcker för tempdb bör du överväga att ändra storlek på den virtuella datorn. Mer information finns i [principer för cachelagring av data filer](#data-file-caching-policies) .
- Stripa flera Azure-datadiskar med [lagrings utrymmen](/windows-server/storage/storage-spaces/overview) för att öka i/O-bandbredden upp till den virtuella mål DATORns IOPS-och data flödes gränser.
- Ange att [cachelagring av värden](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) ska vara skrivskyddat för data fil diskar.
- Ange [cachelagring av värden](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) till ingen för logg fil diskar.
    - Aktivera inte cachelagring för läsning/skrivning på diskar som innehåller SQL Server filer. 
    - Stoppa alltid SQL Server tjänsten innan du ändrar cache-inställningarna för disken.
- För utvecklings-och test arbets belastningar och arkivering med långsiktig säkerhets kopiering bör du använda standard lagring. Vi rekommenderar inte att du använder Standard HDD/SDD för produktions arbets belastningar.
- [Kreditbaserade disk burst](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) bör endast beaktas för mindre arbets belastningar för utveckling/testning och avdelnings system.
- Etablera lagrings kontot i samma region som SQL Server VM. 
- Inaktivera Azure Geo-redundant lagring (geo-replikering) och Använd LRS (lokalt redundant lagring) på lagrings kontot.
- Formatera datadisken så att den använder storleken 64 KB för allokeringsenheter för alla datafiler som placerats på en annan enhet än den temporära `D:\` enheten (som har ett standardvärde på 4 KB). SQL Server virtuella datorer som distribueras via Azure Marketplace levereras med data diskar som är formaterade med storlek och överlagring för allokeringsenhet och överlagring för lagringspoolen inställd på 64 KB. 

Information om hur du jämför lagrings check listan med de andra finns i [Check lista för omfattande prestanda för bästa praxis](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Översikt

Om du vill hitta den mest effektiva konfigurationen för SQL Server arbets belastningar på en virtuell Azure-dator börjar du med att [mäta lagrings prestanda för affärs programmet](performance-guidelines-best-practices-collect-baseline.md#storage). När lagrings kraven är kända väljer du en virtuell dator som stöder nödvändiga IOPS och data flöde med lämpligt minne-till-vCore-förhållande. 

Välj en VM-storlek med tillräckligt skalbart lagrings utrymme för din arbets belastning och en blandning av diskar (vanligt vis i en lagringspool) som uppfyller företagets kapacitet och prestanda krav. 

Vilken typ av disk som är beror på både den filtyp som finns på disken och kraven för högsta prestanda.

> [!TIP]
> Genom att tillhandahålla en SQL Server VM via Azure Portal får du hjälp med lagrings konfigurations processen och implementerar de flesta bästa metoder för lagring, till exempel att skapa separata lagringspooler för dina data och loggfiler, rikta in tempdb till `D:\` enheten och aktivera den optimala auktoriseringsprincipen för cachelagring. Mer information om etablering och konfiguration av lagring finns i [SQL VM Storage Configuration](storage-configuration.md). 

## <a name="vm-disk-types"></a>VM-disktyper

Du har ett alternativ i prestanda nivån för diskarna. De typer av hanterade diskar som är tillgängliga som underliggande lagring (listade genom ökade prestanda funktioner) är standard hård diskar (HDD), standard SSD, förstklassiga solid state-hårddiskar (SSD) och Ultra disks. 

Diskens prestanda ökar med kapaciteten, grupperad efter [Premium disk etiketter](../../../virtual-machines/disks-types.md#premium-ssd) som P1 med 4 GiB utrymme och 120 IOPS till P80 med 32 TIB storage och 20 000 IOPS. Premium Storage stöder lagrings cachen som hjälper till att förbättra Läs-och skriv prestanda för vissa arbets belastningar. Mer information finns i [Översikt över Managed disks](../../../virtual-machines/managed-disks-overview.md). 

Det finns också tre typer av huvud [diskar](../../../virtual-machines/managed-disks-overview.md#disk-roles) som du bör tänka på för din SQL Server på Azure VM – en OS-disk, en temporär disk och dina data diskar. Välj noga vad som lagras på operativ system enheten `(C:\)` och den tillfälliga tillfälliga enheten `(D:\)` . 

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativ system disk är en virtuell hård disk som kan startas och monteras som en aktiv version av ett operativ system och som är märkt som `C:\` enhet. När du skapar en virtuell Azure-dator kommer plattformen att ansluta minst en disk till den virtuella datorn för operativ system disken. `C:\`Enheten är standard platsen för program installationer och fil konfiguration. 

För produktions SQL Server miljöer ska du inte använda operativ system disken för datafiler, loggfiler, fel loggar. 

### <a name="temporary-disk"></a>Tillfällig disk

Många virtuella Azure-datorer innehåller en annan disk typ som kallas temporär disk (märkt som `D:\` enhet). Beroende på den virtuella dator serien och storleken på diskens kapacitet varierar. Den temporära disken är tillfällig, vilket innebär att disk lagringen återskapas (som i, den frigörs och tilldelas igen) när den virtuella datorn startas om, eller flyttas till en annan värd (för [tjänst återställning](/troubleshoot/azure/virtual-machines/understand-vm-reboot), till exempel). 

Den tillfälliga lagrings enheten är inte beständig för Fjärrlagring och bör därför inte lagra databasfiler för användare, transaktionsloggfiler eller något som måste bevaras. 

Placera tempdb på den lokala tillfälliga SSD- `D:\` enheten för SQL Server arbets belastningar om inte konsumtion av lokal cache är ett problem. Om du använder en virtuell dator som [inte har en temporär disk](../../../virtual-machines/azure-vms-no-temp-disk.md) , rekommenderar vi att du placerar tempdb på sin egen isolerade disk eller lagringspool med cachelagring inställd på skrivskyddad. Läs mer i tempdb- [data caching-principer](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Datadiskar

Data diskar är fjärrlagringsenheter som ofta skapas i [lagringspooler](/windows-server/storage/storage-spaces/overview) för att överskrida kapaciteten och prestandan som en enskild disk kan erbjuda till den virtuella datorn.

Koppla det minsta antalet diskar som uppfyller de IOPS-, data flödes-och kapacitets krav som gäller för din arbets belastning. Överskrider inte det maximala antalet data diskar på den minsta virtuella dator som du planerar att ändra storlek till.

Placera data och loggfiler på data diskar etablerade för bästa prestanda krav. 

Formatera datadisken så att den använder storleken 64 KB för allokeringsenheter för alla datafiler som placerats på en annan enhet än den temporära `D:\` enheten (som har ett standardvärde på 4 KB). SQL Server virtuella datorer som distribueras via Azure Marketplace levereras med data diskar som är formaterade med storlek och överlagring för allokeringsenhet och överlagring för lagringspoolen inställd på 64 KB. 

## <a name="premium-disks"></a>Premium diskar

Använd Premium SSD-diskar för data-och loggfiler för produktions SQL Server arbets belastningar. Premium SSD IOPS och bandbredd varierar beroende på [diskens storlek och typ](../../../virtual-machines/disks-types.md). 

För produktions arbets belastningar använder du P30-och/eller P40-diskarna för SQL Server datafiler för att säkerställa cachelagring av stöd och använda P30 upp till P80 för SQL Server transaktionsloggfiler.  För bästa möjliga ägande kostnad börjar du med P30s (5000 IOPS/200 MBIT/s) för data-och loggfiler och väljer bara högre kapacitet när du behöver kontrol lera antalet diskar på den virtuella datorn.

För OLTP-arbetsbelastningar måste du matcha målets IOPS per disk (eller lagringspool) med dina prestanda krav med arbets belastningar vid hög belastnings tider och `Disk Reads/sec`  +  `Disk Writes/sec` prestanda räknarna. För data lager-och rapporterings arbets belastningar matchar du mål data flödet med arbets belastningar vid hög belastnings tider och `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Använd lagrings utrymmen för att uppnå optimala prestanda, konfigurera två pooler, en för loggfilerna och den andra för datafilerna. Om du inte använder disk ränder använder du två Premium SSD-diskar som är mappade till separata enheter, där en enhet innehåller logg filen och den andra innehåller data.

Den [tillhandahållna IOPS och data flödet](../../../virtual-machines/disks-types.md#premium-ssd) per disk som används som en del av lagringspoolen. De kombinerade IOPS-och data flödes funktionerna i diskarna är den maximala kapaciteten upp till data flödes gränserna för den virtuella datorn.

Den bästa metoden är att använda det minsta antalet diskar som kan användas för att uppfylla de lägsta kraven för IOPS (och data flöde) och kapacitet. Dock är balansen mellan pris och prestanda bättre att vara bättre med ett stort antal små diskar i stället för ett litet antal stora diskar.

### <a name="scaling-premium-disks"></a>Skala Premium-diskar

När en Azure-hanterad disk först distribueras, baseras prestanda nivån för den disken på den allokerade disk storleken. Ange prestanda nivån vid distributionen eller ändra den efteråt, utan att ändra storleken på disken. Om efter frågan ökar kan du öka prestanda nivån så att den passar dina affärs behov. 

Genom att ändra prestanda nivån kan administratörer förbereda för och uppfylla högre efter frågan utan att behöva förlita sig på [disk-burst](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Använd den högre prestandan så länge som det behövs, där faktureringen är utformad för att uppfylla lagrings prestanda nivån. Uppgradera nivån så att den matchar prestanda kraven utan att öka kapaciteten. Återgå till den ursprungliga nivån när extra prestanda inte längre krävs.

Den här kostnads effektiva och tillfälliga utökningen av prestanda är ett starkt användnings fall för riktade händelser som shopping, prestandatester, utbildnings händelser och andra korta fönster där större prestanda bara behövs för en kort period. 

Mer information finns i [prestanda nivåer för hanterade diskar](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra disk

Om det finns ett behov av svars tider under millisekunder med minskad latens bör du använda [Azure Ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) för den SQL Server logg enheten, eller till och med data enheten för program som är mycket känsliga för i/O-latens. 

Ultra disk kan konfigureras där kapacitet och IOPS kan skalas oberoende av varandra. Med Ultra disk-administratörer kan du etablera en disk med kapacitets-, IOPS-och data flödes krav baserat på program behov. 

Ultra disk stöds inte på alla VM-serier och har andra begränsningar som region tillgänglighet, redundans och stöd för Azure Backup. Mer information finns i [använda Azure Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md) för en fullständig lista över begränsningar. 

## <a name="standard-hdds-and-ssds"></a>Standard hård diskar och SSD

[Standard hård diskar](../../../virtual-machines/disks-types.md#standard-hdd) och SSD har varierande latens och bandbredd och rekommenderas bara för arbets belastningar för utveckling/testning. Arbets belastningar för produktion bör använda Premium-SSD. Om du använder Standard SSD (utvecklings-/test scenarier) är rekommendationen att lägga till det maximala antalet data diskar som stöds av storleken på den [virtuella](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) datorn och använda disk ränder med lagrings utrymmen för bästa prestanda.

## <a name="caching"></a>Cachelagring

Virtuella datorer som stöder cachelagring av Premium Storage kan dra nytta av ytterligare en funktion som kallas Azure-BlobCache eller cachelagring av värdar för att utöka IOPS-och data flödes funktionerna i en virtuell dator. Virtuella datorer som är aktiverade för både Premium Storage-och Premium Storage-cachelagring har dessa två olika begränsningar för lagrings bandbredd som kan användas tillsammans för att förbättra lagrings prestanda.

IOPS-och Mbit/s-dataflödet utan cachelagring räknas mot en virtuell dators gränser för data flöde i cacheminnet. De maximala cachelagrade gränserna ger ytterligare en buffert för läsningar som hjälper till att adressera tillväxten och oväntade toppar.

Aktivera Premium-cachelagring när alternativet stöds för att förbättra prestanda för läsningar mot data enheten utan ytterligare kostnad. 

Läsningar och skrivningar till Azure-BlobCache (cachelagrad IOPS och data flöde) räknas inte mot de cachelagrade IOPS-och data flödes gränserna för den virtuella datorn.

> [!NOTE]
> Diskcachelagring stöds inte för disk 4 TiB och större (P50 och större). Om flera diskar är anslutna till den virtuella datorn kommer varje disk som är mindre än 4 TiB att ha stöd för cachelagring. Mer information finns i diskcachelagring [.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 

### <a name="uncached-throughput"></a>Ej cachelagrat data flöde

Max gränsen för ej cachelagrade diskar och data flöden är den maximala begränsningen för Fjärrlagring som den virtuella datorn kan hantera. Den här gränsen definieras på den virtuella datorn och är inte en gräns för den underliggande disk lagringen. Den här gränsen gäller endast för I/O mot data enheter som fjärransluter till den virtuella datorn, inte från det lokala I/O mot den temporära enheten ( `D:\` enhet) eller operativ system enheten.

Mängden ej cachelagrad IOPS och data flöde som är tillgängligt för en virtuell dator kan verifieras i dokumentationen för den virtuella datorn.

Dokumentationen för [M-serien](../../../virtual-machines/m-series.md) visar till exempel att max det cachelagrade data flödet för den Standard_M8ms virtuella datorn är 5000 IOPS och 125 Mbit/s för disk data flöde som inte har cachelagrats. 

![Skärm bild som visar d-seriens cachelagrade disk data flödes dokumentation.](./media/performance-guidelines-best-practices/m-series-table.png)

På samma sätt kan du se att Standard_M32ts stöder 20 000 disk-och 500 Mbit/s, ej cachelagrad disk data flöde. Den här gränsen styrs av den virtuella dator nivån oavsett den underliggande Premium disk lagringen.

Mer information finns i [cachelagrade och cachelagrade gränser](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Data flöde för cachelagrad och temporär lagring

Den maximala data flödes gränsen för cachelagrad och temporär lagring är en separat gräns från gränsen för ej cachelagrade data flöden på den virtuella datorn. Azure-BlobCache består av en kombination av den virtuella datorns värds RAM-minne och lokalt ansluten SSD. Den temporära enheten ( `D:\` enheten) i den virtuella datorn finns också på den här lokala SSD.

Den maximala data flödes gränsen för cachelagrade och tillfälliga lagrings enheter styr i/O mot den lokala temporära enheten ( `D:\` enhet) och Azure-BlobCache **endast om** cachelagring av värden är aktiverat. 

När cachelagring har Aktiver ATS på Premium Storage kan virtuella datorer skalas utöver begränsningarna i Fjärrlagring, ej cachelagrade VM-och data flödes gränser.  

Endast vissa virtuella datorer stöder både Premium Storage och Premium Storage-cachelagring (som måste verifieras i dokumentationen för den virtuella datorn). Dokumentationen för [M-serien](../../../virtual-machines/m-series.md) anger till exempel att både Premium Storage och Premium Storage-cachelagring stöds: 

![Skärm bild som visar Premium Storage-stöd i M-serien.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Gränserna för cachen varierar beroende på storleken på den virtuella datorn. Standard_M8ms VM stöder till exempel 10000 cachelagrad disk-IOPS och 1000 Mbit/s, med en sammanlagd cachestorlek på 793 GiB. På samma sätt har Standard_M32ts VM stöd för 40000 cachelagrad disk-IOPS och 400 Mbit/s cachelagrad disk data flöde med den totala cachestorleken på 3174 GiB. 

![Skärm bild som visar cachelagrad disk data flödes dokumentation i M-serien.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Du kan aktivera cachelagring av värdarna manuellt på en befintlig virtuell dator. Stoppa alla program arbets belastningar och SQL Server tjänsterna innan du gör några ändringar i den virtuella datorns princip för cachelagring. Om du ändrar någon av inställningarna för cachelagring av virtuella datorer i den mål disk som kopplas bort och sedan ansluts igen efter att inställningarna har tillämpats.

### <a name="data-file-caching-policies"></a>Principer för cachelagring av data filer

Din princip för cachelagring av lagring varierar beroende på vilken typ av SQL Server datafiler som finns på enheten. 

Följande tabell innehåller en sammanfattning av de rekommenderade caching-principerna baserat på typen av SQL Server data: 

|SQL Server disk |Rekommendation |
|---------|---------|
| **Datadisk** | Aktivera `Read-only` cachelagring för diskarna som är värdar för SQL Server datafiler. <br/> Läsningar från cachen går snabbare än de cachelagrade läsningarna från data disken. <br/> Uncached IOPS och data flöde plus cachelagrad IOPS och data flöde ger total möjliga prestanda som är tillgänglig från den virtuella datorn inom gränserna för virtuella datorer, men den faktiska prestandan varierar beroende på arbets Belastningens möjlighet att använda cachen (cacheträffar). <br/>|
|**Transaktions logg disk**|Ange principen för cachelagring på `None` för diskar som är värdar för transaktions loggen.  Det finns ingen prestanda förmån för att aktivera cachelagring för transaktions logg disken, och i själva `Read-only` verket `Read/Write` kan det påverka prestanda för skrivningar mot enheten och minska mängden cache som är tillgängligt för läsningar på data enheten.  |
|**Operativ system disk** | Standard principen för cachelagring kan vara `Read-only` eller `Read/write` för operativ system enheten. <br/> Vi rekommenderar inte att du ändrar lagrings nivån för operativ system enheten.  |
| **tempdb**| Om tempdb inte kan placeras på den tillfälliga enheten `D:\` på grund av kapacitets skäl kan du antingen ändra storlek på den virtuella datorn för att få en större tillfällig enhet eller placera tempdb på en separat data enhet med `Read-only` konfigurerad cachelagring. <br/> Den virtuella datorns cache och den tillfälliga enheten använder båda lokal SSD, så tänk på detta när du ändrar storlek som tempdb I/O kommer att räknas mot de cachelagrade IOPS-och data flödes gränserna för virtuella datorer som finns på den tillfälliga enheten.| 
| | | 


Mer information finns i diskcachelagring [.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Disk randning

Analysera det data flöde och den bandbredd som krävs för dina SQL-datafiler för att fastställa antalet data diskar, inklusive logg filen och tempdb. Data flödes-och bandbredds gränserna varierar beroende på VM-storlek. Läs mer i VM- [storlek](../../../virtual-machines/sizes.md)

Lägg till ytterligare data diskar och Använd disk ränder för mer data flöde. Till exempel kan ett program som behöver 12 000 IOPS och 180 MB/s-genomflöde använda tre stripe P30-diskar för att leverera 15 000 IOPS och 600 MB/s-genomflöde. 

Om du vill konfigurera disk ränder, se [disk ränder](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Disk capping 

Det finns data flödes gränser på både disk-och virtuell dator nivå. De högsta IOPS-gränserna per virtuell dator och per disk skiljer sig åt och är oberoende av varandra.

Program som förbrukar resurser utöver dessa gränser begränsas (även kallat tak). Välj en virtuell dator och disk storlek i en disk remsa som uppfyller program kraven och inte möter capping-begränsningar. För att adressera capping, använder du cachelagring eller finjusterar programmet så att mindre data flöde krävs.

Till exempel kan ett program som behöver 12 000 IOPS och 180 MB/s: 
- Använd [Standard_M32ms](../../../virtual-machines/m-series.md) som har ett maximalt Cache-lagrat disk flöde på 20 000 IOPS och 500 Mbit/s.
- Ta bort tre P30 diskar för att leverera 15 000 IOPS och 600 MB/s genom strömning.
- Använd en [Standard_M16ms](../../../virtual-machines/m-series.md) virtuell dator och Använd cachelagring av värdar för att använda lokal cache för att använda data flödet. 

Virtuella datorer som kon figurer ATS för att skala upp under en hög belastning bör tillhandahålla lagring med tillräckligt med IOPS och data flöde för att stödja den maximala storleken på virtuella datorer samtidigt som det totala antalet diskar som är mindre än eller lika med det maximala antalet diskar som stöds av den minsta VM-SKU: n som ska användas.

Mer information om disk capping begränsningar och hur du använder cachelagring för att undvika capping finns i [Disk IO-capping](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Vissa disk capping kan fortfarande resultera i tillfredsställande prestanda för användarna. finjustera och underhåll arbets belastningar i stället för att ändra storlek till en större virtuell dator för att balansera hanteringen av kostnader och prestanda för verksamheten. 


## <a name="write-acceleration"></a>Skriv acceleration

Skriv acceleration är en disk funktion som endast är tillgänglig för [M-seriens](https://docs.microsoft.com/azure/virtual-machines/m-series) Virtual Machines (VM). Syftet med att skriva acceleration är att förbättra I/O-svars tiden för skrivningar mot Azure Premium Storage när du behöver I/O-svars tid med enkel volym på grund av hög verksamhets kritiska OLTP-arbetsbelastningar eller data lager miljöer. 

Använd Skriv acceleration för att förbättra Skriv fördröjningen till den enhet som är värd för loggfilerna. Använd inte Skriv acceleration för SQL Server datafiler. 

Skrivningsaccelerator diskar delar samma IOPS-gräns som den virtuella datorn. Anslutna diskar får inte överskrida Skrivningsaccelerator IOPS-gränsen för en virtuell dator.  

I följande tabell beskrivs antalet data diskar och IOPS som stöds per virtuell dator: 

| VM-SKU  | Antal Skrivningsaccelerator diskar  | Skrivningsaccelerator disk-IOPS per virtuell dator  |
|---|---|---|
| M416ms_v2 M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2 M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Det finns ett antal begränsningar för att använda Skriv acceleration. Mer information finns i [begränsningar när du använder Skrivningsaccelerator](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Jämföra med Azure Ultra disk

Den största skillnaden mellan Skriv acceleration och Azure Ultra disks är att Skriv acceleration är en funktion för virtuella datorer som endast är tillgänglig för M-serien och Azure Ultra disks är ett lagrings alternativ. Skriv acceleration är en skrivbar cache med egna begränsningar baserat på storleken på den virtuella datorn. Azure Ultra disks är ett alternativ för disk lagring med låg latens för Azure Virtual Machines. 

Använd om möjligt Skriv acceleration över Ultra disks för transaktions logg disken. Använd Azure Ultra disks för virtuella datorer som inte stöder Skriv acceleration men kräver låg latens för transaktions loggen. 

## <a name="monitor-storage-performance"></a>Övervaka lagrings prestanda

För att bedöma lagrings behoven och bestämma hur bra lagringen presterar måste du förstå vad du ska mäta och vad dessa indikatorer innebär. 

[IOPS (indata/utdata per sekund)](../../../virtual-machines/premium-storage-performance.md#iops) är antalet begär Anden som programmet gör till lagring per sekund. Mät IOPS med prestanda övervaknings räknare `Disk Reads/sec` och `Disk Writes/sec` . [OLTP-program (Online Transaction Processing)](/azure/architecture/data-guide/relational-data/online-transaction-processing) måste köra högre IOPS för att uppnå optimala prestanda. Program som betalnings bearbetnings system, Köp online och detalj handels system är exempel på OLTP-program.

Data [flödet](../../../virtual-machines/premium-storage-performance.md#throughput) är den mängd data som skickas till den underliggande lagringen, vilket ofta mäts med megabyte per sekund. Mät data flöde med prestanda övervaknings räknare `Disk Read Bytes/sec` och `Disk Write Bytes/sec` . [Data lager](/azure/architecture/data-guide/relational-data/data-warehousing) hantering optimeras runt genom STRÖMNING av IOPS. Program som data lager för analys, rapportering, ETL-workstreams och andra Business Intelligence mål är exempel på data lager program.

I/O-enheternas storlek påverkar IOPS-och data flödes funktioner som mindre I/O-storlekar ger högre IOPS och större I/O-storlekar ger högre data flöde. SQL Server väljer den optimala I/O-storleken automatiskt. Mer information om finns i [optimera IOPS, data flöde och svars tid för dina program](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Det finns vissa Azure Monitor mått som inte är värdefulla för att identifiera capping på den virtuella datorn och disk nivån samt förbrukningen och hälsan hos AzureBlob-cachen. Om du vill identifiera nyckel räknare som ska läggas till i övervaknings lösningen och Azure Portal instrument panelen, se [mått för lagrings användning](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor erbjuder för närvarande inte mått på disk nivå för den tillfälliga temporära enheten `(D:\)` . VM cachelagrad IOPS förbrukade procent och VM cachelagrad bandbredd som förbrukas visar IOPS och data flöde från både den tillfälliga temporära enheten `(D:\)` och värd-cachelagring.


## <a name="next-steps"></a>Nästa steg

Läs mer om bästa metoder för prestanda i de andra artiklarna i den här serien:
- [Snabb check lista](performance-guidelines-best-practices-checklist.md)
- [VM-storlek](performance-guidelines-best-practices-vm-size.md)
- [Samla in bas linje](performance-guidelines-best-practices-collect-baseline.md)

Rekommenderade säkerhets metoder finns i [säkerhets överväganden för SQL Server på Azure Virtual Machines](security-considerations-best-practices.md).

För detaljerad testning av SQL Server prestanda på virtuella Azure-datorer med TPC-E och TPC_C benchmarks, se bloggen [optimera OLTP-prestanda](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Granska andra SQL Server virtuella dator artiklar på [SQL Server på Azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).
