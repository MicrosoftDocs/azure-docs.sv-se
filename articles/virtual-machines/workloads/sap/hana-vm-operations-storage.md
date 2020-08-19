---
title: SAP HANA lagrings konfiguration för virtuella Azure-datorer | Microsoft Docs
description: Lagrings rekommendationer för virtuell dator som har SAP HANA distribuerats i dem.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e1b510ed970b253adedef0fb6efb4abe0c3b65b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506404"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA

Azure tillhandahåller olika typer av lagring som lämpar sig för virtuella Azure-datorer som kör SAP HANA. **SAP HANA certifierade Azure Storage-typer** som kan övervägas för SAP HANA distributions listor som: 

- Azure Premium SSD eller Premium-lagring 
- [Ultradisk](../../linux/disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Information om de här disk typerna finns i artikeln [Azure Storage typer för SAP-arbetsbelastningar](./planning-guide-storage.md) och [Välj en disktyp](../../linux/disks-types.md)

Azure erbjuder två distributions metoder för virtuella hård diskar på Azure standard och Premium Storage. Vi räknar med att dra nytta av [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/) för Azure block Storage-distributioner. 

En lista över lagrings typer och deras service avtal i IOPS och lagrings data flöde finns i [Azure-dokumentationen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Oberoende av vilken typ av Azure-lagring som valts måste det fil system som används på lagrings platsen stödjas av SAP för det angivna operativ systemet och DBMS. [SAP support note #405827](https://launchpad.support.sap.com/#/notes/405827) listar de fil system som stöds för olika operativ system och databaser, inklusive SAP HANA. Detta gäller för alla volymer SAP HANA kan komma åt för att läsa och skriva för vilken aktivitet som än är. I synnerhet med NFS på Azure för SAP HANA, gäller ytterligare begränsningar för NFS-versioner som anges längre fram i den här artikeln 


Lägsta SAP HANA certifierade villkor för olika lagrings typer är: 

- Azure Premium Storage – **/Hana/log** krävs för att Azure [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md)ska kunna användas. **/Hana/data** -volymen kan placeras på Premium Storage utan Azure Skrivningsaccelerator eller på Ultra disk
- Azure Ultra disk minst för **/Hana/log** -volymen. **/Hana/data** -volymen kan placeras på antingen Premium Storage utan Azure Skrivningsaccelerator eller för att få snabbare omstart Ultra disk
- **NFS v 4.1** -volymer ovanpå Azure NetApp Files för **/Hana/log och/Hana/data**. Volymen av/Hana/Shared kan använda NFS v3-eller NFS v 4.1-protokollet

Några av lagrings typerna kan kombineras. Det är till exempel möjligt att placera **/Hana/data** till Premium Storage och **/Hana/log** kan placeras på Ultra disk Storage för att få en nödvändig låg latens. Om du använder en volym som baseras på ANF för **/Hana/data**måste  **/Hana/log** -volymen baseras på NFS även ovanpå ANF. Att använda NFS ovanpå ANF för en av volymerna (t. ex./Hana/data) och Azure Premium-lagring eller Ultra disk för den andra volymen (t. ex. **/Hana/log**) **stöds inte**.

I den lokala världen var du sällan medveten om I/O-undersystemen och dess funktioner. Orsaken var att den nödvändiga enhets leverantören för att se till att minimi kraven för lagring är uppfyllda för SAP HANA. När du skapar Azure-infrastrukturen själv bör du vara medveten om några av dessa SAP-utfärdade krav. Några av de minsta data flödes egenskaper som SAP rekommenderar är:

- Läs-och skriv åtgärder på **/Hana/log** på 250 MB/SEK med 1 MB i/O-storlekar
- Läs aktivitet minst 400 MB/SEK för **/Hana/data** för 16 mb och 64 MB i/O-storlekar
- Skriv aktivitet med minst 250 MB/SEK för **/Hana/data** med 16 mb och 64 MB i/O-storlekar

Med tanke på att låg latens svars tider är avgörande för DBMS-system, även om DBMS, som SAP HANA, behåller data i minnet. Den kritiska sökvägen i lagrings utrymmet ligger vanligt vis runt transaktions logg skrivningar i DBMS-systemen. Men även åtgärder som att skriva lagrings punkter eller läsa in data i minnet efter krasch återställning kan vara avgörande. Därför är det **obligatoriskt** att utnyttja Azure Premium Storage, Ultra disk eller ANF för **/Hana/data** -och **/Hana/log** -volymer. 


Vissa GUID-principer vid val av lagrings konfiguration för HANA kan visas som:

- Bestäm vilken typ av lagring som baseras på [Azure Storage typer för SAP-arbetsbelastningar](./planning-guide-storage.md) och [Välj en disk typ](../../linux/disks-types.md)
- Det totala antalet i/O-dataflöde och IOPS-gränser i åtanke när du ändrar storlek på eller bestämmer dig för en virtuell dator. Det totala data flödet för VM-lagring dokumenteras i artikel [minnet optimerade storlekar för virtuella datorer](../../sizes-memory.md)
- När du bestämmer dig för lagrings konfigurationen kan du försöka stanna under det totala data flödet för den virtuella datorn med din **/Hana/data** volym konfiguration. När du skriver lagrings punkter kan SAP HANA vara aggressivt utfärdande I/o. Det går enkelt att skjuta upp till data flödes gränserna för din **/Hana/data** -volym när du skriver en lagrings punkt. Om diskarna som bygger **/Hana/data** -volymen har ett högre data flöde än vad den virtuella datorn tillåter kan du köra i situationer där data flöde som används av lagrings utrymmes skrivningen stör data flödes kraven för Skriv om-logg skrivningar. En situation som kan påverka programmets data flöde
- Om du använder Azure Premium Storage är den billigaste konfigurationen att använda logiska volym hanterare för att bygga stripe-uppsättningar för att bygga **/Hana/data** -och **/Hana/log** -volymer

> [!IMPORTANT]
> Förslagen för lagrings konfigurationerna är avsedda som instruktioner för att börja med. Genom att köra arbets belastning och analysera lagrings användnings mönster kan du vara medveten om att du inte använder all lagrings bandbredd eller IOPS som tillhandahålls. Du kan överväga downsizing på lagring och sedan. I motsats kan din arbets belastning behöva mer lagrings data flöde än det som föreslås med dessa konfigurationer. Därför kan du behöva distribuera mer kapacitet, IOPS eller data flöde. I fältet för en spänning mellan lagrings kapacitet som krävs, lagrings fördröjning krävs, lagrings data flöde och IOPS som krävs och minst dyrbar konfiguration, erbjuder Azure tillräckligt med olika lagrings typer med olika funktioner och olika pris punkter för att hitta och anpassa till rätt kompromisser för dig och din HANA-arbetsbelastning.

## <a name="linux-io-scheduler-mode"></a>Läge för I/O-Schemaläggaren I Linux
Linux har flera olika I/O-schemaläggnings lägen. Vanliga rekommendationer via Linux-leverantörer och SAP är att konfigurera om I/O Scheduler-läget för disk volymer från **MQ-** eller **Kyber** -läget till **Noop** (inte multiqueue) eller **ingen** för läget (multiqueue). Information finns i [SAP obs #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösningar med Premium Storage och Azure Skrivningsaccelerator för Azure M-seriens virtuella datorer
Azure Skrivningsaccelerator är en funktion som endast är tillgänglig för virtuella datorer i Azure M-serien. Som namn tillstånd är syftet med funktionen att förbättra I/O-svars tiden för skrivningar mot Azure Premium-lagringen. För SAP HANA ska Skrivningsaccelerator endast användas mot **/Hana/log** -volymen. Därför är **/Hana/data** och **/Hana/log** separata volymer med Azure Skrivningsaccelerator som bara stöder **/Hana/log** -volymen. 

> [!IMPORTANT]
> När du använder Azure Premium Storage är användningen av Azure- [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) för **/Hana/log** -volymen obligatorisk. Skrivningsaccelerator är endast tillgängligt för Premium Storage och virtuella datorer i M-serien och Mv2-serien. Skrivningsaccelerator fungerar inte tillsammans med andra Azure VM-familjer, t. ex. Esv3 eller Edsv4.

Rekommendationerna för cachelagring för Azure Premium-diskar nedan förutsätter I/O-egenskaperna för SAP HANA listan, t. ex.:

- Det finns ingen eventuell Läs arbets belastning mot HANA-datafilerna. Undantag är stora I/o efter omstart av HANA-instansen eller när data har lästs in i HANA. Ett annat fall av större Läs-I/o mot datafiler kan vara HANA-databas säkerhets kopieringar. Som ett resultat för cachelagring av utdata är det oftast inte bra eftersom i de flesta fall måste alla data fil volymer läsas fullständigt. 
- Det går att skriva till datafilerna i burst utifrån HANA-lagrings punkter och HANA-krasch återställning. Skrivning av lagrings punkter är asynkront och innehåller inga användar transaktioner. Att skriva data under krasch återställning är prestanda kritiska för att få systemet att svara snabbt och enkelt. Krasch återställning bör dock vara i stället för exceptionella situationer
- Det finns inga läsningar från HANA gör om-filerna. Undantag är stora I/o när du utför säkerhets kopieringar av transaktions loggar, krasch återställning eller i start fasen för en HANA-instans.  
- Huvud belastningen mot den SAP HANA göra om-logg filen skrivs. Beroende på arbets Belastningens natur kan du ha I/o-typ så litet som 4 KB eller i andra fall i/O-storlekar på 1 MB eller mer. Skriv fördröjning mot SAP HANA gör om loggen prestanda kritisk.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

**Rekommendation: som ett resultat av dessa observerade I/O-mönster genom SAP HANA ska cachelagring för de olika volymerna med Azure Premium Storage ställas in som:**

- **/Hana/data** – ingen cachelagring eller cachelagring av läsning
- **/Hana/log** – ingen cachelagring – undantag för M-och Mv2-serien där Skrivningsaccelerator ska aktive ras utan cachelagring av läsning. 
- **/Hana/Shared** – cachelagring för läsning
- **OS-disk** – ändra inte standardvärdet för cachelagring som anges av Azure när den virtuella datorn skapades


Om du använder LVM eller mdadm för att skapa stripe-uppsättningar över flera Azure Premium-diskar måste du definiera stripe-storlekar. Dessa storlekar skiljer sig mellan **/Hana/data** och **/Hana/log**. **Rekommendation: som en rand storlek rekommenderar vi att du använder:**

- 256 KB för **/Hana/data**
- 64 KB för **/Hana/log**

> [!NOTE]
> Stripe-storleken för **/Hana/data** har ändrats från tidigare rekommendationer som anropar för 64 kb eller 128 kb till 256 KB baserat på kund upplevelser med nyare Linux-versioner. Storleken på 256 KB ger något bättre prestanda. Vi har också ändrat rekommendationen för stripe-storlekar på **/Hana/log** från 32 kb till 64 KB för att få tillräckligt med data flöde med större I/O-storlekar.

> [!NOTE]
> Du behöver inte konfigurera någon redundans nivå med RAID-volymer eftersom Azure block Storage behåller tre avbildningar av en virtuell hård disk. Användningen av en stripe-uppsättning med Azure Premium-diskar är enbart att konfigurera volymer som ger tillräcklig IOPS och/eller I/O-genomflöde.

Ackumulerar ett antal Azure-VHD: er under en stripe-uppsättning, är accumulative från en IOPS-och Storage-dataflödes sida. Så om du lägger till en stripe-uppsättning över över 3 x P30 Azure Premium Storage-diskar, bör den ge dig tre gånger IOPS och tre gånger lagrings data flödet för en enskild Azure Premium Storage P30-disk.

> [!IMPORTANT]
> Om du använder LVM eller mdadm som volym hanterare för att skapa stripe-uppsättningar över flera Azure Premium-diskar, får inte de tre SAP HANA fil grupperna/data,/log och/Shared placeras i en standard-eller rot volym grupp. Vi rekommenderar starkt att du följer rikt linjerna för Linux-leverantörer som vanligt vis skapar enskilda volym grupper för/data,/log och/Shared.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-funktioner för Premium Storage
För Azure Premium Storage-diskar som är mindre än eller lika med 512 GiB erbjuds burst-funktionerna. Exakt hur disk-burst fungerar beskrivs i artikeln [disk bursting](../../linux/disk-bursting.md). När du läser artikeln förstår du konceptet med att Periodisera IOPS och data flöde när din I/O-arbetsbelastning är lägre än den nominella IOPS och data flödet för diskarna (mer information om det nominella data flödet finns i [priser för hanterad disk](https://azure.microsoft.com/pricing/details/managed-disks/)). Du kommer att Periodisera delta i IOPS och data flöde mellan den aktuella användningen och de nominella värdena för disken. Burst-överföringarna är begränsade till högst 30 minuter.

De idealiska fall där denna Burst-funktion kan planeras i kommer förmodligen att vara de volymer eller diskar som innehåller datafiler för olika DBMS. I/O-arbetsbelastningen förväntas mot dessa volymer, särskilt med små till mellan intervall system förväntas se ut så här:

- Låg till måttlig Läs belastnings belastning eftersom data som helst cachelagras i minnet eller som i händelse av HANA bör vara helt i minnet
- Burst-överföring som utlösts av databas kontroll punkter eller lagrings punkter som utfärdas med jämna mellanrum
- Säkerhets kopierings arbets belastning som läser i en kontinuerlig ström i fall där säkerhets kopior inte körs via lagrings ögonblicks bilder
- För SAP HANA, Läs in data i minnet efter en instans omstart

Särskilt i mindre DBMS-system där din arbets belastning hanterar några hundra transaktioner per sekund, kan en sådan Burst-funktion även vara användbar för de diskar eller volymer som lagrar transaktionen eller gör om loggen. Förväntad arbets belastning mot sådan disk eller volymerna ser ut så här:

- Vanliga skrivningar till den disk som är beroende av arbets belastningen och typen av arbets belastning eftersom varje incheckning som utfärdats av programmet sannolikt utlöser en I/O-åtgärd
- Högre arbets belastning i data flödet för fall av operativa uppgifter som att skapa eller återskapa index
- Läs burst-överföring när du utför transaktions logg eller gör om logg säkerhets kopior


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Rekommenderad lagrings lösning för produktion baserat på Azure Premium Storage

> [!IMPORTANT]
> SAP HANA-certifiering för Azure M-seriens virtuella datorer är exklusivt med Azure Skrivningsaccelerator för **/Hana/log** -volymen. Därför förväntas produktions scenariot SAP HANA distributioner på virtuella datorer i Azure M-serien att konfigureras med Azure Skrivningsaccelerator för **/Hana/log** -volymen.  

> [!NOTE]
> I scenarier som omfattar Azure Premium Storage implementerar vi burst-funktioner i konfigurationen. När du använder lagrings test verktyg för vilken form eller vilket formulär som används, bör du tänka på hur [disk burst i Azure Premium fungerar](../../linux/disk-bursting.md) . Genom att köra de lagrings test som levereras via verktyget SAP HWCCT eller HCMT, förväntar vi dig inte att alla tester ska klara villkoren, eftersom vissa av testerna kommer att överskrida de burst-krediter som du kan samla. Särskilt när alla tester körs sekventiellt utan avbrott.


> [!NOTE]
> För produktions scenarier kontrollerar du om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Rekommendation: de rekommenderade konfigurationerna med Azure Premium Storage för produktions scenarier ser ut så här:**

Konfiguration för SAP **/Hana/data** -volym:

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /hana/data | Maximalt burst-genomflöde | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 Mbit/s | 4 x P6 | 680 Mbit/s | 960 | 14 000 |
| M32ls | 256 GiB | 500 Mbit/s | 4 x P6 | 680 Mbit/s | 960 | 14 000 |
| M64ls | 512 GiB | 1 000 Mbit/s | 4 x P10 |  680 Mbit/s | 2 000 | 14 000 |
| M64s | 1 000 GiB | 1 000 Mbit/s | 4 x p15 | 680 Mbit/s | 4 400 | 14 000 |
| M64ms | 1 750 GiB | 1 000 Mbit/s | 4 x P20 | 680 Mbit/s | 9 200 | 14 000 |  
| M128s | 2 000 GiB | 2 000 Mbit/s | 4 x P20 | 680 Mbit/s | 9 200| 14 000 | 
| M128ms | 3 800 GiB | 2 000 Mbit/s | 4 x P30 | 800 Mbit/s (etablerad) | 20 000 | ingen burst | 
| M208s_v2 | 2 850 GiB | 1 000 Mbit/s | 4 x P30 | 800 Mbit/s (etablerad) | 20 000| ingen burst | 
| M208ms_v2 | 5 700 GiB | 1 000 Mbit/s | 4 x P40 | 1 000 Mbit/s (etablerad) | 25,000 | ingen burst |
| M416s_v2 | 5 700 GiB | 2 000 Mbit/s | 4 x P40 | 1 000 Mbit/s (etablerad) | 25,000 | ingen burst |
| M416ms_v2 | 11 400 GiB | 2 000 Mbit/s | 4 x P50 | 2 000 Mbit/s (etablerad) | 25,000 | ingen burst |


För **/Hana/log** -volymen. konfigurationen skulle se ut så här:

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | **/Hana/log** volym | Maximalt burst-genomflöde | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 Mbit/s | 3 x P10 | 510 Mbit/s | 1500 | 10 500 | 
| M32ls | 256 GiB | 500 Mbit/s | 3 x P10 | 510 Mbit/s | 1500 | 10 500 | 
| M64ls | 512 GiB | 1 000 Mbit/s | 3 x P10 | 510 Mbit/s | 1500 | 10 500 | 
| M64s | 1 000 GiB | 1 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 | 
| M64ms | 1 750 GiB | 1 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 |  
| M128s | 2 000 GiB | 2 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500|  
| M128ms | 3 800 GiB | 2 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 | 
| M208s_v2 | 2 850 GiB | 1 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 |  
| M208ms_v2 | 5 700 GiB | 1 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 |  
| M416s_v2 | 5 700 GiB | 2 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 |  
| M416ms_v2 | 11 400 GiB | 2 000 Mbit/s | 3 x p15 | 510 Mbit/s | 3 300 | 10 500 | 


För de andra volymerna skulle konfigurationen se ut så här:

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /hana/shared | /root-volym | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 Mbit/s | 1 x P20 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 Mbit/s |  1 x P20 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 Mbit/s | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1 000 GiB | 1 000 Mbit/s | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1 750 GiB | 1 000 Mbit/s | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2 000 GiB | 2 000 Mbit/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3 800 GiB | 2 000 Mbit/s | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2 850 GiB | 1 000 Mbit/s |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5 700 GiB | 1 000 Mbit/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5 700 GiB | 2 000 Mbit/s |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11 400 GiB | 2 000 Mbit/s | 1 x P30 | 1 x P10 | 1 x P6 | 


Kontrol lera om lagrings data flödet för de olika föreslagna volymerna uppfyller den arbets belastning som du vill köra. Om arbets belastningen kräver högre volymer för **/Hana/data** och **/Hana/log**måste du öka antalet virtuella hård diskar för Azure Premium Storage. Att ändra storlek på en volym med fler virtuella hård diskar än vad som anges i listan ökar IOPS-och I/O-genomflödet inom gränserna för den virtuella Azure-dator typen.

Azure-Skrivningsaccelerator fungerar bara tillsammans med [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör **/Hana/log** -volymen måste distribueras som hanterade diskar. Mer detaljerade instruktioner och begränsningar för Azure Skrivningsaccelerator finns i artikeln [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md).

För de HANA-certifierade virtuella datorerna i Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) -familjen och [EDSV4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)måste du ANF för **/Hana/data** -och **/Hana/log** -volymen. Eller så behöver du utnyttja Azure Ultra disk Storage i stället för Azure Premium Storage för **/Hana/log** -volymen. Därför kan konfigurationerna för **/Hana/data** -volymen på Azure Premium Storage se ut så här:

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /hana/data | Maximalt burst-genomflöde | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 Mbit/s | 3 x P10 | 510 Mbit/s | 1500 | 10 500 |
| E32ds_v4 | 256 GiB | 768 Mbit/s | 3 x P10 |  510 Mbit/s | 1500 | 10 500|
| E48ds_v4 | 384 GiB | 1 152 Mbit/s | 3 x p15 |  510 Mbit/s | 3 300  | 10 500 | 
| E64ds_v4 | 504 GiB | 1 200 Mbit/s | 3 x p15 |  510 Mbit/s | 3 300 | 10 500 | 
| E64s_v3 | 432 GiB | 1 200 MB/s | 3 x p15 |  510 Mbit/s | 3 300 | 10 500 | 

För de andra volymerna, inklusive **/Hana/log** på Ultra disk, kan konfigurationen se ut så här:

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/log volym | /Hana/log I/O-genomflöde | /Hana/log IOPS | /hana/shared | /root-volym | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 Mbit/s | 80 GB | 250 Mbit/s | 1800 | 1 x p15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 Mbit/s | 128 GB | 250 Mbit/s | 1800 | 1 x p15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1 152 Mbit/s | 192 GB | 250 Mbit/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1 200 Mbit/s | 256 GB | 250 Mbit/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1 200 Mbit/s | 220 GB | 250 Mbit/s | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguration av Azure Ultra disk Storage för SAP HANA
En annan Azure Storage-typ kallas [Azure Ultra disk](../../windows/disks-types.md#ultra-disk). Den stora skillnaden mellan Azure Storage och Ultra disk är att disk funktionerna inte är kopplade till disk storleken längre. Som kund kan du definiera dessa funktioner för Ultra disk:

- Storlek på en disk som sträcker sig från 4 GiB till 65 536 GiB
- IOPS sträcker sig från 100 IOPS till 160K IOPS (maximalt beror på VM-typer)
- Lagrings data flöde från 300 MB/SEK till 2 000 MB/SEK

Ultra disk ger dig möjlighet att definiera en enskild disk som uppfyller din storlek, IOPS och disk data flödes intervallet. I stället för att använda logiska volym hanterare som LVM eller MDADM ovanpå Azure Premium Storage för att skapa volymer som uppfyller IOPS-och lagrings data flödes krav. Du kan köra en konfigurations kombination mellan Ultra disk och Premium Storage. Det innebär att du kan begränsa användningen av Ultra disk till de prestanda kritiska **/Hana/data** -och **/Hana/log** -volymerna och de andra volymerna med Azure Premium Storage

Andra fördelar med Ultra disk kan vara den bättre Läs fördröjningen jämfört med Premium Storage. Svars tiden för snabbare läsning kan ha fördelar när du vill minska HANA-starttiden och den efterföljande inläsningen av data i minnet. Fördelarna med Ultra disk Storage kan också vara filtpenna när HANA skriver lagrings punkter. 

> [!NOTE]
> Ultra disk finns ännu inte i alla Azure-regioner och har ännu inte stöd för alla VM-typer som anges nedan. Detaljerad information om hur Ultra disk är tillgängligt och vilka VM-familjer som stöds finns i artikeln [vilka disk typer som är tillgängliga i Azure?](../../windows/disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Rekommenderad lagrings lösning för produktion med ren Ultra disk Configuration
I den här konfigurationen behåller du **/Hana/data** -och **/Hana/log** -volymerna separat. De föreslagna värdena härleds ut från KPI: er som SAP måste certifiera VM-typer för SAP HANA-och lagrings konfigurationer enligt rekommendationerna i [fakta bladet för SAP TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Rekommendationerna överskrider ofta de lägsta kraven för SAP som anges tidigare i den här artikeln. De listade rekommendationerna är en kompromiss mellan storleks rekommendationerna av SAP och det maximala lagrings utrymmet som de olika VM-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data volym | /Hana/data I/O-genomflöde | /Hana/data IOPS | /Hana/log volym | /Hana/log I/O-genomflöde | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 Mbit/s | 2 500 | 80 GB | 250 MB | 1800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 Mbit/s | 2 500 | 128 GB | 250 Mbit/s | 1800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 Mbit/s | 3 000 | 192 GB | 250 Mbit/s | 1800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 Mbit/s | 3 500 |  256 GB | 250 Mbit/s | 1800 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 610 GB | 400 Mbit/s | 3 500 | 220 GB | 250 MB | 1800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 Mbit/s | 2 500 | 96 GB | 250 Mbit/s  | 1800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 Mbit/s | 2 500 | 256 GB | 250 Mbit/s  | 1800 |
| M64ls | 512 GiB | 1 000 MB/s | 620 GB | 400 Mbit/s | 3 500 | 256 GB | 250 Mbit/s  | 1800 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 Mbit/s | 5 000 | 512 GB | 250 Mbit/s  | 2 500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 Mbit/s | 5 000 | 512 GB | 250 Mbit/s  | 2 500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 750 Mbit/s | 7 000 | 512 GB | 250 Mbit/s  | 2 500 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 750 Mbit/s |7 000 | 512 GB | 250 Mbit/s  | 2 500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 750 Mbit/s | 7 000 | 512 GB | 250 Mbit/s  | 2 500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 750 Mbit/s | 7 000 | 512 GB | 250 Mbit/s  | 2 500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 000 Mbit/s | 9,000 | 512 GB | 400 Mbit/s  | 4 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 Mbit/s | 9,000 | 512 GB | 400 Mbit/s  | 4 000 |   

**Värdena i listan är avsedda att vara en start punkt och måste utvärderas mot de verkliga kraven.** Fördelen med Azure Ultra disk är att värdena för IOPS och data flöde kan anpassas utan att du behöver stänga av den virtuella datorn eller stoppa arbets belastningen som tillämpas på systemet.   

> [!NOTE]
> Lagrings ögonblicks bilder med Ultra disk Storage är hittills inte tillgängliga. Detta blockerar användningen av ögonblicks bilder av virtuella datorer med Azure Backup Services


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v 4.1-volymer på Azure NetApp Files
Azure NetApp Files tillhandahåller interna NFS-resurser som kan användas för **/Hana/Shared**-, **/Hana/data**-och **/Hana/log** -volymer. Att använda ANF-baserade NFS-resurser för **/Hana/data** -och **/Hana/log** -volymerna kräver att v 4.1 NFS-protokollet används. NFS-protokollet v3 stöds inte för användning av **/Hana/data** -och **/Hana/log** -volymer när du baserar resurserna på ANF. 

> [!IMPORTANT]
> Det NFS v3-protokoll som implementeras på Azure NetApp Files stöds **inte** för användning för **/Hana/data** och **/Hana/log**. Användningen av NFS 4,1 är obligatorisk för **/Hana/data** -och **/Hana/log** -volymer från en funktionell punkt i vyn. För **/Hana/Shared** -volymen kan NFS v3 eller NFS v 4.1-protokollet användas från en funktionell punkt i vyn.

### <a name="important-considerations"></a>Att tänka på
Tänk på följande viktiga överväganden när du överväger Azure NetApp Files för SAP-NetWeaver och SAP HANA:

- Den minsta kapacitets poolen är 4 TiB.  
- Den minsta volym storleken är 100 GiB
- Azure NetApp Files och alla virtuella datorer, där Azure NetApp Files volymer ska monteras, måste finnas i samma Azure-Virtual Network eller i [peer-anslutna virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät, delegerat till Azure NetApp Files.
- Data flödet för en Azure NetApp-volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven.  
- Azure NetApp Files erbjuder [export princip](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): du kan kontrol lera tillåtna klienter, åtkomst typ (Läs&Skriv, skrivskyddad, osv.). 
- Azure NetApp Files funktionen är inte en zon medveten än. För närvarande är Azure NetApp Files funktionen inte distribuerad i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.  
- Det är viktigt att de virtuella datorerna distribueras i närheten av Azure NetApp-lagringen för låg latens. 
- Användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` på de virtuella datorerna måste matcha konfigurationen i Azure NetApp Files. 

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  

> [!IMPORTANT]
> Om det finns ett matchnings fel mellan användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` mellan den virtuella datorn och Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer, monterade på virtuella datorer, som `nobody` . Se till att ange rätt användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` , när [ett nytt system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ska Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Storlek för HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp-volym är en funktion i volym storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

När du designar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta krav på lagrings data flöde från SAP, som översätts till lägsta data flödes egenskaper för:

- Aktivera Läs-och skriv åtgärder på **/Hana/log** i 250 MB/SEK med 1 MB i/O-storlekar  
- Aktivera Läs aktivitet på minst 400 MB/SEK för **/Hana/data** för 16 mb och 64 MB i/O-storlekar  
- Aktivera Skriv aktivitet med minst 250 MB/SEK för **/Hana/data** med 16 mb och 64 MB i/O-storlekar  

[Azure NetApp Files data flödes gränser](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TIB volym kvot:
- Premium Storage-nivå – 64 MiB/s  
- Ultra Storage-nivå – 128 MiB/s  

> [!IMPORTANT]
> Oberoende av kapaciteten som du distribuerar på en enskild NFS-volym förväntas data flödet till platå i intervallet 1.2 – 1,4 GB/SEK som används av en konsument i en virtuell dator. Detta måste göra med den underliggande arkitekturen i ANF-erbjudandet och relaterade Linux-sessionsgränser runt NFS. Prestanda-och data flödes numren som dokumenteras i artikeln prestandatest [resultat för Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) utfördes mot en delad NFS-volym med flera virtuella klient datorer och på grund av flera sessioner. Det scenariot är annorlunda för det scenario vi mäter i SAP. Där vi mäter data flödet från en enskild virtuell dator mot en NFS-volym. finns på ANF.

För att uppfylla de lägsta data flödes kraven för SAP för data och logg, och enligt rikt linjerna för `/hana/shared` , skulle de rekommenderade storlekarna se ut så här:

| Volym | Storlek<br /> Premium Storage nivå | Storlek<br /> Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) per 4 arbetsnoder | Max (512 GB, 1xRAM) per 4 arbetsnoder | v3 eller v 4.1 |


> [!NOTE]
> De Azure NetApp Files storleks rekommendationer som anges här är riktade till att uppfylla minimi kraven för SAP-Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier, är det inte tillräckligt. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.  

Därför kan du överväga att distribuera liknande data flöde för de ANF-volymer som anges för Ultra disk Storage redan. Tänk också på storlekarna för de storlekar som anges för volymerna för de olika VM SKU: erna som utförda i de Ultra disk-tabellerna redan.

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva `unmount` volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Det gör det möjligt att uppfylla ditt program både förväntat och oförutsedda data flödes krav.

Dokumentation om hur du distribuerar en SAP HANA skalbar konfiguration med en standby-nod med hjälp av NFS v 4.1-volymer som finns i ANF publiceras i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Kostnads medveten lösning med Azure Premium Storage
Den Azure Premium Storage-lösning som beskrivs i det här dokumentet i avsnitts [lösningar med Premium Storage och azure Skrivningsaccelerator för Azure M-seriens virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) avsåg SAP HANA produktions scenarier som stöds. En av egenskaperna för konfigurationer som stöds för produktion är separering av volymer för SAP HANA data och gör om att logga in på två olika volymer. Orsaken till en sådan separation är att arbets Belastningens egenskaper på volymerna skiljer sig åt. Med de föreslagna produktions konfigurationerna kan olika typer av cachelagring eller till och med olika typer av Azure block-lagring vara nödvändiga. Konfigurationerna för produktion som stöds med Azure block Storage Target är kompatibla med det [enda service nivå avtalet för virtuella datorer för azure Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/) också.  För icke-produktions scenarier kan vissa av de överväganden som vidtas för produktions system inte gälla för mer låga system som inte är för produktion. Det leder till att HANA-data och logg volymen kombineras. Även om det i vissa culprits, till exempel inte uppfyller vissa data flöden eller fördröjnings-KPI: er som krävs för produktions system. En annan aspekt för att minska kostnaderna i sådana miljöer kan vara användningen av [Azure standard SSD Storage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage). Det är ett alternativ som gör att du inte validerar [service avtalet för enskild virtuell dator för Azure Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Ett mindre kostsamt alternativ för sådana konfigurationer kan se ut så här:


| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data och/Hana/log<br /> randigt med LVM eller MDADM | /hana/shared | /root-volym | /usr/sap | Comment |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM-typen inte HANA-certifierad <br /> Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 5 000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM-typen inte HANA-certifierad <br /> Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 Mbit/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 5 000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1 152 Mbit/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| E64v3 | 432 GiB | 1 200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Kommer inte att uppnå mindre än 1ms lagrings fördröjning<sup>1</sup> |
| M64ls | 512 GiB | 1 000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 10 000<sup>2</sup> |
| M64s | 1 000 GiB | 1 000 MB/s | 7 x p15 | 1 x E30 | 1 x E6 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 10 000<sup>2</sup> |
| M64ms | 1 750 GiB | 1 000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 10 000<sup>2</sup> |
| M128s | 2 000 GiB | 2 000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 20 000<sup>2</sup> |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 10 000<sup>2</sup> |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 20 000<sup>2</sup> |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 10 000<sup>2</sup> |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 20 000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Om du använder Skrivningsaccelerator för kombinerad data och logg volym begränsas IOPS-priset till 20 000<sup>2</sup> |


<sup>1</sup> [Azure Skrivningsaccelerator](../../how-to-enable-write-accelerator.md) kan inte användas med Ev4 och Ev4 VM-familjer. I och med att använda Azure Premium Storage är I/O-fördröjningen inte mindre än 1ms

<sup>2</sup> VM-serien har stöd för [Azure Skrivningsaccelerator](../../how-to-enable-write-accelerator.md), men det finns en risk att IOPS-gränsen för Write Accelerator kan begränsa disk konfigurationens IOPS-funktioner

Om du kombinerar data-och logg volymen för SAP HANA bör diskarna som skapar stripe-volymen inte ha Read cache eller read/write cache aktiverat.

Det finns VM-typer i listan som inte är certifierade med SAP och som inte visas i den så kallade [SAP HANA maskin varu katalog](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Kundernas feedback var att de icke-listade VM-typerna har använts för vissa icke-produktions uppgifter.


## <a name="next-steps"></a>Nästa steg
Mer information finns i:

- [SAP HANA guide för hög tillgänglighet för virtuella Azure-datorer](./sap-hana-availability-overview.md).
