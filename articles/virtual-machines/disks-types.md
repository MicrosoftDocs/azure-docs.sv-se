---
title: Välj en disktyp för virtuella Azure IaaS-hanterade diskar
description: Lär dig mer om de tillgängliga Azure-disk typerna för virtuella datorer, inklusive Ultra disks, Premium SSD, standard SSD och standard hård diskar.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500775"
---
# <a name="what-disk-types-are-available-in-azure"></a>Vilka disktyper är tillgängliga i Azure?

Azure Managed disks erbjuder för närvarande fyra disk typer, och varje typ är riktad mot specifika kund scenarier.

## <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av Ultra disks, Premium solid-state-hårddiskar (SSD), standard SSD och standard hård diskar (HDD) för hanterade diskar för att hjälpa dig att avgöra vad som ska användas.

| Detalj | Ultradisk | Premium SSD | Standard SSD | Standard HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Disktyp   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensiva arbets belastningar, till exempel [SAP HANA](workloads/sap/hana-vm-operations-storage.md), toppnivå databaser (till exempel SQL, Oracle) och andra transaktions krävande arbets belastningar.   |Produktion och prestandakänsliga arbetsbelastningar   |Webbservrar, företagsprogram med lätt användning och Dev/Test   |Säkerhetskopiering, icke-kritisk, lågfrekvent åtkomst   |
|Maximal diskstorlek   |65 536 gibibyte (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximalt dataflöde   |2 000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Maximalt IOPS   |160 000    |20 000   |6 000   |2 000   |

## <a name="ultra-disk"></a>Ultradisk

Azures ultradiskar tillhandahåller disklagring med ett snabbt dataflöde, hög IOPS och konsekvent snabba svarstider för virtuella Azure IaaS-datorer. Vissa ytterligare fördelar med Ultra disks är möjligheten att dynamiskt ändra diskens prestanda, tillsammans med dina arbets belastningar, utan att behöva starta om dina virtuella datorer (VM). Ultradiskar är lämpliga för dataintensiva arbetsbelastningar som SAP HANA, databaser på toppnivå och transaktionskrävande arbetsbelastningar. Ultradiskar kan endast användas som datadiskar. Vi rekommenderar att du använder Premium-SSD-diskar som operativsystemsdiskar.

### <a name="performance"></a>Prestanda

När du etablerar en Ultra disk kan du oberoende konfigurera kapaciteten och diskens prestanda. Ultra disks har flera fasta storlekar, från 4 GiB upp till 64 TiB och en flexibel prestanda konfigurations modell som gör att du kan konfigurera IOPS och data flöde oberoende av varandra.

Några viktiga funktioner i Ultra disks:

- Disk kapacitet: Ultra disks-kapacitet sträcker sig från 4 GiB upp till 64 TiB.
- Disk IOPS: Ultra disks stöder IOPS-gränser på 300 IOPS/GiB, upp till högst 160 K IOPS per disk. Säkerställ att den valda disk-IOPS är mindre än den virtuella datorns IOPS-gräns för att uppnå den IOPS som du har allokerat. Lägsta garanterade IOPS per disk är 2 IOPS/GiB, med en total bas linje som är minst 100 IOPS. Om du till exempel har en 4 GiB Ultra-disk får du minst 100 IOPS i stället för åtta IOPS.
- Disk data flöde: med Ultra disks är data flödes gränsen på en enskild disk 256 KiB/s för varje etablerad IOPS, upp till högst 2000 MBps per disk (där Mbit/s = 10 ^ 6 byte per sekund). Lägsta garanterade data flöde per disk är 4KiB/s för varje etablerad IOPS med en total bas linje som är minst 1 Mbit/s.
- Ultra disks stöder justering av attribut för disk prestanda (IOPS och data flöde) vid körning utan att disken kopplas bort från den virtuella datorn. När en disk prestanda har ändrats på en disk kan det ta upp till en timme innan ändringen träder i kraft. Det finns en gräns för fyra åtgärder för storleks ändring under en 24-timmarsperiod. Det går att ändra storlek på en prestanda för att kunna köras på grund av brist på prestanda bandbredds kapacitet.

### <a name="disk-size"></a>Diskstorlek

|Disk storlek (GiB)  |IOPS-hölje  |Data flödes gräns (Mbit/s)  |
|---------|---------|---------|
|4     |1 200         |300         |
|8     |2 400         |600         |
|16     |4 800         |1 200         |
|32     |9 600         |2 000         |
|64     |19 200         |2 000         |
|128     |38 400         |2 000         |
|256     |76 800         |2 000         |
|512     |153 600         |2 000         |
|1 024-65536 (storlekar i det här intervallet ökar i steg om 1 TiB)     |160 000         |2 000         |

Ultra disks har utformats för att ge under millisekunder-fördröjningar och mål-IOPS och data flöde som beskrivs i föregående tabell 99,99% av tiden.

### <a name="ga-scope-and-limitations"></a>Allmän omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Om du vill börja använda Ultra disks, se vår artikel på ämnet: [använda Azure Ultra disks](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD levererar hög prestanda och disk support med låg latens för virtuella datorer med indata/utdata (i/o)-intensiva arbets belastningar. Om du vill dra nytta av hastighet och prestanda för Premium Storage-diskar kan du migrera befintliga VM-diskar till Premium-SSD. Premium-SSD är lämpliga för verksamhets kritiska produktions program. Premium-SSD kan bara användas med VM-serien som är Premium Storage-kompatibla.

Mer information om enskilda VM-typer och storlekar i Azure för Windows eller Linux, inklusive vilka storlekar som är kompatibla med Premium Storage, finns i [storlekar för virtuella datorer i Azure](sizes.md). I den här artikeln måste du kontrol lera varje enskild artikel för VM-storlek för att avgöra om den är Premium Storage-kompatibel.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

När du etablerar en Premium Storage-disk, till skillnad från standard lagring, garanterar du att disken har kapacitet, IOPS och data flöde. Om du till exempel skapar en P50-disk, etablerar Azure 4 095-GB lagrings kapacitet, 7 500 IOPS och 250 MB/s genom strömning för disken. Programmet kan använda hela eller delar av kapaciteten och prestandan. Premium SSD diskar har utformats för att tillhandahålla låg latens i millisekunder och mål-IOPS och data flöde som beskrivs i föregående tabell 99,9% av tiden.

## <a name="bursting"></a>Bursting "

Premium SSD storlekar som är mindre än P30 nu erbjuder disk burst-överföring och kan överföra deras IOPS per disk upp till 3 500 och bandbredden upp till 170 MB/s. Burst-överföring automatiseras och fungerar baserat på ett kredit system. Krediter samlas in automatiskt i en burst-Bucket när disk trafiken är lägre än det tillhandahållna prestanda målet och krediterna förbrukas automatiskt när trafiken överförs bortom målet, upp till den maximala burst-gränsen. Max burst-gränsen definierar taket för disk-IOPS & bandbredd även om du har burst-krediter att använda. Disk burst ger bättre tolerans på oförutsägbara ändringar av IO-mönster. Du kan använda den bäst för start och program för operativ system diskar med hög belastnings trafik.    

Disk burst-stöd kommer att aktive ras vid nya distributioner av tillämpliga disk storlekar som standard, utan någon användar åtgärd krävs. För befintliga diskar av tillämpliga storlekar kan du aktivera burst-överföring med något av följande två alternativ: koppla från och återanslut disken eller stoppa och starta om den anslutna virtuella datorn. Alla användbara disk storlekar börjar med en fullständig överförings kredit Bucket när disken är ansluten till en virtuell dator som har stöd för maximal varaktighet vid högsta burst-gräns på 30 minuter. Mer information om hur du använder burst-överföring på Azure-diskar finns [Premium SSD bursting](./disk-bursting.md). 

### <a name="transactions"></a>Transaktioner

För Premium-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB.

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD är ett kostnads effektivt lagrings alternativ som är optimerat för arbets belastningar som behöver konsekvent prestanda på lägre IOPS-nivåer. Standard SSD erbjuder en bra ingångs nivå för de som vill flytta till molnet, särskilt om du får problem med var Ian sen för arbets belastningar som körs på dina hård disk lösningar lokalt. Jämfört med standard hård diskar ger standard SSD bättre tillgänglighet, konsekvens, tillförlitlighet och latens. Standard SSD är lämpliga för webb servrar, låga IOPS-programservrar, lätt använda företags program och arbets belastningar för utveckling/testning. Som standard hård diskar är standard-SSD tillgängliga på alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard SSD är utformad för att tillhandahålla en ensiffrig millisekunds fördröjning och IOPS och data flöde upp till de gränser som beskrivs i föregående tabell 99% av tiden. Faktisk IOPS och data flöde kan variera ibland beroende på trafik mönstren. Standard SSD ger mer konsekvent prestanda än hård diskar med lägre latens.

### <a name="transactions"></a>Transaktioner

För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. Dessa transaktioner har en fakturerings påverkan.

## <a name="standard-hdd"></a>Standard HDD

Azure standard-hårddiskar levererar tillförlitligt stöd för diskar med låg kostnad för virtuella datorer som kör svars känsliga arbets belastningar. Med standard lagring lagras data på hård diskar (HDD). Svars tid, IOPS och data flöde för Standard HDD diskar kan variera mycket mer jämfört med SSD-baserade diskar. Standard HDD diskar har utformats för att leverera Skriv fördröjningar under 10ms och Läs fördröjning under 20ms för de flesta i/o-åtgärder, men den faktiska prestandan kan variera beroende på i/o-storlek och arbets belastnings mönster. När du arbetar med virtuella datorer kan du använda standard diskar för hård diskar för utvecklings-och test scenarier och mindre kritiska arbets belastningar. Standard hård diskar är tillgängliga i alla Azure-regioner och kan användas med alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transaktioner

För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek. Dessa transaktioner har en fakturerings påverkan.

## <a name="billing"></a>Fakturering

När du använder hanterade diskar gäller följande saker:

- Disktyp
- hanterad disk storlek
- Ögonblicksbilder
- Utgående data överföringar
- Antal transaktioner

**Hanterad disk storlek**: hanterade diskar debiteras enligt den etablerade storleken. Azure mappar den etablerade storleken (avrundat uppåt) till den närmaste erbjudna disk storleken. Mer information om de disk storlekar som erbjuds finns i föregående tabeller. Varje disk mappar till ett erbjudande för allokerad disk storlek och debiteras enligt detta. Om du till exempel har upprättat en 200 GiB Standard SSD, mappas den till disk storleks erbjudandet för E15 (256 GiB). Faktureringen för en etablerad disk beräknas per timme genom att använda det månatliga priset för lagrings erbjudandet. Om du till exempel har upprättat en E10-disk och tagit bort den efter 20 timmar debiteras du för E10-erbjudandet som har klassificerats till 20 timmar. Detta gäller oavsett hur mycket faktisk data som skrivs till disken.

**Ögonblicks bilder**: ögonblicks bilder faktureras baserat på storleken som används. Om du till exempel skapar en ögonblicks bild av en hanterad disk med en etablerad kapacitet på 64 GiB och den faktiska använda data storleken på 10 GiB faktureras ögonblicks bilden endast för den använda data storleken på 10 GiB.

Mer information om ögonblicks bilder finns i avsnittet om ögonblicks bilder i [översikten över hanterade diskar](managed-disks-overview.md).

**Utgående data överföringar**: [utgående data överföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures Data Center) debiteras för bandbredds användning.

**Transaktioner**: du debiteras för antalet transaktioner som du utför på en standard-hanterad disk. För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek.

Detaljerad information om priser för Managed Disks, inklusive transaktionskostnader, finns [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Avgift för VM-reservation på Ultra disk

Virtuella Azure-datorer har möjlighet att ange om de är kompatibla med Ultra disks. En Ultra disk-kompatibel virtuell dator allokerar dedikerad bandbredds kapacitet mellan den virtuella Compute VM-instansen och block lagrings enheten för att optimera prestandan och minska svars tiden. Genom att lägga till den här funktionen på den virtuella datorn får du en reservations avgift som bara införs om du har aktiverat Ultra disk-kapacitet på den virtuella datorn utan att koppla en Ultra disk till den. När en Ultra disk är ansluten till den Ultra disk-kompatibla virtuella datorn gäller inte avgiften. Den här avgiften är per vCPU etablerad på den virtuella datorn. 

> [!Note]
> För [begränsade KÄRN VM-storlekar](constrained-vcpu.md)baseras reservations avgiften på det faktiska antalet virtuella processorer och inte begränsade kärnor. Reservations avgiften för Standard_E32-8s_v3 baseras på 32 kärnor. 

Se pris [sidan för Azure-diskar](https://azure.microsoft.com/pricing/details/managed-disks/) för pris information för Ultra disk.

### <a name="azure-disk-reservation"></a>Reservation av Azure-disk

Disk reservation är möjligheten att köpa ett års disk lagring i förväg till en rabatt, vilket minskar den totala kostnaden. När du köper en disk reservation väljer du en speciell disk-SKU i en mål region, till exempel 10 P30 (1TiB) Premium-SSD i regionen USA, östra 2 för ett år. Reservations upplevelsen liknar reserverade instanser av virtuella datorer. Du kan paketera VM-och disk reservationer för att maximera dina besparingar. För närvarande erbjuder Azure disks-reservation en års åtagande plan för Premium SSD-SKU: er från P30 (1TiB) till P80 (32 TiB) i alla produktions regioner. Mer information om priser för reserverade diskar finns på [sidan med priser för Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Nästa steg

Se [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks/) för att komma igång.