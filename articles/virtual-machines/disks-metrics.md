---
title: Disk mått
description: Exempel på disk burst-mått
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674980"
---
# <a name="disk-performance-metrics"></a>Mått för disk prestanda
Azure erbjuder mått i Azure Portal som ger insikt om hur dina virtuella datorer (VM) och diskar fungerar. Måtten kan också hämtas via ett API-anrop. Den här artikeln är uppdelad i tre underavsnitt:

- **Mått för disk-i/o, data flöde och ködjup** – dessa mått gör att du kan se lagrings prestandan från en disks perspektiv och en virtuell dator.
- **Mått för disk burst** – dessa mått ger till insyn i vår [burst](disk-bursting.md) -funktion på våra Premium-diskar.
- **Mått för lagrings-i/o-användning** – dessa mått hjälper till att diagnostisera Flask halsar i lagrings prestanda med diskar. 

Alla mått genereras varje minut, med undantag för måttet för mellanliggande kredit i procent, som genereras var 5: e minut.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Mått för disk-i/o, data flöde och ködjup
Följande mått är tillgängliga för att få insikt om prestanda för virtuella datorer och disk-till-disk-, data flödes-och ködjup:

- **Ködjup för OS-disk**: antalet aktuella utestående IO-begäranden som väntar på att läsas från eller skrivas till OS-disken.
- **Lästa byte i OS-disk/SEK**: antalet byte som läses in en sekund från operativ system disken.
- **Läs åtgärder för operativ system disk/SEK**: antalet åtgärder som har lästs in en sekund från OS-disken.
- **Skrivna byte på OS-disk/SEK**: antalet byte som skrivs från en sekund från operativ system disken.
- **Skriv åtgärder för operativ system disk/SEK**: antalet utgående åtgärder som skrivs från en sekund från operativ system disken.
- **Data disk Queue djup**: antalet aktuella utestående IO-begäranden som väntar på att läsas från eller skrivas till data diskarna.
- **Lästa byte på datadisk/SEK**: antalet byte som läses in en sekund från data diskarna.
- **Läs åtgärder för data disk/SEK**: antalet indata-åtgärder som läses i en sekund från data diskar.
- **Skrivna byte på datadisk/SEK**: antalet byte som skrivs i en sekund från data diskarna.
- **Skriv åtgärder för data disk/SEK**: antalet utgående åtgärder som har skrivits i en sekund från data diskar.
- **Disk-lästa byte/s**: det totala antalet byte som läses i en sekund från alla diskar som är kopplade till en virtuell dator.
- **Disk Läs åtgärder/SEK**: antalet ingångs åtgärder som läses i en sekund från alla diskar som är kopplade till en virtuell dator.
- **Disk-skrivna byte/s**: antalet byte som skrivs i en sekund från alla diskar som är kopplade till en virtuell dator.
- **Disk skrivnings åtgärder/SEK**: antalet utgående åtgärder som skrivs i en sekund från alla diskar som är kopplade till en virtuell dator.

## <a name="bursting-metrics"></a>Mått för burst-överföring
Följande mått hjälper till med att observera vår [burst](disk-bursting.md) -funktion på våra Premium-diskar:

