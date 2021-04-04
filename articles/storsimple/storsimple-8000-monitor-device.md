---
title: Övervaka din StorSimple 8000-serie enhet
description: Beskriver hur du använder tjänsten StorSimple Enhetshanteraren för att övervaka användning, I/O-prestanda och kapacitets användning.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962441"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Använd tjänsten StorSimple Enhetshanteraren för att övervaka StorSimple-enheten

## <a name="overview"></a>Översikt
Du kan använda tjänsten StorSimple Enhetshanteraren för att övervaka vissa enheter i din StorSimple-lösning. Du kan skapa anpassade diagram baserat på I/O-prestanda, kapacitets användning, nätverks data flöde och enhets prestanda mått och fästa dem på instrument panelen. Mer information finns i [anpassa portalens instrument panel](../azure-portal/azure-portal-dashboards.md).

Om du vill visa övervaknings informationen för en speciell enhet går du till Azure Portal och väljer tjänsten StorSimple Enhetshanteraren. Välj enheten i listan med enheter och gå sedan till **övervaka**. Du kan sedan se diagram över **kapacitet**, **användning** och **prestanda** för den valda enheten.

## <a name="capacity"></a>Kapacitet
**Kapaciteten** spårar det allokerade utrymmet och utrymmet som återstår på enheten. Den återstående kapaciteten visas sedan lokalt eller i nivå.

Den tillhandahållna och återstående kapaciteten delas upp ytterligare genom att skiktas och lokalt fästa volymer. För varje volym visas den etablerade kapaciteten och den återstående kapaciteten på enheten.

