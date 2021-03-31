---
title: Optimera prestanda för Apache Kafka HDInsight-kluster
description: Ger en översikt över tekniker för att optimera Apache Kafka arbets belastningar i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935882"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimera prestanda för Apache Kafka HDInsight-kluster

Den här artikeln innehåller förslag på hur du kan optimera prestanda för dina Apache Kafka-arbetsbelastningar i HDInsight. Fokus är på anpassning av konfigurationen av Producer och Broker. Det finns olika sätt att mäta prestanda och de optimeringar som du använder beror på dina affärs behov.

## <a name="architecture-overview"></a>Översikt över arkitekturen

Kafka ämnen används för att organisera poster. Poster produceras av producenter, och används av konsumenter. Producenter skickar poster till Kafka-utjämnare som sedan lagrar data. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Ämnen delar upp poster över meddelandeköer. När du förbrukar poster, kan du använda en konsument per partition för att uppnå parallell bearbetning av data.

Replikering används för att duplicera partitioner mellan noder. Detta skyddar mot avbrott i Node (Broker). En enda partition mellan gruppen med repliker utses som partition ledare. Producenttrafik dirigeras till varje ledande nod med det tillstånd som hanteras av ZooKeeper.

## <a name="identify-your-scenario"></a>Identifiera ditt scenario

Apache Kafka prestanda har två huvud aspekter – data flöde och svars tid. Data flödet är den högsta hastigheten med vilken data kan bearbetas. Högre data flöde är vanligt vis bättre. Svars tiden är den tid det tar för data att lagras eller hämtas. Lägre latens är vanligt vis bättre. Det kan vara svårt att hitta rätt balans mellan genomflödet, svars tid och kostnaden för programmets infrastruktur. Dina prestanda krav kommer förmodligen att matcha något av följande tre vanliga situationer, baserat på om du behöver högt data flöde, låg latens eller båda:

* Högt data flöde, låg latens. Det här scenariot kräver både högt data flöde och låg latens (~ 100 millisekunder). Ett exempel på den här typen av program är övervakning av tjänst tillgänglighet.
* Högt data flöde, hög latens. Det här scenariot kräver högt data flöde (~ 1,5 Gbit/s) men kan tolerera högre latens (< 250 MS). Ett exempel på den här typen av program är telemetri data inmatning för nära real tids processer som säkerhets-och intrångs identifierings program.
* Lågt data flöde, låg latens. Det här scenariot kräver låg latens (< 10 ms) för bearbetning i real tid, men kan tolerera lägre data flöde. Ett exempel på den här typen av program är online-kontroller för stavnings-och grammatikkontroll.

## <a name="producer-configurations"></a>Producerande konfigurationer