- **Data disk Max burst-bandbredd**: den data flödes gräns som data diskarna kan överföras till.
- **Högsta bandbredd för OS-disk**: den data flödes gräns som OS-disken kan överföra till.
- **Data disk Max burst IOPS**: den IOPS-gräns som data diskarna kan överföra upp till.
- **Högsta burst-IOPS för OS-disk**: den IOPS-gräns som OS-disken kan överföra upp till.
- **Bandbredd för Datadiskens mål**: data flödes gränsen som data diskarna kan uppnå utan burst-överföring.
- **Bandbredd för OS-diskens mål**: den data flödes gräns som OS-disken kan uppnå utan burst-överföring.
- **Data disk mål IOPS**: den IOPS-gräns som data diskarna kan uppnå utan mellanburst.
- **OS-disk mål IOPS**: den IOPS-gräns som data diskarna kan uppnå utan burst-överföring.
- **Data disk som använder burst bps kredit procent**: den ackumulerade procent andelen av genomflödet som används för data diskarna. Orsakat ett 5-minuters intervall.
- **Procentuellt antal använda burst bps-krediter**: den ackumulerade procent andelen av genomflödet som används för OS-disken. Orsakat ett 5-minuters intervall.
- **Procent andel förbrukade IO-krediter i data diskar**: den ackumulerade procent andelen av IOPS-burst som används för data diskarna. Orsakat ett 5-minuters intervall.
- **Procent andel förbrukade IO-krediter i OS**: den ackumulerade procent andelen av IOPS-burst som används för OS-disken. Orsakat ett 5-minuters intervall.

## <a name="storage-io-utilization-metrics"></a>Mått för lagring i i/o-användning
Följande mått hjälper till att diagnostisera Flask halsar i din virtuella dator och disk kombination. Dessa mått är bara tillgängliga när du använder Premium-aktiverad virtuell dator. De här måtten är tillgängliga för alla disk typer förutom Ultra. 

Mått som hjälper till att diagnostisera disk-i/o-capping:

- **Data disk IOPS förbrukad procent**: den procent andel som beräknas av den data disk IOPS som slutförts över den etablerade data disken IOPS. Om den här mängden är 100% är ditt program i/o-gränsen från data diskens IOPS-gräns.
- **Förbrukad mängd data disk bandbredd**: den procent andel som beräknas av data disk flödet som slutförts via data flödet för den allokerade datadisken. Om den här mängden är på 100% är ditt program som körs i/o-gränser från data diskens bandbredds gräns.
- **Förbrukad procent** andel av OS-disk: den procent andel som beräknats av OS-disken IOPS som har slutförts på den etablerade OS-disken IOPS. Om den här mängden är på 100% är ditt program som körs i/o-gränser från din OS-disks IOPS-gräns.
- **Förbrukad procent andel OS disk bandbredd**: den procent andel som beräknats av OS-dataflödet som slutförts via det ALLOKERAde OS-disk flödet. Om den här mängden är på 100% är ditt program som körs i/o-gränser från din OS-disks bandbredds gräns.

Mått som hjälper till att diagnostisera VM-IO-capping:

- **VM cachelagrad IOPS förbrukad procent**: den procent andel som beräknats av den totala IOPS som slutförts via den maximala IOPS-gränsen för virtuella datorer. Om den här mängden är på 100% är ditt program som körs i/o-gränser från den virtuella datorns cachelagrade IOPS-gräns.
- **VM cachelagrad bandbredd för förbrukad bandbredd**: procent andelen beräknad av det totala disk data flödet som slutförts via det maximala data flödet för cachelagrat virtuella datorer Om den här mängden är 100% är ditt program i/o-gräns från den virtuella datorns cachelagrade bandbredds gräns.
- **VM Uncached IOPS förbrukad procent**: den procent andel som beräknats av det totala antalet IOPS på en virtuell dator som har slutförts via den maximala IOPS-gränsen på den virtuella datorn. Om den här mängden är 100% är ditt program i/o-gräns från den virtuella datorns ej cachelagrade IOPS-gräns.
- **Virtuell dator i cacheminnet för förbrukad bandbredd**: den procent andel som beräknas av den totala disk data flödet på en virtuell dator som har slutförts via det högsta etablerade data flödet för den virtuella datorn. Om den här mängden är på 100% är ditt program i/o-gräns från den virtuella datorns ej cachelagrade bandbredds gräns.

## <a name="storage-io-metrics-example"></a>Exempel på IO-mått för lagring

