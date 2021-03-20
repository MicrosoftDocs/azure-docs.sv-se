---
title: Strömning i stor skala i Azure HDInsight
description: Använda data strömning med skalbara Apache-kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 2b2dfe9da55548f2648f847a9d7c2cb3478e6bad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931708"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Direktuppspelning i skala i HDInsight

Real tids data lösningar i real tid fungerar på data som är i rörelse. Dessa data är vanligt vis mest värdefulla vid ankomst tillfället. Om den inkommande data strömmen blir större än vad som kan hanteras för tillfället kan du behöva begränsa resurserna. Alternativt kan ett HDInsight-kluster skala upp för att uppfylla din strömnings lösning genom att lägga till noder på begäran.

I ett strömmande program genererar en eller flera data källor händelser (ibland i miljon tals per sekund) som måste matas in snabbt utan att du behöver släppa någon användbar information. Inkommande händelser hanteras med *Stream-buffring*, även kallat *Event Queuing*, av en tjänst som [Apache Kafka](kafka/apache-kafka-introduction.md) eller [Event Hubs](https://azure.microsoft.com/services/event-hubs/). När du har samlat in händelserna kan du analysera data med hjälp av ett real tids analys system i lager *bearbetnings* skiktet, till exempel [Apache Storm](storm/apache-storm-overview.md) eller [Apache Spark strömning](spark/apache-spark-streaming-overview.md). Bearbetade data kan lagras i långsiktiga lagrings system, t. ex. [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)och visas i real tid på en Business Intelligence instrument panel, till exempel [Power BI](https://powerbi.microsoft.com), Tableau eller en anpassad webb sida.

![Återströmnings mönster i Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka tillhandahåller en meddelande Queueing-tjänst med stora data flöden och låg latens, och är nu en del av Apache-serien med program vara med öppen källkod (OSS). Kafka använder en publicerings-och prenumerations meddelande modell och lagrar data strömmar med partitionerade data på ett säkert sätt i ett distribuerat, replikerat kluster. Kafka skalas linjärt när data flödet ökar.

Mer information finns i [introduktion Apache Kafka på HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm är ett distribuerat, feltolerant beräknings system med öppen källkod som är optimerat för bearbetning av data strömmar i real tid med Hadoop. Kärn data enheten för en inkommande händelse är en tupel, som är en oföränderlig uppsättning nyckel/värde-par. En obunden sekvens av dessa Tupleer formar en ström som kommer från en kanalen. Kanalen kapslar in en strömmande data källa (till exempel Kafka) och genererar tupler. En Storm-topologi är en sekvens med omvandlingar för dessa strömmar.

Mer information finns i [Vad är Apache storm på Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark streaming är ett tillägg till Spark, vilket gör att du kan återanvända samma kod som du använder för batchbearbetning. Du kan kombinera både batch-och interaktiva frågor i samma program. Till skillnad från Storm ger Spark-direktuppspelning tillstånds känsligt exakt en gång för bearbetning av semantik. När det används i kombination med [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), som säkerställer att alla Kafka-data tas emot av Spark streaming exakt en gång, är det möjligt att uppnå exakt en gång. En av Spark-strömmarnas styrkor är dess feltoleranta funktioner och återställer Felaktiga noder snabbt när flera noder används i klustret.

Mer information finns i [Vad är Apache Spark streaming?](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skala ett kluster

Även om du kan ange antalet noder i klustret när du skapar, kanske du vill öka eller minska klustret så att det matchar arbets belastningen. Med alla HDInsight-kluster kan du [ändra antalet noder i klustret](hdinsight-administer-use-portal-linux.md#scale-clusters). Spark-kluster kan släppas utan data förlust, eftersom alla data lagras i Azure Storage eller Data Lake Storage.

Det finns fördelar med att koppla från teknikerna. Kafka är till exempel en Event buffer-teknik, så det är mycket i/o-intensiv och behöver inte mycket processor kraft. I jämförelse är Stream-processorer som Spark-direktuppspelning beräknings intensiva, vilket kräver mer kraftfulla virtuella datorer. Genom att använda de här teknikerna i olika kluster kan du skala dem oberoende av varandra samtidigt som de bäst använder de virtuella datorerna.

### <a name="scale-the-stream-buffering-layer"></a>Skala Stream buffer Layer

Teknikerna för Stream Buffing Event Hubs och Kafka både använder partitioner och konsumenter läser från dessa partitioner. Skalning av indata-genomflöde kräver skalning av antalet partitioner, och om du lägger till partitioner ökar parallellitet. I Event Hubs kan du inte ändra antalet partitioner efter distributionen så det är viktigt att börja med mål skalan i åtanke. Med Kafka är det möjligt att [lägga till partitioner](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), även när Kafka bearbetar data. Kafka tillhandahåller ett verktyg för att omtilldela partitioner  `kafka-reassign-partitions.sh` . HDInsight tillhandahåller ett [verktyg för ombalansering av partition Replica](https://github.com/hdinsight/hdinsight-kafka-tools),  `rebalance_rackaware.py` . Detta verktyg anropar `kafka-reassign-partitions.sh` verktyget på ett sådant sätt att varje replik finns i en separat feldomän och uppdaterings domän, vilket gör Kafka rack och ökar fel toleransen.

### <a name="scale-the-stream-processing-layer"></a>Skala Stream-bearbetnings skiktet

Både Apache Storm och Spark Streaming stöder tillägg av arbetsnoder till deras kluster, även när data bearbetas.

Om du vill dra nytta av nya noder som lagts till genom skalnings Storm måste du balansera om alla Storm-topologier som startats innan kluster storleken ökade. Den här ombalanseringen kan göras med storm Web UI eller dess CLI. Mer information finns i Apache Storm- [dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark använder tre nyckel parametrar för att konfigurera dess miljö, beroende på program krav: `spark.executor.instances` , `spark.executor.cores` och `spark.executor.memory` . En *utförar* är en process som startas för ett Spark-program. En utförar körs på Worker-noden och ansvarar för att utföra programmets uppgifter. Standard antalet körningar och utförar storlekarna för varje kluster beräknas utifrån antalet arbetsnoder och storleken på arbets noden. Dessa tal lagras i `spark-defaults.conf` filen på varje kluster huvud nod.

Dessa tre parametrar kan konfigureras på kluster nivå, för alla program som körs i klustret och kan också anges för varje enskilt program. Mer information finns i [Hantera resurser för Apache Spark kluster](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa och övervaka en Apache Storm topologi i Azure HDInsight](storm/apache-storm-quickstart.md)
* [Exempeltopologier för Apache Storm på HDInsight](storm/apache-storm-example-topology.md)
* [Introduktion till Apache Spark på HDInsight](spark/apache-spark-overview.md)
* [Kom igång med Apache Kafka i HDInsight](kafka/apache-kafka-get-started.md)