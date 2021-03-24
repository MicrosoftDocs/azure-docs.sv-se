---
title: Optimera Apache-gris med Apache Ambari i Azure HDInsight
description: Använd webb gränssnittet Apache Ambari för att konfigurera och optimera Apache gris.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 43159360342063af1682a984db037ae286721411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871171"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Optimera Apache-gris med Apache Ambari i Azure HDInsight

Apache Ambari är ett webb gränssnitt för att hantera och övervaka HDInsight-kluster. En introduktion till Ambari-webbgränssnitt finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

Apache gris-egenskaper kan ändras från Ambari-webbgränssnittet för att justera gris-frågor. Om du ändrar egenskaper för gris från Ambari ändras egenskaperna för svinen direkt i `/etc/pig/2.4.2.0-258.0/pig.properties` filen.

1. Om du vill ändra egenskaper för gris navigerar du till fliken **konfigurations** inställningar för svin och expanderar sedan fönstret **Avancerat gris-Properties** .

1. Hitta, ta bort kommentar och ändra värdet för den egenskap som du vill ändra.

1. Välj **Spara** längst upp till höger i fönstret för att spara det nya värdet. Vissa egenskaper kan kräva att tjänsten startas om.

    :::image type="content" source="./media/optimize-pig-ambari/advanced-pig-properties.png" alt-text="Avancerade egenskaper för Apache-gris" border="true":::

> [!NOTE]  
> Alla inställningar för sessionsbaserade åsidosätter egenskaps värden i `pig.properties` filen.

## <a name="tune-execution-engine"></a>Justera körnings motor

Det finns två körnings motorer tillgängliga för att köra gris-skript: MapReduce och Tez. Tez är en optimerad motor och är mycket snabbare än MapReduce.

1. Om du vill ändra körnings motorn går du till fönstret **Avancerat gris-Properties** och letar upp egenskapen `exectype` .

1. Standardvärdet är **MapReduce**. Ändra den till **Tez**.

## <a name="enable-local-mode"></a>Aktivera lokalt läge

På samma sätt som Hive, används lokalt läge för att påskynda jobb med relativt mindre mängd data.

1. Aktivera lokalt läge genom att ange `pig.auto.local.enabled` **True**. Standardvärdet är false.

1. Jobb med en indata-storlek som är mindre än `pig.auto.local.input.maxbytes` egenskap svärdet anses vara små jobb. Standardvärdet är 1 GB.

## <a name="copy-user-jar-cache"></a>Kopiera användarens jar-cache

Gris kopierar JAR-filerna som krävs av UDF: er till en distribuerad cache för att göra dem tillgängliga för aktiviteter. Dessa JAR v7 ändras inte ofta. Om inställningen är aktive rad `pig.user.cache.enabled` kan jar v7 placeras i ett cacheminne för att återanvända dem för jobb som körs av samma användare. Den här inställningen resulterar i en mindre ökning av jobbets prestanda.

1. Om du vill aktivera anger `pig.user.cache.enabled` du True. Standardvärdet är False.

1. Om du vill ange bas Sök vägen för den cachelagrade jar v7 anger `pig.user.cache.location` du bas Sök vägen. Standardvärdet är `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Optimera prestanda med minnes inställningar

Följande minnes inställningar kan hjälpa till att optimera skript prestanda för gris.

* `pig.cachedbag.memusage`: Mängden minne som tilldelats en påse. En påse är en samling av tupler. En tupel är en ordnad uppsättning fält och ett fält är en del av data. Om data i en påse överskrider det tilldelade minnet, så spiller det till disk. Standardvärdet är 0,2, vilket motsvarar 20 procent av tillgängligt minne. Det här minnet delas i alla väskor i ett program.

* `pig.spill.size.threshold`: Väskor som är större än den här tröskelvärdet för spill storlek (i byte) spillas till disken. Standardvärdet är 5 MB.

## <a name="compress-temporary-files"></a>Komprimera temporära filer

Gris genererar temporära filer under jobb körningen. Komprimering av de temporära filerna resulterar i ökad prestanda när filer läses eller skrivs till disk. Följande inställningar kan användas för att komprimera temporära filer.

* `pig.tmpfilecompression`: När värdet är true, aktiverar temporär fil komprimering. Standardvärdet är false.

* `pig.tmpfilecompression.codec`: Den komprimerings-codec som används för att komprimera de temporära filerna. De rekommenderade komprimerings-codecarna är LZO och fästfunktionen för lägre CPU-användning.

## <a name="enable-split-combining"></a>Aktivera delnings kombination

När den är aktive rad kombineras små filer för färre kart aktiviteter. Den här inställningen förbättrar effektiviteten hos jobb med många små filer. Om du vill aktivera anger `pig.noSplitCombination` du True. Standardvärdet är false.

## <a name="tune-mappers"></a>Finjustera mappningar

Antalet Mapper styrs genom att egenskapen ändras `pig.maxCombinedSplitSize` . Den här egenskapen anger storleken på de data som ska bearbetas av en enskild kart aktivitet. Standardvärdet är fil systemets standard block storlek. Att öka det här värdet resulterar i ett lägre antal Mapper-aktiviteter.

## <a name="tune-reducers"></a>Finjustera dämpare

Antalet dereducerare beräknas baserat på parametern `pig.exec.reducers.bytes.per.reducer` . Parametern anger antalet byte som bearbetas per minskning, som standard 1 GB. Om du vill begränsa det maximala antalet avreducerare ställer du in `pig.exec.reducers.max` egenskapen som standard 999.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimera kluster](./hdinsight-changing-configs-via-ambari.md)
* [Optimera Apache HBase](./optimize-hbase-ambari.md)
* [Optimera Apache Hive](./optimize-hive-ambari.md)
