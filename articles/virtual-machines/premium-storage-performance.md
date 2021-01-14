---
title: 'Azure Premium Storage: design för hög prestanda'
description: Skapa högpresterande program med hjälp av Azure Premium SSD-hanterade diskar. Premium Storage erbjuder disk support med hög prestanda och låg latens för I/O-intensiva arbets belastningar som körs på Azure Virtual Machines.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 26ef07269c9451c2e9d05d42e2247fbfcdae4844
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201968"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-lagring: design för hög prestanda

Den här artikeln innehåller rikt linjer för att skapa högpresterande program med Azure Premium Storage. Du kan använda instruktionerna i det här dokumentet tillsammans med bästa metoder för prestanda som gäller för tekniker som används av ditt program. För att illustrera rikt linjerna har vi använt SQL Server som körs på Premium Storage som ett exempel i det här dokumentet.

Vi hanterar prestanda scenarier för lagrings skiktet i den här artikeln, men du måste optimera program skiktet. Om du till exempel är värd för en SharePoint-grupp på Azure Premium Storage kan du använda SQL Server-exemplen i den här artikeln för att optimera databas servern. Optimera dessutom SharePoint-servergruppens webb server och program Server för att få ut mesta möjliga prestanda.

Den här artikeln hjälper dig att besvara vanliga frågor om hur du optimerar program prestanda på Azure Premium Storage

* Hur mäter du program prestanda?  
* Varför ser du inte förväntad hög prestanda?  
* Vilka faktorer påverkar programmets prestanda på Premium Storage?  
* Hur påverkar dessa faktorer prestanda för ditt program på Premium Storage?  
* Hur kan du optimera för IOPS, bandbredd och latens?  

Vi har tillhandahållit dessa rikt linjer specifikt för Premium Storage eftersom arbets belastningar som körs på Premium Storage är mycket prestanda känsliga. Vi har fått exempel där det är lämpligt. Du kan också använda vissa av dessa rikt linjer för program som körs på virtuella IaaS-datorer med standard lagrings diskar.