![I/o-kapacitet](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Användning
**Användningen** spårar mått som rör mängden data lagrings utrymme som används av volymerna, volym behållarna eller enheten. Du kan skapa rapporter baserat på kapacitets användningen av din primära lagring, din moln lagring eller din enhets lagring. Kapacitets användningen kan mätas på en bestämd volym, en bestämd volym behållare eller alla volym behållare.
Som standard rapporteras användningen för de senaste 24 timmarna. Du kan redigera diagrammet för att ändra hur länge användningen rapporteras genom att välja från:
* Senaste 24 timmarna
* Senaste 7 dagarna
* Senaste 30 dagarna
* De senaste 90 dagarna
* Senaste året

Två nyckel värden, tillväxt och intervall rapporteras för användnings diagrammen. Intervallet avser det högsta värdet och de lägsta värdena för användningen som rapporter ATS under den valda varaktigheten (t. ex. under de senaste 7 dagarna).

Tillväxten avser ökningen av användningen från den första dagen till den sista dagen under den valda varaktigheten. 

Tillväxt och intervall kan också representeras av följande ekvationer:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Den primära, moln-och lokala lagring som används kan beskrivas på följande sätt:

### <a name="primary-storage-usage"></a>Primär lagrings användning
De här diagrammen visar mängden data som skrivs till StorSimple-volymer innan data dedupliceras och komprimeras. Du kan visa den primära lagrings platsen som används av alla volymer i en volym behållare eller för en enskild volym. Det använda primära lagrings utrymmet delas upp ytterligare genom primärt lagrings utrymme som används och primärt lokalt fäst lagrings utrymme som används.

Följande diagram visar det primära lagrings utrymmet som används för en StorSimple-enhet före och efter att en ögonblicks bild av molnet togs. Eftersom detta bara är volym data bör en moln ögonblicks bild inte ändra den primära lagringen. Som du kan se visar diagrammet ingen skillnad i den primära eller lokalt fasta lagring som används på grund av en ögonblicks bild av molnet. Moln ögonblicks bilden startade cirka 11:50 PM på enheten.

![Primär kapacitets användning efter moln ögonblicks bild](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Om du nu kör IO på värden som är ansluten till din StorSimple-enhet, kommer du att se en ökning av den primära lagrings platsen eller den primära lokalt fästa lagrings utrymme som används beroende på vilka volymer (på nivå eller lokalt fästa) som du skriver data till. Här är de primära lagrings användnings diagrammen för en StorSimple-enhet. På den här enheten har StorSimple-värden börjat betjäna skrivningar vid cirka 2:30 PM på en nivå volym på enheten. Du kan se topp i de skrivna byte/s som motsvarar den i/o som körs på värden.

![Prestanda när IO körs på skiktade volymer](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Om du tittar på den primära lagrings platsen som används, som har varit aktiv, förblir den primära lokalt fästa användningen oförändrad eftersom det inte finns några skrivningar till lokalt fästa volymer på enheten.

![Primär kapacitets användning när IO körs på skiktade volymer](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Om du kör uppdatering 3 eller senare kan du dela upp den primära lagrings kapaciteten med en enskild volym, alla volymer, alla volymer på nivån och alla lokalt fästa volymer som visas nedan. Genom att dela upp alla lokalt fästa volymer kan du snabbt fastställa hur mycket av den lokala nivån som används.

![Primär kapacitets användning för alla skiktade volymer](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primär kapacitets användning för alla lokalt fästa volymer](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Du kan också klicka på var och en av volymerna i listan och se motsvarande användning.

![Primär kapacitets användning för alla lokalt fästa volymer 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Användning av moln lagring
De här diagrammen visar mängden moln lagring som används. Den här informationen är deduplicerad och komprimerad. Den här mängden innehåller moln ögonblicks bilder som kan innehålla data som inte återspeglas i någon primär volym och bevaras för äldre eller krävda kvarhållning. Du kan jämföra de primära och moln lagrings förbruknings siffrorna för att få en uppfattning om data minsknings takten, även om talet inte är exakt.

I följande diagram visas moln lagrings användningen för en StorSimple-enhet när en ögonblicks bild av molnet togs.

* Moln ögonblicks bilden startade kl. 11:50 am på enheten och du kan se att innan moln ögonblicks bilden användes, användes ingen moln lagring. 
* När moln ögonblicks bilden har slutförts, tog moln lagrings användningen upp 0,89 GB. 
* Medan moln ögonblicks bilden pågår, finns det också motsvarande topp i IO från enhet till molnet.

    ![Moln lagrings användning innan moln ögonblicks bilder](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Användning av moln lagring efter moln ögonblicks bilder](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/o från enhet till moln under en moln ögonblicks bild](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Lokal lagrings användning
De här diagrammen visar den totala användningen av enheten, vilket är mer än primär lagrings användning eftersom den linjära nivån SSD ingår. Den här nivån innehåller en mängd data som också finns på enhetens övriga nivåer. Kapaciteten i den linjära nivån SSD är aktive ras så att den gamla informationen flyttas till hård disk nivån (då den dedupliceras och komprimeras) och därefter till molnet när nya data kommer in.

Med tiden ökar primärt lagrings utrymme och lokal lagring som används förmodligen tillsammans tills data börjar på nivå av molnet. Vid det här tillfället börjar den använda lokala lagringen förmodligen att platå, men den primära lagrings enheten ökar när mer data skrivs.

Följande diagram visar det primära lagrings utrymmet som används för en StorSimple-enhet när en ögonblicks bild av molnet togs. Moln ögonblicks bilden startade kl. 11:50 och den lokala lagringen började minska vid denna tidpunkt. Den lokala lagringen som används gick från 1,445 GB till 1,09 GB. Det tyder på att de mest sannolika data i den linjära SSD-nivån har deduplicerats, komprimerats och flyttats till hård disk nivån. Observera att om enheten redan har en stor mängd data på både SSD-och HDD-nivåerna kanske inte den här minskningen visas. I det här exemplet har enheten en liten mängd data.

![Lokal lagrings användning efter moln ögonblicks bilder](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestanda
**Prestanda** spårar mått som rör antalet Läs-och skriv åtgärder mellan antingen iSCSI-initierarens gränssnitt på värd servern och enheten eller enheten och molnet. Prestandan kan mätas för en bestämd volym, en bestämd volym behållare eller alla volym behållare. Prestanda omfattar även processor användning och nätverks data flöde för de olika nätverks gränssnitten på enheten.

### <a name="io-performance-for-initiator-to-device"></a>I/O-prestanda för initierare till enhet
I diagrammet nedan visas I/O för initieraren till enheten för alla volymer för en produktions enhet. De ritade värdena är lästa och skrivna byte per sekund. Du kan också skapa diagram med Läs-, skriv-och enastående i/o, eller Läs-och skriv fördröjningar.

![I/o-prestanda för initierare till enhet](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/O-prestanda för enhet till molnet
För samma enhet ritas i/O-åtgärderna för data från enheten till molnet för alla volym behållare. På den här enheten är data endast på den linjära nivån och inget har spillat i molnet. Det sker inga Läs-och skriv åtgärder från enheten till molnet. Därför är topparna i diagrammet i ett intervall på 5 minuter som motsvarar den frekvens som pulsslaget kontrol leras mellan enheten och tjänsten.

För samma enhet vidtogs en moln ögonblicks bild för volym data från och med 11:50. Detta ledde till att data flödar från enheten till molnet. Skrivningar betjänades molnet under denna varaktighet. I/o-diagrammet visas en topp i de skrivna byte/s som motsvarar den tidpunkt då ögonblicks bilden togs.

![I/o från enhet till moln under en moln ögonblicks bild](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Nätverks data flöde för enhetens nätverks gränssnitt
**Nätverks data flödet** spårar mått som rör mängden data som överförs från iSCSI-initierarens nätverks gränssnitt på värd servern och enheten och mellan enheten och molnet. Du kan övervaka det här måttet för vart och ett av iSCSI-nätverks gränssnitten på enheten.

I följande diagram visas nätverks data flödet för data 0, 1 1 GbE-nätverket på enheten, som både är moln-aktiverat (standard) och iSCSI-aktiverat. På den här enheten den 14 juni till den 14 juni var data i molnet (inga ögonblicks bilder av molnet togs vid den tidpunkten, vilket pekar på att det går att flytta data till molnet) som ledde till att IO-inlevereras till molnet. Det finns en motsvarande topp i nätverks data flödes diagrammet för samma tid och det mesta av nätverks trafiken är utgående till molnet.

![Nätverks data flöde för data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Om vi tittar på data diagrammets data flödes diagram, ett annat 1 GbE-nätverkskort som bara var iSCSI-aktiverat, så fanns det nästan ingen nätverks trafik under denna varaktighet.

![Nätverks data flöde för data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-användning för enhet
**Processor belastningen** spårar mått som rör CPU: n som används på enheten. Följande diagram visar processor användnings statistik för en enhet i produktionen.

![CPU-användning för enhet](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder instrument panelen för StorSimple-Enhetshanteraren](./storsimple-8000-device-dashboard.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](./storsimple-8000-manager-service-administration.md).