I följande avsnitt visas några av de viktigaste konfigurations egenskaperna för att optimera prestanda för dina Kafka-tillverkare. En detaljerad förklaring av alla konfigurations egenskaper finns i [Apache Kafka dokumentation om producerande konfigurationer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Batchstorlek

Apache Kafka producenter sammansätter grupper av meddelanden (kallas batchar) som skickas som en enhet som ska lagras i en enda diskpartition. Batchstorlek innebär antalet byte som måste finnas innan gruppen överförs. Om du ökar `batch.size` parametern kan data flödet öka genom strömningen, eftersom bearbetningen minskar från nätverks-och IO-begäranden. Under låg belastning kan ökad batchstorlek öka Kafka skicka latens när producenten väntar på att en batch ska bli klar. Under hög belastning rekommenderar vi att du ökar batchstorleken för att förbättra data flödet och svars tiden.

### <a name="producer-required-acknowledgments"></a>Tillverkare som behöver bekräftelser

Konfigurationen av den obligatoriska tillverkaren `acks` avgör hur många bekräftelser som krävs av partitionsstrukturen innan en skrivbegäran anses vara slutförd. Den här inställningen påverkar data tillförlitligheten och använder värdena `0` , `1` , eller `-1` . Värdet för `-1` innebär att en bekräftelse måste tas emot från alla repliker innan skrivningen har slutförts. Inställningen `acks = -1` ger starkare garantier mot data förlust, men ger även högre latens och lägre data flöde. Om din program krav kräver högre data flöde kan du prova att ange `acks = 0` eller `acks = 1` . Tänk på att inte att bekräfta att alla repliker kan minska data säkerheten.

### <a name="compression"></a>Komprimering

En Kafka-producent kan konfigureras för att komprimera meddelanden innan de skickas till utjämnare. `compression.type`Inställningen anger vilken komprimerings-codec som ska användas. Komprimerings-codecar som stöds är "gzip", "fästfunktionen" och "lz4". Komprimering är fördelaktigt och bör övervägas om det finns en begränsning på disk kapaciteten.

Bland de två ofta använda komprimerings-codecarna `gzip` och `snappy` `gzip` har en högre komprimerings grad, vilket leder till lägre disk användning till kostnaden för högre CPU-belastning. `snappy`Codecen ger mindre komprimering med mindre processor belastning. Du kan bestämma vilken codec som ska användas baserat på Service Broker-disk eller tillverkarenas CPU-begränsningar. `gzip` kan komprimera data med en hastighet som är fem gånger högre än `snappy` .

Genom att använda data komprimering ökar antalet poster som kan lagras på en disk. Det kan också öka processor belastningen i fall där det finns ett matchnings fel mellan de komprimerings format som används av producenten och Broker. eftersom data måste komprimeras innan de skickas och expanderas innan bearbetningen.

## <a name="broker-settings"></a>Service Broker-inställningar

I följande avsnitt visas några av de viktigaste inställningarna för att optimera prestandan hos Kafka-utjämnare. En detaljerad förklaring av alla Service Broker-inställningar finns i [Apache Kafka dokumentation om producerande konfigurationer](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Antal diskar

Lagrings diskarna har begränsad IOPS (antal inkommande/utgående åtgärder per sekund) och lästa/skrivna byte per sekund. När du skapar nya partitioner lagrar Kafka varje ny partition på disken med minst ett antal befintliga partitioner för att utjämna dem över de tillgängliga diskarna. Trots lagrings strategin, vid bearbetning av hundratals partition repliker på varje disk, kan Kafka enkelt fylla tillgängliga disk data flöde. Kompromissen här är mellan data flöde och kostnad. Om programmet kräver större data flöde skapar du ett kluster med fler hanterade diskar per Broker. HDInsight har för närvarande inte stöd för att lägga till hanterade diskar i ett kluster som körs. Mer information om hur du konfigurerar antalet hanterade diskar finns i [Konfigurera lagring och skalbarhet för Apache Kafka i HDInsight](apache-kafka-scalability.md). Förstå kostnads konsekvenserna av att öka lagrings utrymmet för noderna i klustret.

### <a name="number-of-topics-and-partitions"></a>Antal ämnen och partitioner

Kafka-tillverkare skriver till ämnen. Kafka-konsumenter läser från ämnen. Ett ämne är associerat med en logg, som är en data struktur på disk. Kafka lägger till poster från en eller flera tillverkare i slutet av en ämnes logg. En ämnes logg består av många partitioner som är spridda över flera filer. De här filerna är i sin tur spridda över flera Kafka-klusternoder. Konsumenter läser från Kafka ämnen på sina takt och kan välja sin position (förskjutning) i ämnes loggen.

Varje Kafka-partition är en loggfil på systemet och producent trådar kan skriva till flera loggar samtidigt. På samma sätt, eftersom varje konsument tråd läser meddelanden från en partition, hanteras de också parallellt med flera partitioner.

Om du ökar partitionens densitet (antalet partitioner per Broker) läggs en kostnad som rör metadata-åtgärder och per partitions förfrågan/svar mellan partitionsstrukturen och dess följare. Även om data inte flödar över, kommer partitions repliker fortfarande att hämta data från ledare, vilket resulterar i extra bearbetning för att skicka och ta emot begär anden över nätverket.

För Apache Kafka kluster 1,1 och senare i HDInsight rekommenderar vi att du har högst 1000 partitioner per Broker, inklusive repliker. Om du ökar antalet partitioner per Broker minskas data flödet och det kan också leda till att ämnet inte är tillgängligt. Mer information om stöd för Kafka partition finns i [det officiella Apache Kafka blogg inlägget på antalet partitioner som stöds i version 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Mer information om hur du ändrar ämnen finns i [Apache Kafka: ändra ämnen](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Antal repliker

Högre replikeringsrelation resulterar i ytterligare förfrågningar mellan partitionsstrukturen och följare. Det innebär att en högre replikeringsrelation förbrukar mer disk och CPU för att hantera ytterligare förfrågningar, öka Skriv fördröjningen och minska data flödet.

Vi rekommenderar att du använder minst 3x-replikering för Kafka i Azure HDInsight. De flesta Azure-regioner har tre fel domäner, men i regioner med endast två fel domäner ska användarna använda 4x replikering.

Mer information om replikering finns i [Apache Kafka: replikering](https://kafka.apache.org/documentation/#replication) och [Apache Kafka: öka replikeringsrelationen](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Nästa steg

* [Bearbetning av biljontals händelser per dag med Apache Kafka på Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