> [!NOTE]
> Ibland är det som verkar vara ett disk prestanda problem i själva verket en nätverks Flask hals. I dessa fall bör du optimera [nätverks prestandan](../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Om du vill mäta din disk kan du läsa våra artiklar om att mäta en disk:
>
> * För Linux: [benchmarka ditt program på Azure-disklagring](linux/disks-benchmarks.md)
> * För Windows: [benchmarking a disk](windows/disks-benchmarks.md).
>
> Om din virtuella dator har stöd för accelererat nätverk bör du kontrol lera att den är aktive rad. Om den inte är aktive rad kan du aktivera den på redan distribuerade virtuella datorer på både [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Innan du börjar, om du är nybörjare på Premium Storage, läser du först [Välj en Azure-disktyp för virtuella datorer med IaaS](disks-types.md) och [skalbarhets mål för Premium Page Blob Storage-konton](../storage/blobs/scalability-targets-premium-page-blobs.md).

## <a name="application-performance-indicators"></a>Program prestanda indikatorer

Vi bedömer om ett program presterar bra eller inte använder prestanda indikatorer, t. ex. hur snabbt ett program bearbetar en användarbegäran, hur mycket data som ett program bearbetar per begäran, hur lång tid det tar för en användare att bearbeta en viss tids period, hur länge en användare måste vänta på att få ett svar efter att ha skickat in sin begäran. De tekniska villkoren för dessa prestanda indikatorer är, IOPS, data flöde, bandbredd och latens.

I det här avsnittet kommer vi att diskutera vanliga prestanda indikatorer i samband med Premium Storage. I följande avsnitt, som samlar in program krav, kommer du att lära dig hur du mäter dessa prestanda indikatorer för ditt program. Senare i optimeringen av program prestanda får du lära dig mer om faktorer som påverkar dessa prestanda indikatorer och rekommendationer för att optimera dem.

## <a name="iops"></a>IOPS

IOPS-eller in-/utdata-åtgärder per sekund är antalet förfrågningar som programmet skickar till lagrings diskarna i en sekund. En in-/utdata-åtgärd kan läsas eller skrivas, sekventiellt eller slumpmässigt. OLTP-program (Online Transaction Processing) som en online-webbplats måste bearbeta många samtidiga användar förfrågningar omedelbart. Användar förfrågningarna infogar och uppdaterar intensiva databas transaktioner, vilket programmet måste bearbeta snabbt. Därför kräver OLTP-program mycket höga IOPS. Sådana program hanterar miljon tals små och slumpmässiga IO-begäranden. Om du har ett sådant program måste du utforma program infrastrukturen för att optimera för IOPS. I avsnittet senare, *optimera program prestanda*, diskuterar vi i detalj alla faktorer som du måste tänka på för att få hög IOPS.

När du kopplar en premiumlagringsdisk till en virtuell dator på hög nivå tilldelar Azure ett garanterat antal IOPS enligt diskspecifikationen. Exempel: en P50-disk ger 7500 IOPS. Varje storlek för virtuella datorer på hög nivå har en specifik IOPS-gräns. Till exempel har en standard GS5 VM 80 000 IOPS.

## <a name="throughput"></a>Dataflöde

Data flöde eller bandbredd är den mängd data som programmet skickar till lagrings diskarna inom ett angivet intervall. Om ditt program utför in-/utdata-åtgärder med stora IO-enheter, kräver det stora data flöden. Data lager program tenderar att utfärda skannings intensiva åtgärder som kommer åt stora delar av data i taget och ofta utför Mass åtgärder. Med andra ord kräver sådana program högre data flöde. Om du har ett sådant program måste du utforma dess infrastruktur för att optimera data flödet. I nästa avsnitt diskuterar vi i detalj de faktorer som du måste finjustera för att uppnå detta.

När du ansluter en Premium Storage-disk till en virtuell dator med hög skalning, etablerar Azure Data flödet enligt disk specifikationen. Till exempel tilldelas en P50-disk ett dataflöde på 250 MB per sekund per disk. Varje storlek för virtuella datorer på hög nivå har en specifik gräns för genomflödet. Till exempel, den virtuella datorn Standard GS5 har en maximal genomströmning på 2 000 MB per sekund.

Det finns en relation mellan genomflödet och IOPS som visas i formeln nedan.

![Relation för IOPS och data flöde](linux/media/premium-storage-performance/image1.png)

Därför är det viktigt att fastställa de optimala data flödes-och IOPS-värden som programmet kräver. När du försöker optimera en, påverkas även den andra. I ett senare avsnitt, *optimera program prestanda*, diskuterar vi mer information om att optimera IOPS och data flöden.

## <a name="latency"></a>Svarstid

Svars tiden är den tid det tar för ett program att ta emot en enskild begäran, skicka det till lagrings diskarna och skicka svaret till klienten. Detta är ett kritiskt mått för programmets prestanda utöver IOPS och data flöde. Svars tiden för en Premium Storage-disk är den tid det tar att hämta information för en begäran och att kommunicera tillbaka till ditt program. Premium Storage ger konsekvent låg fördröjning. Premium diskar är utformade för att tillhandahålla ensiffriga MS-fördröjningar för de flesta i/o-åtgärder. Om du aktiverar skrivskyddad cachelagring av värden på Premium Storage-diskar kan du få mycket lägre Läs fördröjning. Vi kommer att diskutera diskcachelagring i mer detalj i avsnittet senare om hur du *optimerar program prestanda*.

När du optimerar ditt program för att få högre IOPS och data flöde kommer det att påverka svars tiden för ditt program. När du har justerat program prestandan ska du alltid utvärdera svars tiden för programmet för att undvika oväntad hög latens-beteende.

Följande kontroll Plans åtgärder på Managed Disks kan innebära att disken flyttas från en lagrings plats till en annan. Detta dirigeras via bakgrunds kopia av data som kan ta flera timmar att slutföra, vanligt vis mindre än 24 timmar beroende på mängden data i diskarna. Under den tiden kan ditt program uppleva högre än vanliga Läs fördröjningar eftersom vissa läsningar kan omdirigeras till den ursprungliga platsen och det kan ta längre tid att slutföra. Skriv fördröjningen påverkas inte under den här perioden.

- Uppdatera lagrings typen.
- Koppla från och koppla en disk från en virtuell dator till en annan.
- Skapa en hanterad disk från en virtuell hård disk.
- Skapa en hanterad disk från en ögonblicks bild.
- Konvertera ohanterade diskar till hanterade diskar.

## <a name="performance-application-checklist-for-disks"></a>Check lista för prestanda program för diskar

Det första steget i att utforma högpresterande program som körs på Azure Premium Storage är att förstå programmets prestanda krav. När du har samlat in prestanda krav kan du optimera ditt program för att uppnå bästa möjliga prestanda.

I föregående avsnitt förklarade vi vanliga prestanda indikatorer, IOPS, data flöde och svars tid. Du måste identifiera vilka av dessa prestanda indikatorer som är viktiga för ditt program för att leverera den önskade användar upplevelsen. Till exempel är höga IOPS-frågor mest till OLTP-program som bearbetar miljon tals transaktioner i en sekund. Högt data flöde är kritiskt för data lager program som bearbetar stora mängder data i en sekund. Extremt låg latens är avgörande för real tids program som webbplatser för direkt uppspelning av video.

Därefter mäter du programmets högsta prestanda krav under dess livstid. Använd exempel check lista nedan som en start. Registrera de maximala prestanda kraven under perioder för normala, högsta och andra arbets timmar. Genom att identifiera krav för alla nivåer av arbets belastningar kan du fastställa programmets övergripande prestanda krav. Den normala arbets belastningen för en e-handelswebbplats är till exempel de transaktioner som används under de flesta dagar under ett år. Den högsta arbets belastningen hos webbplatsen är de transaktioner som används under jul-eller särskilda försäljnings händelser. Hög belastnings belastningen har normalt en begränsad period, men kan kräva att ditt program skalar två eller fler gånger om den normala driften. Ta reda på 50 percentil, 90 percentil och 99 percentils krav. Detta hjälper dig att filtrera bort avvikande värden i prestanda kraven och du kan fokusera på dina ansträngningar för att optimera för rätt värden.

## <a name="application-performance-requirements-checklist"></a>Check lista för program prestanda krav

| **Prestandakrav** | **50 percentil** | **90 percentil** | **99 percentil** |
| --- | --- | --- | --- |
| Max. Transaktioner per sekund | | | |
| % Läs åtgärder | | | |
| % Skriv åtgärder | | | |
| % Slumpmässiga åtgärder | | | |
| % Sekventiella åtgärder | | | |
| Storlek för IO-begäran | | | |
| Genomsnittligt data flöde | | | |
| Max. Dataflöde | | | |
| Minimum. Svarstid | | | |
| Genomsnittlig svars tid | | | |
| Max. Processor | | | |
| Genomsnittlig CPU | | | |
| Max. Minne | | | |
| Genomsnittligt minne | | | |
| Ködjup | | | |

> [!NOTE]
> Du bör överväga att skala dessa siffror baserat på den förväntade framtida tillväxten av ditt program. Det är en bra idé att planera för tillväxt i förväg, eftersom det kan vara svårare att ändra infrastrukturen för att förbättra prestanda senare.

Om du har ett befintligt program och vill flytta till Premium Storage börjar du med att bygga check listan ovan för det befintliga programmet. Skapa sedan en prototyp av ditt program på Premium Storage och utforma programmet baserat på rikt linjer som beskrivs i *optimera program prestanda* i ett senare avsnitt i det här dokumentet. I nästa artikel beskrivs de verktyg du kan använda för att samla in prestanda mått.

### <a name="counters-to-measure-application-performance-requirements"></a>Räknare för att mäta program prestanda krav

Det bästa sättet att mäta prestanda kraven för ditt program är att använda verktyg för prestanda övervakning som tillhandahålls av serverns operativ system. Du kan använda PerfMon för Windows och iostat för Linux. Dessa verktyg fångar in räknare som motsvarar varje mått som beskrivs i avsnittet ovan. Du måste samla in värdena för dessa räknare när ditt program kör arbets belastningarna normal, högsta och andra arbets tid.

PerfMon-räknarna är tillgängliga för processor, minne och, varje logisk disk och fysisk disk på servern. När du använder Premium Storage-diskar med en virtuell dator är räknarna för fysiska diskar för varje Premium Storage-disk och räknare för logiska diskar för varje volym som skapas på Premium Storage-diskarna. Du måste samla in värdena för diskarna som är värdar för din program arbets belastning. Om det finns en till en mappning mellan logiska och fysiska diskar kan du referera till fysiska disk räknare. Se i övrigt räknare för logiska diskar. I Linux genererar kommandot iostat en processor-och disk användnings rapport. Disk användnings rapporten innehåller statistik per fysisk enhet eller partition. Om du har en databas server med data och loggar på separata diskar samlar du in dessa data för båda diskarna. I tabellen nedan beskrivs räknare för diskar, processorer och minne:

| Räknare | Description | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS eller transaktioner per sekund** |Antalet I/O-begäranden som har utfärdats till lagrings disken per sekund. |Disk läsningar/SEK <br> Disk skrivningar/SEK |TPS <br> r/s <br> w/s |
| **Disk läsningar och skrivningar** |% av Läs-och skriv åtgärder som utförts på disken. |% Disk Läs tid <br> Disk skrivnings tid i procent |r/s <br> w/s |
| **Dataflöde** |Mängden data som läses från eller skrivs till disken per sekund. |Disk-lästa byte/s <br> Disk-skrivna byte/s |kB_read/s <br> kB_wrtn/s |
| **Svarstid** |Total tid för att slutföra en disk-IO-begäran. |Medel s/disk läsning <br> Medel s/disk skrivning |sena <br> svctm |
| **I/o-storlek** |Storleken på I/O-begäranden till lagrings diskarna. |Genomsnittligt antal Disk byte/läsning <br> Genomsnittlig Disk byte/skrivning |avgrq-sz |
| **Ködjup** |Antal väntande I/O-begäranden som väntar på att läsas från eller skrivas till lagrings disken. |Aktuell diskkölängd |avgqu-sz |
| **Högsta minnes storlek** |Mängden minne som krävs för att köra programmet smidigt |% använda dedikerade byte |Använd vmstat |
| **Max. CPU** |PROCESSOR mängd som krävs för att köra programmet smidigt |% Processor tid |% util |

Läs mer om [iostat](https://linux.die.net/man/1/iostat) och [perfmon](/windows/win32/perfctrs/performance-counters-portal).



## <a name="optimize-application-performance"></a>Optimera program prestanda

De huvudsakliga faktorer som påverkar prestanda för ett program som körs på Premium Storage är arten av IO-begäranden, VM-storlek, disk storlek, antal diskar, diskcachelagring, multitrådning och ködjup. Du kan styra några av de här faktorerna med rattar som tillhandahålls av systemet. De flesta program kan inte ge dig möjlighet att ändra i/o-storlek och ködjup direkt. Om du till exempel använder SQL Server kan du inte välja IO-storlek och ködjup. SQL Server väljer den optimala storleken för IO-storlek och ködjup för att få ut mesta möjliga prestanda. Det är viktigt att förstå effekterna av båda typerna av faktorer i programmets prestanda, så att du kan etablera lämpliga resurser för att möta prestanda behoven.

I det här avsnittet finns en check lista för program krav som du har skapat för att identifiera hur mycket du behöver för att optimera program prestandan. Utifrån detta kommer du att kunna avgöra vilka faktorer från det här avsnittet som du måste justera. Om du vill beräkna effekterna av varje faktor i programmets prestanda kör du benchmarking-verktyg i program konfigurationen. Mer information om hur du kör vanliga benchmarking-verktyg på virtuella Windows-och Linux-datorer finns i artikeln om benchmarking-artikeln, som är länkad i slutet.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optimera IOPS, data flöde och svars tider snabbt

I tabellen nedan sammanfattas prestanda faktorer och de steg som krävs för att optimera IOPS, data flöde och svars tid. I avsnitten som följer den här sammanfattningen beskrivs varje faktor mycket mer djup.

Mer information om storlekar på virtuella datorer och om IOPS, data flöde och svars tid som är tillgängliga för varje typ av virtuell dator finns i [storlekar för virtuella datorer i Azure](sizes.md).

| | **IOPS** | **Dataflöde** | **Svarstid** |
| --- | --- | --- | --- |
| **Exempel scenario** |Enterprise OLTP-program som kräver mycket höga transaktioner per sekund. |Företags data lager hanterings program bearbetar stora mängder data. |Nära real tids program som kräver omedelbara svar på användar förfrågningar, t. ex. online-spel. |
| **Prestanda faktorer** | &nbsp; | &nbsp; | &nbsp; |
| **I/o-storlek** |Mindre IO-storlek ger högre IOPS. |Större IO-storlek för att ge högre data flöde. | &nbsp;|
| **VM-storlek** |Använd en VM-storlek som erbjuder IOPS som är större än ditt program krav. |Använd en VM-storlek med en data flödes gräns som är större än ditt program krav. |Använd en VM-storlek som ger större skalnings gränser än ditt program krav. |
| **Disk storlek** |Använd en disk storlek som erbjuder IOPS som är större än ditt program krav. |Använd en disk storlek med data flödes gräns som är större än ditt program krav. |Använd en disk storlek som ger större skalnings gränser än ditt program krav. |
| **Begränsningar för virtuell dator och disk skala** |IOPS-gränsen för den valda virtuella dator storleken ska vara större än det totala antalet IOPS som är kopplade till den. |Data flödes gränsen för den valda virtuella dator storleken ska vara större än det totala data flödet som styrs av Premium Storage-diskar som är anslutna till den |Skalnings gränserna för den valda virtuella dator storleken måste vara större än de sammanlagda skalnings gränserna för anslutna Premium Storage-diskar. |
| **Diskcachelagring** |Aktivera ReadOnly-cache på Premium Storage-diskar med Läs tung-åtgärder för att få högre Läs-IOPS. | &nbsp; |Aktivera ReadOnly-cache på Premium Storage-diskar med klara tung åtgärder för att få mycket låg Läs fördröjning. |
| **Disk randning** |Använd flera diskar och ta bort dem tillsammans för att få en kombination av högre IOPS och data flödes gräns. Den kombinerade gränsen per virtuell dator måste vara högre än de sammanlagda gränserna för anslutna Premium diskar. | &nbsp; | &nbsp; |
| **Rand storlek** |Mindre rand storlek för slumpmässiga små IO-mönster som visas i OLTP-program. Använd till exempel stripe-storlek på 64 KB för SQL Server OLTP-program. |Större rand storlek för sekventiellt stort IO-mönster visas i data lager program. Använd till exempel 256 KB stripe-storlek för SQL Server data lager program. | &nbsp; |
| **Flertrådsteknik** |Använd flera trådar för att push-överföra fler begär anden till Premium Storage som leder till högre IOPS och data flöde. På SQL Server till exempel ange ett högt MAXDOP-värde för att allokera fler processorer till SQL Server. | &nbsp; | &nbsp; |
| **Ködjup** |Större ködjup ger högre IOPS. |Större ködjup ger högre genomflöde. |Mindre ködjup ger lägre fördröjning. |

## <a name="nature-of-io-requests"></a>Typ av IO-begäranden

En IO-begäran är en enhet med in-/utdata-åtgärd som programmet kommer att utföra. Att identifiera vilken typ av IO-begäranden, slumpmässig eller sekventiell, läsa eller skriva, liten eller stor, hjälper dig att avgöra programmets prestanda krav. Det är viktigt att förstå vilken typ av IO-begäranden som krävs för att fatta rätt beslut när du utformar program infrastrukturen. IOs måste distribueras jämnt för att uppnå bästa möjliga prestanda.

I/o-storlek är en av de viktiga faktorerna. I/o-storleken är storleken på begäran om indata/utdata som genererats av ditt program. I/o-storleken har en betydande inverkan på prestanda, särskilt på den IOPS och bandbredd som programmet kan uppnå. Följande formel visar förhållandet mellan IOPS, IO-storlek och bandbredd/data flöde.  
    ![Ett diagram som visar formeln I O P S gånger I O-storlek är lika med data flödet.](media/premium-storage-performance/image1.png)

I vissa program kan du ändra deras IO-storlek, medan vissa program inte gör det. SQL Server bestämmer till exempel den optimala i/o-storleken och ger inte användare med någon ratt att ändra den. Å andra sidan tillhandahåller Oracle en parameter med namnet [db \_ block \_ storlek](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) som du kan använda för att konfigurera i/O-begäran storlek för databasen.

Om du använder ett program, som inte tillåter att du ändrar i/o-storlek, använder du rikt linjerna i den här artikeln för att optimera prestanda-KPI: n som är mest relevant för ditt program. Exempel:

* Ett OLTP-program genererar miljon tals små och slumpmässiga IO-begäranden. Om du vill hantera dessa typer av IO-begäranden måste du utforma program infrastrukturen för att få högre IOPS.  
* Ett data lager program genererar stora och sekventiella IO-begäranden. Om du vill hantera dessa typer av IO-begäranden måste du utforma program infrastrukturen för att få högre bandbredd eller data flöde.

Om du använder ett program, som gör att du kan ändra i/o-storlek, använder du den här regeln för i/o-storleken förutom andra rikt linjer för prestanda.

* Mindre IO-storlek för att få högre IOPS. Till exempel 8 KB för ett OLTP-program.  
* Större IO-storlek för att få högre bandbredd/data flöde. Till exempel 1024 KB för ett data lager program.

Här är ett exempel på hur du kan beräkna IOPS och data flöde/bandbredd för ditt program. Överväg ett program med hjälp av en P30-disk. Högsta antal IOPS och data flöde/bandbredd som en P30 disk kan uppnå är 5000 IOPS och 200 MB per sekund. Om ditt program kräver högsta IOPS från P30-disken och du använder en mindre IO-storlek som 8 KB, kommer den resulterande bandbredd som du kan hämta vara 40 MB per sekund. Men om ditt program kräver maximalt data flöde/bandbredd från P30 disk, och du använder en större IO-storlek som 1024 KB, kommer den resulterande IOPS att vara mindre än 200 IOPS. Därför bör du justera i/o-storleken så att den uppfyller både ditt programs IOPS och data flöde/bandbredds krav. I följande tabell sammanfattas olika IO-storlekar och deras motsvarande IOPS och data flöde för en P30 disk.

| Program krav | I/O-storlek | IOPS | Genom strömning/bandbredd |
| --- | --- | --- | --- |
| Maximalt IOPS |8 kB |5 000 |40 MB per sekund |
| Maximalt data flöde |1024 KB |200 |200 MB per sekund |
| Maximalt data flöde + hög IOPS |64 kB |3 200 |200 MB per sekund |
| Högsta IOPS + högt data flöde |32 KB |5 000 |160 MB per sekund |

Om du vill få IOPS och bandbredd som är högre än det högsta värdet för en enskild Premium Storage-disk, använder du flera Premium diskar stripe tillsammans. Du kan till exempel ta bort två P30 diskar för att få en kombination av IOPS på 10 000 IOPS eller ett kombinerat data flöde på 400 MB per sekund. Som förklaras i nästa avsnitt måste du använda en VM-storlek som stöder den kombinerade diskens IOPS och data flöde.

> [!NOTE]
> När du ökar antingen IOPS eller data flödet kan andra också öka, se till att du inte trycker på genomflödet eller IOPS-gränser för disken eller den virtuella datorn när du ökar en.

För att vittnea effekterna av IO-storleken på program prestanda kan du köra benchmarking-verktyg på din virtuella dator och diskar. Skapa flera test körningar och Använd en annan IO-storlek för varje körning för att se effekten. Mer information finns i artikeln om benchmarking, som är länkad i slutet.

## <a name="high-scale-vm-sizes"></a>Hög skalning av VM-storlekar

När du börjar utforma ett program, en av de första saker som du bör göra, väljer du en virtuell dator som är värd för ditt program. Premium Storage levereras med hög skalnings storlekar för virtuella datorer som kan köra program som kräver högre beräknings kraft och en hög I/O-prestanda för lokala diskar. De här virtuella datorerna ger snabbare processorer, ett högre förhållandet mellan minne och kärna och en Solid-State hård disk (SSD) för den lokala disken. Exempel på virtuella datorer med hög skalning som stöder Premium Storage är virtuella datorer i DS och GS-serien.

Virtuella datorer med hög skalning är tillgängliga i olika storlekar med olika antal processor kärnor, minne, OS och temporär disk storlek. Varje VM-storlek har också maximalt antal data diskar som du kan ansluta till den virtuella datorn. Det innebär att den valda virtuella dator storleken påverkar hur mycket bearbetning, minne och lagrings kapacitet som är tillgängligt för ditt program. Det påverkar också beräknings-och lagrings kostnaden. Nedan visas till exempel specifikationerna för den största virtuella dator storleken i en DS-serie och GS-serien:

| Storlek på virtuell dator | Processorkärnor | Minne | Disk storlekar för virtuella datorer | Max. data diskar | Cachestorlek | IOPS | IO-gränser för bandbredds cache |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Lokal SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB per sekund |4 000 IOPS och 33 MB per sekund |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Lokal SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB per sekund |5 000 IOPS och 50 MB per sekund |

Om du vill visa en fullständig lista över alla tillgängliga storlekar för virtuella Azure-datorer, se [storlekar för virtuella datorer i Azure](sizes.md) eller. Välj en VM-storlek som kan mötas och skalas till önskade program prestanda krav. Förutom detta bör du ta hänsyn till följande viktiga överväganden när du väljer VM-storlekar.

*Skalnings gränser*  
De högsta IOPS-gränserna per virtuell dator och per disk är olika och oberoende av varandra. Se till att programmet kör IOPS inom gränserna för den virtuella datorn samt de Premium-diskar som är kopplade till den. Annars kommer program prestanda att drabbas av begränsning.

Anta till exempel att ett program krav är högst 4 000 IOPS. För att åstadkomma detta etablerar du en P30-disk på en DS1-VM. P30-disken kan leverera upp till 5 000 IOPS. Den virtuella DS1-datorn är dock begränsad till 3 200 IOPS. Det innebär att program prestandan begränsas av den virtuella datorns gräns vid 3 200 IOPS och att prestanda försämras. För att förhindra den här situationen väljer du en virtuell dator och disk storlek som båda uppfyller program kraven.

*Drifts kostnader*  
I många fall är det möjligt att den totala kostnaden för åtgärden med Premium Storage är lägre än att använda standard lagring.

Överväg till exempel ett program som kräver 16 000 IOPS. För att uppnå den här prestandan behöver du en standard \_ D14 Azure IaaS VM, som kan ge en maximal IOPS på 16 000 med 32 standard lagring 1 TB diskar. Varje 1 – TB standard lagrings disk kan uppnå högst 500 IOPS. Den uppskattade kostnaden för den här virtuella datorn per månad blir $1 570. Månads kostnaden för 32 standard lagrings diskar är $1 638. Den uppskattade totala månads kostnaden blir $3 208.

Men om du har samma program på Premium Storage behöver du en mindre VM-storlek och färre Premium Storage-diskar, vilket minskar den totala kostnaden. En vanlig \_ virtuell DS13-dator kan uppfylla 16 000 IOPS-kravet med fyra P30-diskar. Den virtuella datorn DS13 har en maximal IOPS på 25 600 och varje P30-disk har maximalt IOPS på 5 000. Som helhet kan den här konfigurationen uppnå 5 000 x 4 = 20 000 IOPS. Den uppskattade kostnaden för den här virtuella datorn per månad blir $1 003. Månads kostnaden för fyra P30 Premium Storage-diskar är $544,34. Den uppskattade totala månads kostnaden blir $1 544.

I tabellen nedan sammanfattas kostnads nedbrytningen för det här scenariot för standard och Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Kostnad för virtuell dator per månad** |$1 570,58 (standard \_ D14) |$1 003,66 (standard \_ DS13) |
| **Kostnad för diskar per månad** |$1 638,40 (32 x 1 – TB diskar) |$544,34 (4 x P30 diskar) |
| **Total kostnad per månad** |$3 208,98 |$1 544,34 |

*Linux-distributioner*  

Med Azure Premium Storage får du samma prestanda nivå för virtuella datorer som kör Windows och Linux. Vi har stöd för många varianter Linux-distributioner och du kan se den fullständiga listan [här](linux/endorsed-distros.md). Det är viktigt att Observera att olika distributioner passar bättre för olika typer av arbets belastningar. Du kan se olika prestanda nivåer beroende på vilken distribution din arbets belastning körs på. Testa Linux-distributioner med ditt program och välj det som fungerar bäst.

När du kör Linux med Premium Storage kontrollerar du de senaste uppdateringarna om nödvändiga driv rutiner för att garantera höga prestanda.

## <a name="premium-storage-disk-sizes"></a>Storlek på Premium Storage-diskar

Azure Premium Storage erbjuder en mängd olika storlekar så att du kan välja en som passar dina behov bäst. Varje disk storlek har en annan skalnings gräns för IOPS, bandbredd och lagring. Välj rätt Premium Storage disk storlek beroende på program kraven och den virtuella datorns storlek för hög skalning. I tabellen nedan visas disk storlekar och deras funktioner. P4-, P6-, p15-, P60-, P70-och P80-storlekarna stöds för närvarande bara för Managed Disks.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Hur många diskar du väljer beror på vilken disk storlek som valts. Du kan använda en enskild P50-disk eller flera P10-diskar för att uppfylla ditt program krav. Ta hänsyn till de överväganden som visas nedan när du gör valet.

*Skalnings gränser (IOPS och data flöde)*  
IOPS-och data flödes gränserna för varje Premium disk-storlek är olika och oberoende av skalnings gränserna för virtuella datorer. Kontrol lera att total IOPS och data flöde från diskarna ligger inom skalnings gränserna för den valda virtuella dator storleken.

Om ett program krav till exempel är högst 250 MB/s genom strömning och du använder en virtuell DS4-dator med en enda P30-disk. Den virtuella datorn DS4 kan ge upp till 256 MB/s-genomflöde. En enda P30-disk har dock en gräns på 200 MB/SEK. Programmet kommer därför att begränsas med 200 MB/s på grund av disk gränsen. Du kan lösa den här gränsen genom att etablera fler än en data disk till den virtuella datorn eller ändra storlek på diskarna till P40 eller P50.

> [!NOTE]
> Läsningar som hanteras av cachen ingår inte i disk-IOPS och data flödet, och kan därför inte omfattas av disk gränser. Cachen har en separat IOPS och data flödes gräns per virtuell dator.
>
> Till exempel är dina läsningar och skrivningar från början 60MB/SEK respektive 40MB/s. Med tiden kan cachen värma upp och bevarar mer av läsningarna från cachen. Sedan kan du få högre Skriv data flöde från disken.

*Antal diskar*  
Fastställ hur många diskar du behöver för att utvärdera program kraven. Varje VM-storlek har också en gräns för antalet diskar som du kan ansluta till den virtuella datorn. Vanligt vis är detta två gånger antalet kärnor. Se till att den virtuella dator storleken du väljer har stöd för antalet diskar som behövs.

Kom ihåg att Premium Storage diskar har högre prestanda funktioner jämfört med standard lagrings diskar. Om du migrerar ditt program från den virtuella Azure IaaS-datorn med standard lagring till Premium Storage, behöver du förmodligen färre Premium diskar för att uppnå samma eller högre prestanda för ditt program.

## <a name="disk-caching"></a>Diskcachelagring

Virtuella datorer med hög skalning som utnyttjar Azure Premium Storage har en teknik för cachelagring på flera nivåer som kallas BlobCache. BlobCache använder en kombination av värd-RAM och lokal SSD för cachelagring. Den här cachen är tillgänglig för Premium Storage beständiga diskar och lokala VM-diskar. Den här cache-inställningen är som standard inställd på läsa/skriva för OS-diskar och ReadOnly för data diskar som finns på Premium Storage. När diskcachelagring är aktiverat på Premium Storage diskar kan virtuella datorer med hög skala uppnå mycket höga prestanda nivåer som överstiger den underliggande disk prestandan.

> [!WARNING]
> Diskcachelagring stöds inte för disk 4 TiB och större. Om flera diskar är anslutna till den virtuella datorn kommer varje disk som är mindre än 4 TiB att ha stöd för cachelagring.
>
> När du ändrar cacheinställningen för en Azure-disk så frånkopplas och återansluts måldisken. Om det är operativ system disken startas den virtuella datorn om. Stoppa alla program/tjänster som kan påverkas av det här avbrottet innan du ändrar inställningen för diskcachelagring. De här rekommendationerna kan leda till skadade data.

Mer information om hur BlobCache fungerar finns i blogg inlägget i [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

Det är viktigt att aktivera cache på rätt disk uppsättning. Huruvida du bör aktivera diskcachelagring på en Premium-disk eller inte beror på arbets belastnings mönstret som disken ska hantera. I tabellen nedan visas standardinställningarna för cache för OS-och data diskar.

| **Disktyp** | **Standardinställning för cacheminne** |
| --- | --- |
| OS-disk |ReadWrite |
| Datadisk |ReadOnly |

Följande är de rekommenderade diskens cacheinställningar för data diskar,

| **Inställning av diskcachelagring** | **rekommendation när du ska använda den här inställningen** |
| --- | --- |
| Ingen |Konfigurera värd-cachen som ingen för skrivbara och skrivbara diskar. |
| ReadOnly |Konfigurera Host-cache som skrivskyddat för skrivskyddade och Läs-och skriv diskar. |
| ReadWrite |Konfigurera Host-cache enbart som ReadWrite om ditt program hanterar skrivningen av cachelagrade data korrekt till beständiga diskar vid behov. |

*ReadOnly*  
Genom att konfigurera ReadOnly-cachelagring på Premium Storage data diskar kan du få låg Läs fördröjning och få mycket hög Läs-IOPS och data flöde för ditt program. Detta beror på två orsaker,

1. Läsningar som utförs från cache, som finns på det virtuella dator minnet och lokal SSD, är mycket snabbare än läsningar från data disken, som finns i Azure Blob Storage.  
1. Premium Storage räknar inte vilka läsningar som hanteras från cachen, mot diskens IOPS och data flöde. Därför kan ditt program uppnå högre total IOPS och data flöde.

*ReadWrite*  
Som standard har OS-diskar ReadWrite-cachelagring aktiverat. Vi har nyligen lagt till stöd för ReadWrite-cachelagring på data diskar även. Om du använder ReadWrite-cachelagring måste du ha ett korrekt sätt att skriva data från cache till beständiga diskar. SQL Server hanterar till exempel att skriva cachelagrade data till de beständiga lagrings diskarna. Att använda ReadWrite cache med ett program som inte hanterar beständiga data kan leda till data förlust, om den virtuella datorn kraschar.

*Ingen*  
För närvarande stöds **ingen** på data diskar. Den stöds inte på OS-diskar. Om du anger **ingen** på en operativ system disk åsidosätts detta internt och anges som **skrivskyddad**.

Du kan till exempel använda dessa rikt linjer för att SQL Server som körs på Premium Storage genom att göra följande:

1. Konfigurera "ReadOnly"-cache på Premium Storage-diskar som värd för data filer.  
   a.  Snabb läsningarna från cachen sänker tiden för SQL Server fråga eftersom data sidor hämtas mycket snabbare från cachen jämfört med direkt från data diskarna.  
   b.  Att betjäna läsningar från cache innebär att det finns ytterligare data flöde tillgängliga från Premium data diskar. SQL Server kan använda detta ytterligare data flöde för att hämta fler data sidor och andra åtgärder som säkerhets kopiering/återställning, batch-belastning och index återuppbyggnad.  
1. Konfigurera cachen "inget" på Premium Storage-diskar som är värd för loggfilerna.  
   a.  Loggfiler har främst Skriv åtgärder. Därför drar de inte ut den skrivskyddade cachen.

## <a name="optimize-performance-on-linux-vms"></a>Optimera prestanda på virtuella Linux-datorer

För alla Premium-SSD eller Ultra disks kan du eventuellt inaktivera "barriärer" för fil system på disken för att förbättra prestandan när det är känt att det inte finns några cacheminnen som kan förlora data.  Om Azure-diskcachelagring är inställt på ReadOnly eller ingen kan du inaktivera barriärer.  Men om cachelagring är inställt på ReadWrite bör barriärerna förbli aktiverade för att säkerställa Skriv tåligheten.  Barriärer är vanligt vis aktiverade som standard, men du kan inaktivera barriärer med någon av följande metoder beroende på typ av fil system:

* För **reiserFS** använder du alternativet barriär = ingen montering för att inaktivera barriärer.  Använd barriär = Flush för att uttryckligen aktivera hinder.
* För **EXT3/Ext4** använder du alternativet barriär = 0 montering för att inaktivera barriärer.  Använd barriär = 1 för att uttryckligen aktivera barriärer.
* För **xfs** använder du monterings alternativet nobarriär för att inaktivera barriärer.  Använd barriärer för att uttryckligen aktivera barriärer.  Observera att i senare Linux-kernel-versioner säkerställer utformningen av XFS-filsystemet alltid hållbarhet och inaktive ring av barriärer har ingen påverkan.  

## <a name="disk-striping"></a>Disk randning

När en virtuell dator med hög skalning är kopplad till flera beständiga diskar för Premium Storage kan diskarna slås samman för att aggregera deras IOPs-, bandbredds-och lagrings kapacitet.

I Windows kan du använda lagrings utrymmen för att Stripa diskar tillsammans. Du måste konfigurera en kolumn för varje disk i en pool. Annars kan den övergripande prestandan för stripe-volymer vara lägre än förväntat, på grund av ojämn fördelning av trafik över diskarna.

Viktigt: med Serverhanteraren användar gränssnitt kan du ange det totala antalet kolumner upp till 8 för en stripe-volym. När du ansluter fler än åtta diskar använder du PowerShell för att skapa volymen. Med hjälp av PowerShell kan du ange antalet kolumner som motsvarar antalet diskar. Till exempel, om det finns 16 diskar i en enda stripe-uppsättning. Ange 16 kolumner i parametern *NumberOfColumns* för PowerShell-cmdleten *New-VirtualDisk* .

I Linux använder du MDADM-verktyget för att Stripa diskar tillsammans. Detaljerade anvisningar om hur du tar bort diskar i Linux, se [Konfigurera programvaru-RAID på Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).

*Rand storlek*  
En viktig konfiguration i disk ränder är stripe-storleken. Stripe-storlek eller block storlek är det minsta data segmentet som programmet kan adressera på en stripe-volym. Stripe-storleken som du konfigurerar beror på typen av program och dess fråge mönster. Om du väljer fel rand storlek kan det leda till i/o-fel i i/o, vilket leder till försämrade prestanda för ditt program.

Om t. ex. en IO-begäran som genererats av ditt program är större än storleken på disk stripe skriver lagrings systemet det över stripe-enhetens gränser på mer än en disk. När det är dags att komma åt dessa data måste de söka i fler än en rand enhet för att slutföra begäran. Den kumulativa påverkan av sådant beteende kan leda till avsevärd prestanda försämring. Å andra sidan, om storleken på IO-begärandena är mindre än stripe-storlek, och om den är slumpmässig i natur, kan IO-begärandena lägga till upp på samma disk som orsakar en Flask hals och i slut ändan försämra IO-prestandan.

Välj en lämplig rand storlek beroende på vilken typ av arbets belastning ditt program körs på. Använd en mindre rand storlek för slumpmässiga små IO-begäranden. För stora sekventiella IO-begäranden används en större rand storlek. Ta reda på rand storleks rekommendationerna för det program som du kommer att köra på Premium Storage. För SQL Server konfigurerar du stripe-storlek på 64 KB för OLTP-arbetsbelastningar och 256 KB för arbets belastningar för data lager. Mer information finns i [metod tips för prestanda för SQL Server på virtuella Azure-datorer](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) .

> [!NOTE]
> Du kan ta bort högst 32 Premium Storage-diskar på en VM för VM-serien och 64 Premium Storage-diskar på en virtuell GS-serien.

## <a name="multi-threading"></a>Multi-threading

Azure har utformat Premium Storage plattform för att vara massivt parallell. Ett program med flera trådar uppnår därför mycket högre prestanda än ett enda trådat program. Ett program med flera trådar delar upp sina aktiviteter över flera trådar och ökar effektiviteten i körningen genom att använda den virtuella datorns och disk resurserna till max.

Om ditt program till exempel körs på en enskild kärn virtuell dator med två trådar, kan Processorn växla mellan de två trådarna för att uppnå effektiviteten. Även om en tråd väntar på att en disk i/o ska slutföras, kan CPU: n växla till den andra tråden. På så sätt kan två trådar utföra mer än en enskild tråd. Om den virtuella datorn har mer än en kärna minskar den ytterligare körnings tiden eftersom varje kärna kan köra aktiviteter parallellt.

Du kanske inte kan ändra hur ett program utanför hyllan implementerar enkel trådning eller flera trådar. SQL Server kan till exempel hantera multi-CPU och flera kärnor. SQL Server bestämmer dock under vilka omständigheter som ska utnyttja en eller flera trådar för att bearbeta en fråga. Den kan köra frågor och skapa index med flera trådar. För en fråga som involverar koppling av stora tabeller och sortering av data innan användaren återgår till användaren, kommer SQL Server förmodligen att använda flera trådar. En användare kan dock inte styra om SQL Server kör en fråga med hjälp av en enda tråd eller flera trådar.

Det finns konfigurations inställningar som du kan ändra för att påverka den här multi-threading-eller parallell bearbetningen av ett program. Om SQL Server till exempel den högsta graden av Parallel-konfiguration. Den här inställningen kallas MAXDOP, som gör att du kan konfigurera det maximala antalet processorer SQL Server kan använda vid parallell bearbetning. Du kan konfigurera MAXDOP för enskilda frågor eller index åtgärder. Detta är fördelaktigt när du vill balansera resurser i systemet för ett kritiskt prestanda program.

Anta till exempel att ditt program använder SQL Server köra en stor fråga och en index åtgärd på samma tid. Låt oss anta att du vill att index åtgärden ska vara mer utförd jämfört med den stora frågan. I sådana fall kan du ange MAXDOP-värdet för index åtgärden som är högre än MAXDOP-värdet för frågan. På så sätt har SQL Server fler processorer som kan utnyttjas för index åtgärden jämfört med antalet processorer som det kan tilldela till den stora frågan. Kom ihåg att du inte styr antalet trådar som SQL Server används för varje åtgärd. Du kan kontrol lera hur många processorer som är dedikerade för flera trådar.

Lär dig mer om [grader av parallellitet](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) i SQL Server. Ta reda på sådana inställningar som påverkar flera trådar i ditt program och deras konfigurationer för att optimera prestanda.

## <a name="queue-depth"></a>Ködjup

Ködjup eller köns längd eller kös Tor lek är antalet väntande IO-begäranden i systemet. Värdet för Queue-djupet avgör hur många IO-åtgärder som programmet kan hantera, vilket innebär att lagrings diskarna bearbetas. Det påverkar alla de tre program prestanda indikatorer som vi beskrivit i den här artikeln, nämligen, IOPS, data flöde och svars tid.

Ködjup och multi-threading är nära relaterade. Värdet för ködjup anger hur mycket flera trådar som kan uppnås av programmet. Om ködjup är stort kan programmet köra fler åtgärder samtidigt, med andra ord, mer multi-threading. Om ködjup är litet, även om programmet är flertrådadt, så har det inte tillräckligt många begär Anden för samtidig körning.

Du kan vanligt vis inte ändra ködjup på grund av hylletiketter, eftersom om inställningen är felaktig blir det svårare än så. Programmet kommer att ange rätt värde för ködjup för att få optimala prestanda. Det är dock viktigt att förstå det här konceptet så att du kan felsöka prestanda problem med programmet. Du kan också titta på effekterna av ködjup genom att köra benchmarking-verktyg på systemet.

Vissa program tillhandahåller inställningar för att påverka köns djupet. Till exempel inställningen MAXDOP (maximal grad av parallellitet) i SQL Server förklaras i föregående avsnitt. MAXDOP är ett sätt att påverka ködjup och multi-threading, även om det inte direkt ändrar värdet för ködjup för SQL Server.

*Högt ködjup*  
Ett högt ködjup raderar fler åtgärder på disken. Disken känner igen nästa förfrågan i kön i förväg. Det innebär att disken kan schemalägga åtgärder i förväg och bearbeta dem i en optimal sekvens. Eftersom programmet skickar fler begär anden till disken kan disken bearbeta mer Parallel IOs. Slutligen kommer programmet att kunna uppnå högre IOPS. Eftersom programmet bearbetar fler förfrågningar ökar även det totala data flödet för programmet.

Ett program kan normalt uppnå maximalt data flöde med 8-16 + utestående IOs per ansluten disk. Om ett ködjup är ett, skickar programmet inte tillräckligt med IOs till systemet, och det bearbetar mindre mängd under en viss period. Med andra ord, mindre data flöde.

Till exempel, i SQL Server, ställer in MAXDOP-värdet för en fråga på "4" informerar SQL Server att den kan använda upp till fyra kärnor för att köra frågan. SQL Server avgör vad som är det bästa värdet för ködjup och antalet kärnor för frågekörningen.

*Optimalt ködjup*  
Mycket högt värde för ködjup har också sina nack delar. Om värdet för ködjup är för högt försöker programmet att köra mycket höga IOPS. Om inte programmet har permanenta diskar med tillräckligt etablerade IOPS kan detta påverka program fördröjningar negativt. Följande formel visar relationen mellan IOPS, svars tid och ködjup.  
    ![Ett diagram som visar formeln I O P S gånger svars tiden är lika med ködjup.](media/premium-storage-performance/image6.png)

Du bör inte konfigurera ködjup till något högt värde, men till ett optimalt värde som kan leverera tillräckligt med IOPS för programmet utan att påverka fördröjning. Om programmets fördröjning till exempel måste vara 1 millisekund måste det ködjup som krävs för att uppnå 5 000 IOPS vara KÖDJUP = 5000 x 0,001 = 5.

*Ködjup för stripe-volym*  
För en stripe-volym upprätthåller du ett högt tillräckligt ködjup så att varje disk har ett djup ködjup individuellt. Överväg till exempel ett program som pushar ett ködjup på 2 och det finns fyra diskar i stripen. De två IO-begärandena skickas till två diskar och återstående två diskar är inaktiva. Konfigurera därför köns djupet så att alla diskar kan vara upptagna. I formeln nedan visas hur du fastställer ködjup för stripe-volymer.  
    ![Ett diagram som visar ekvationen Q D per disk gånger antalet kolumner per volym motsvarar Q D för stripe-volymer.](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Begränsning

Azure Premium Storage etablerar det angivna antalet IOPS och data flöden beroende på vilka VM-storlekar och disk storlekar du väljer. När ditt program försöker köra IOPS eller data flödet ovanför de här gränserna för vad den virtuella datorn eller disken kan hantera, kommer Premium Storage att reglera den. Detta manifest i form av försämrade prestanda i ditt program. Detta kan betyda högre latens, lägre data flöde eller lägre IOPS. Om Premium Storage inte begränsas kan programmet bli helt misslyckat genom att överskrida vad dess resurser kan uppnå. För att undvika prestanda problem på grund av begränsning, ska du alltid tillhandahålla tillräckligt med resurser för ditt program. Ta hänsyn till vad vi har diskuterat i avsnitten VM-storlekar och disk storlekar ovan. Benchmarking är det bästa sättet att ta reda på vilka resurser du behöver för att vara värd för ditt program.

## <a name="next-steps"></a>Nästa steg

Om du vill mäta din disk kan du läsa våra artiklar om att mäta en disk:

* För Linux: [benchmarka ditt program på Azure-disklagring](linux/disks-benchmarks.md)
* För Windows: [benchmarking a disk](windows/disks-benchmarks.md).

Läs mer om tillgängliga disk typer:

* För Linux: [Välj en disktyp](disks-types.md)
* För Windows: [Välj en disktyp](disks-types.md)

För SQL Server användare läser du artiklar om bästa metoder för prestanda för SQL Server:

* [Metod tips för prestanda för SQL Server i Azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)