---
title: ta med fil
description: ta med fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808510"
---
![Diagram som visar D s v v 3-specifikationer.](media/vm-disk-performance/dsv3-documentation.jpg)

- Max gränsen för ej *cachelagrade* diskar är den maximala lagrings gränsen som den virtuella datorn kan hantera.
- Max gränsen för *cachelagrade* lagrings data är en separat gräns när du aktiverar cachelagring av värden.

Cachelagring av värdar fungerar genom att placera lagring närmare den virtuella dator som kan skrivas eller läsas snabbt. Mängden lagrings utrymme som är tillgängligt för den virtuella datorn för cachelagring av värden finns i dokumentationen. Du kan till exempel se att Standard_D8s_v3 levereras med 200 GiB cache Storage.

Du kan aktivera cachelagring av värdar när du skapar en virtuell dator och kopplar diskar. Du kan också aktivera och inaktivera cachelagring av värden på diskarna på en befintlig virtuell dator.

![Skärm bild som visar cachelagring av värden.](media/vm-disk-performance/host-caching.jpg)

Du kan justera cachelagringen för värden så att den överensstämmer med dina arbets belastnings krav för varje disk. Du kan ställa in att värddatorns cachelagring ska vara:

- **Skrivskyddat**: för arbets belastningar som bara har Läs åtgärder
- **Läs/skriv**: för arbets belastningar som utför balans mellan Läs-och skriv åtgärder

Om din arbets belastning inte följer något av dessa mönster rekommenderar vi inte att du använder cachelagring av värden.

Nu ska vi köra genom ett par exempel på olika värd-cacheinställningar för att se hur det påverkar data flödet och prestandan. I det här första exemplet ska vi titta på vad som händer med IO-begäranden när inställningen för cachelagring av värden är **skrivskyddad**.

**Installationsfilerna**

- Standard_D8s_v3
  - Cachelagrad IOPS: 16 000
  - Ej cachelagrad IOPS: 12 800
- P30 datadisk
  - IOPS: 5 000
  - Cachelagring av värd: **skrivskyddad**

När en läsning utförs och önskade data är tillgängliga i cacheminnet, returnerar cachen begärda data. Du behöver inte läsa från disken. Den här läsningen räknas mot den virtuella datorns cachelagrade gränser.

![Diagram som visar en läsnings träff för läsning av värddatorn.](media/vm-disk-performance/host-caching-read-hit.jpg)

När en läsning utförs och önskade data *inte* är tillgängliga i cacheminnet, vidarebefordras Read-begäran till disken. Sedan sparar disken den både i cacheminnet och den virtuella datorn. Den här läsningen räknas till både VM: s ej cachelagrade gräns och den virtuella datorns cachelagrade gräns.

![Diagram som visar en Read-cache med Läs värden.](media/vm-disk-performance/host-caching-read-miss.jpg)

När en skrivning utförs måste skrivningen skrivas till både cachen och disken innan den anses vara slutförd. Den här skrivningen räknas till den virtuella datorns ej cachelagrade gräns och den cachelagrade gränsen för den virtuella datorn.

![Diagram som visar skrivning av Skriv skydds värd.](media/vm-disk-performance/host-caching-write.jpg)

Härnäst ska vi titta på vad som händer med IO-begäranden när inställningen för cachelagring är inställd på **Läs/skriv**.

**Installationsfilerna**

- Standard_D8s_v3
  - Cachelagrad IOPS: 16 000
  - Ej cachelagrad IOPS: 12 800
- P30 datadisk
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**

En läsning hanteras på samma sätt som en skrivskyddad. Skrivningar är det enda som skiljer sig från läsning/skrivning-cachelagring. När du skriver med cachelagring av värden till **Läs-och skriv** behörighet behöver du bara skriva till värd-cachen för att anses vara slutförd. Skrivningen skrivs sedan till disken som bakgrunds process Lazy. Det innebär att en skrivning räknas mot cachelagrad IO när den skrivs till cachen. När Lazy skrivs till disken räknas den till i/o.

![Diagram som visar Skriv-och skriv värden för cachelagring av värdar.](media/vm-disk-performance/host-caching-read-write.jpg)

Nu ska vi fortsätta med vår Standard_D8s_v3 virtuella dator. Förutom den här gången aktiverar vi cachelagring av värdar på diskarna. Nu är den virtuella datorns IOPS-gräns 16 000 IOPS. Anslutna till den virtuella datorn är tre underliggande P30-diskar som kan hantera 5 000 IOPS.

**Installationsfilerna**

- Standard_D8s_v3
  - Cachelagrad IOPS: 16 000
  - Ej cachelagrad IOPS: 12 800
- P30 OS-disk
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**
- Två P30 data diskar × 2
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**

![Diagram som visar ett exempel på en värd-cachelagring.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Programmet använder en Standard_D8s_v3 virtuell dator med cachelagring aktiverat. Den gör en begäran om 15 000 IOPS. Förfrågningarna är uppdelade som 5 000 IOPS till varje underliggande disk som är ansluten. Ingen prestanda capping inträffar.

## <a name="combined-uncached-and-cached-limits"></a>Kombinerade gränser som inte är cachelagrade och cachelagrade

Den virtuella datorns cachelagrade gränser skiljer sig från de ej cachelagrade gränserna. Det innebär att du kan aktivera cachelagring av värdar på diskar som är anslutna till en virtuell dator utan att aktivera cachelagring av värden på andra diskar. Den här konfigurationen gör att dina virtuella datorer får en total lagrings-IO för den cachelagrade gränsen plus den ej cachelagrade gränsen.

Vi går igenom ett exempel för att hjälpa dig att förstå hur dessa gränser fungerar tillsammans. Vi kommer att fortsätta med konfigurationen för den Standard_D8s_v3 virtuella datorn och Premium-diskar som är ansluten.

**Installationsfilerna**

- Standard_D8s_v3
  - Cachelagrad IOPS: 16 000
  - Ej cachelagrad IOPS: 12 800
- P30 OS-disk
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**
- Två P30 data diskar × 2
  - IOPS: 5 000
  - Cachelagring av värd: **läsa/skriva**
- Två P30 data diskar × 2
  - IOPS: 5 000
  - Cachelagring av värd: **inaktiverat**

![Diagram som visar ett exempel på en värd-cachelagring med Fjärrlagring.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

I det här fallet gör programmet som körs på en Standard_D8s_v3 virtuell dator en begäran om 25 000 IOPS. Begäran är uppdelad som 5 000 IOPS till var och en av de anslutna diskarna. Tre diskar använder cachelagring av värdar och två diskar använder inte cachelagring av värden.

- Eftersom de tre diskarna som använder cachelagring av värden ligger inom de cachelagrade gränserna för 16 000 slutförs dessa förfrågningar. Ingen capping för lagrings prestanda inträffar.
- Eftersom de två diskar som inte använder cachelagring av värden ligger inom de ej cachelagrade gränserna för 12 800 slutförs även dessa förfrågningar. Ingen capping inträffar.

## <a name="disk-performance-metrics"></a>Mått för disk prestanda

Vi har mått på Azure som ger insikt om hur dina virtuella datorer och diskar presterar. De här måtten kan visas via Azure Portal. De kan också hämtas via ett API-anrop. Måtten beräknas med en minuters intervall. Följande mått är tillgängliga för att få insikt om VM och disk-i/o och även på data flödes prestanda:

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

## <a name="storage-io-utilization-metrics-example"></a>Exempel på användnings mått för lagrings-i/o

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
