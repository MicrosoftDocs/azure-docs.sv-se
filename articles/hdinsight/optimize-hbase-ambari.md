---
title: Optimera Apache HBase med Apache Ambari i Azure HDInsight
description: Använd webb gränssnittet Apache Ambari för att konfigurera och optimera Apache HBase.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 307e9decad35c20885fbe550cc1f7de58c9386c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942584"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Optimera Apache HBase med Apache Ambari i Azure HDInsight

Apache Ambari är ett webb gränssnitt för att hantera och övervaka HDInsight-kluster. En introduktion till Ambari-webbgränssnitt finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

Apache HBase-konfigurationen har ändrats från fliken **HBase-konfigurationer** . I följande avsnitt beskrivs några av de viktiga konfigurations inställningar som påverkar HBase prestanda.

## <a name="set-hbase_heapsize"></a>Ange HBASE_HEAPSIZE

HBase-heap-storleken anger den maximala mängd heap som ska användas i megabyte efter *region* och *huvud* servrar. Standardvärdet är 1 000 MB. Det här värdet bör justeras för klustrets arbets belastning.

1. Om du vill ändra går du till fönstret **Avancerat HBase-miljö** på fliken HBase- **konfiguration** och letar sedan upp `HBASE_HEAPSIZE` inställningen.

1. Ändra standardvärdet till 5 000 MB.

    !["Apache Ambari HBase Memory heapsize"](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Optimera Läs frekventa arbets belastningar

Följande konfigurationer är viktiga för att förbättra prestandan för Läs intensiva arbets belastningar.

### <a name="block-cache-size"></a>Block cachestorlek

Block-cachen är Read cache. Dess storlek styrs av `hfile.block.cache.size` parametern. Standardvärdet är 0,4, vilket är 40 procent av det totala region Server minnet. Ju större block-cache-storlek, desto snabbare blir de slumpmässiga läsningarna.

1. Om du vill ändra den här parametern går du till fliken **Inställningar** på fliken HBase **configs** och letar sedan upp **% av RegionServer som har allokerats för att läsa buffertar**.

    ![Cachestorlek för Apache HBase-minnes block](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Om du vill ändra värdet väljer du **redigerings** ikonen.

### <a name="memstore-size"></a>Memstores-storlek

Alla redigeringar lagras i minnesbufferten, som kallas en *memstores*. Den här bufferten ökar den totala mängden data som kan skrivas till disk i en enda åtgärd. Den påskyndar också åtkomsten till de senaste redigeringarna. Memstores storlek definieras av följande två parametrar:

* `hbase.regionserver.global.memstore.UpperLimit`: Definierar den maximala procent andelen av den region server som Memstores kombinerat kan använda.

* `hbase.regionserver.global.memstore.LowerLimit`: Definierar den minsta procent andelen av den region server som Memstores kombinerat kan använda.

Om du vill optimera för slumpmässiga läsningar kan du minska Memstores övre och nedre gränser.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Antal rader som hämtades vid genomsökning från disk

`hbase.client.scanner.caching`Inställningen definierar antalet rader som läses från disken när `next` metoden anropas på en skanner.  Standardvärdet är 100. Ju högre siffra, desto färre fjärran rop som görs från klienten till region servern, vilket resulterade i snabbare genomsökningar. Den här inställningen kommer dock även att öka minnes belastningen på klienten.

![Apache HBase antal hämtade rader](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ange inte värdet så att tiden mellan anrop av nästa metod på en skanner är större än tids gränsen för skannern. Tids gränsen för skannerns varaktighet definieras av `hbase.regionserver.lease.period` egenskapen.

## <a name="optimize-write-heavy-workloads"></a>Optimera Skriv tunga arbets belastningar

Följande konfigurationer är viktiga för att förbättra prestandan för Skriv tunga arbets belastningar.

### <a name="maximum-region-file-size"></a>Maximal region fil storlek

HBase lagrar data i ett internt fil format som kallas *HFile*. Egenskapen `hbase.hregion.max.filesize` definierar storleken på en enskild HFile för en region.  En region delas upp i två regioner om summan av alla HFiles i en region är större än den här inställningen.

![' Apache HBase HRegion Max FILESIZE '](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Ju större region fil storlek, desto mindre är antalet delningar. Du kan öka fil storleken för att fastställa ett värde som resulterar i maximal skriv prestanda.

### <a name="avoid-update-blocking"></a>Undvik att blockera uppdateringar

* Egenskapen `hbase.hregion.memstore.flush.size` definierar storleken som memstores töms på disk. Standard storleken är 128 MB.

* HBase region Blocks multiplikator definieras av `hbase.hregion.memstore.block.multiplier` . Standardvärdet är 4. Det högsta tillåtna värdet är 8.

* HBase blockerar uppdateringar om memstores är ( `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` ) byte.

    Med standardvärdet för tömnings storlek och block multiplikatorer blockeras uppdateringar när Memstores är 128 * 4 = 512 MB i storlek. Öka värdet för om du vill minska antalet blockerade uppdateringar `hbase.hregion.memstore.block.multiplier` .

![Apache HBase region block multiplikator](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definiera Memstores-storlek

Memstores-storlek definieras av `hbase.regionserver.global.memstore.upperLimit` parametrarna och `hbase.regionserver.global.memstore.lowerLimit` . Att ställa in dessa värden på samma sätt minskar pauserna under skrivningar (även om de orsakar frekvent tömning) och ger ökad skriv prestanda.

## <a name="set-memstore-local-allocation-buffer"></a>Ange Memstores Local Allocation buffer

Memstores för lokal allokering bestäms av egenskapen `hbase.hregion.memstore.mslab.enabled` . När aktive rad (true) förhindrar den här inställningen en heap-fragmentering under tung Skriv åtgärd. Standardvärdet är True.

![HBase. hregion. memstores. mslab. Enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimera kluster](./hdinsight-changing-configs-via-ambari.md)
* [Optimera Apache Hive](./optimize-hive-ambari.md)
* [Optimera Apache Pig](./optimize-pig-ambari.md)