Nu ska vi köra ett exempel på hur du använder dessa nya mått för lagrings-i/o-användning för att hjälpa oss att felsöka där en Flask hals är i systemet. Systeminställningarna är desamma som i föregående exempel, förutom den här gången är den anslutna OS-disken *inte* cachelagrad.

**Installationsfilerna**

- Standard_D8s_v3
  - Cachelagrad IOPS: 16 000
  - Ej cachelagrad IOPS: 12 800
- P30 OS-disk
  - IOPS: 5 000
  - Cachelagring av värd: **inaktiverat**
- Två P30 data diskar × 2
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**
- Två P30 data diskar × 2
  - IOPS: 5 000
  - Cachelagring av värd: **inaktiverat**

Nu ska vi köra ett benchmarking-test på den här virtuella datorn och den disk kombination som skapar IO-aktivitet. Information om hur du kan mäta lagrings-i/o i Azure finns i avsnittet [om att mäta ditt program på Azure-disklagring](disks-benchmarks.md). Från benchmarking-verktyget kan du se att kombinationen av virtuell dator och disk kan uppnå 22 800 IOPS:

![Skärm bild av f i o-utdata som visar r = 22.8 k markerat.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 kan få totalt 28 600 IOPS. Vi använder måtten för att undersöka vad som händer och identifiera vår Flask hals i Storage. I den vänstra rutan väljer du **mått**:

![Skärm bild som visar mått som marker ATS i det vänstra fönstret.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Låt oss ta en titt på vårt mått på **VM cachelagrad IOPS förbrukade procent** :

![Skärm bild som visar den förbrukade procent satsen V M cachelagrad I O s](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Det här måttet visar att 61% av 16 000 IOPS som tilldelats till cachelagrade IOPS på den virtuella datorn används. Den här procent andelen innebär att lagrings-i/o-Flask halsen inte är med de diskar som cachelagras eftersom den inte är 100%. Nu ska vi titta på vårt mått för **förbrukade IOPS i virtuell dator i procent** :

![Skärm bild som visar V M Uncached I O P S förbrukade procent.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Måttet är på 100%. Det meddelar oss att alla 12 800 IOPS som tilldelats till den cachelagrade IOPS på den virtuella datorn används. Ett sätt som vi kan åtgärda problemet är att ändra storleken på den virtuella datorn till en större storlek som kan hantera ytterligare IO. Men innan vi gör det ska vi titta på den anslutna disken för att ta reda på hur många IOPS de ser. Kontrol lera OS-disken genom att titta på **OS-diskens IOPS förbrukade procent**:

![Skärm bild som visar O-S disk-I/O S förbrukade procent.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Det här måttet visar oss att cirka 90% av 5 000 IOPS som har allokerats för den här P30 OS-disken används. Den här procent andelen innebär att det inte finns någon Flask hals på OS-disken. Nu ska vi kontrol lera de data diskar som är anslutna till den virtuella datorn genom att titta på **data disken IOPS förbrukade procent**:

![Skärm bild som visar den förbrukade procenten för datadisk I O s.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Det här måttet anger att den genomsnittliga IOPS-förbrukade procenten på alla diskar som är anslutna är cirka 42%. Den här procent satsen beräknas baserat på IOPS som används av diskarna och hanteras inte från värd-cachen. Nu ska vi gå djupare i det här måttet genom att använda *delning* för dessa mått och dela upp dem med LUN-värdet:

![Skärm bild som visar datadisk I O P S förbrukade procent med delning.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Det här måttet talar om för oss att data diskarna som är anslutna på LUN 3 och 2 använder runt 85% av sina etablerade IOPS. Här är ett diagram över hur IO-händelsen ser ut som i arkitekturen för virtuella datorer och diskar:

![Diagram över enhets mått I O-exempel.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Monitor mått](../azure-monitor/essentials/data-platform-metrics.md)
- [Förklaring av mått aggregation](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Skapa, visa och hantera måttaviseringar med Azure Monitor](../azure-monitor/alerts/alerts-metric.md)