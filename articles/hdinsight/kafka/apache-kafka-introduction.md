---
title: En introduktion till Apache Kafka på HDInsight – Azure
description: 'Lär dig mer om Apache Kafka på HDInsight: vad det är, dess syfte och var du hittar exempel och kommer igång.'
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: baf41b9e66b14e4b255b6e13b22691357f9e7309
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870814"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Vad är Apache Kafka i Azure HDInsight

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Kafka tillhandahåller även funktionen för asynkron meddelandekö som liknar en meddelandekö där du kan publicera och prenumerera på namngivna dataströmmar.

Följande är kännetecknen för Kafka på HDInsight:

* Det är en hanterad tjänst som tillhandahåller en förenklad konfigurations process. Resultatet är en konfiguration som har testats av och som stöds av Microsoft.

* Microsoft tillhandahåller ett 99,9 % serviceavtal (SLA) för Kafkas driftstid. Mer information finns i dokumentet [SLA-information för HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Den använder hanterade diskar i Azure som lagringsenhet för Kafka. Hanterade diskar kan tillhandahålla upp till 16 TB lagringsutrymme per asynkron Kafka-meddelandekö. Mer information om att konfigurera hanterade diskar med Kafka i HDInsight finns i [Öka skalbarheten för Apache Kafka i HDInsight](apache-kafka-scalability.md).

    För mer information om hanterade diskar, se [Hanterade Azure-diskar](../../virtual-machines/managed-disks-overview.md).

* Kafka har utformats med en enda dimensionell vy av ett rack. Azure delar ett rack i två dimensioner – uppdateringsdomäner (UD) och feldomäner (FD). Microsoft tillhandahåller verktyg som balanserar om Kafka-partitioner och -repliker mellan uppdateringsdomäner och feldomäner.

    Mer information finns i [Hög tillgänglighet med Apache Kafka i HDInsight](apache-kafka-high-availability.md).

* I HDInsight kan du ändra antalet arbetarnoder (som är värdar för den asynkrona Kafka-meddelandekön) när klustret har skapats. Du kan utföra en uppskalning från Azure Portal, Azure PowerShell och andra hanterings gränssnitt för Azure. För Kafka bör du balansera om partitionsrepliker efter eventuell skalning. När du balanserar om partitionerna kan Kafka dra nytta av nya antalet arbetarnoder.

   HDInsight-Kafka stöder inte nedskalning eller minskning av antalet utjämnare i ett kluster. Om ett försök görs att minska antalet noder `InvalidKafkaScaleDownRequestErrorCode` returneras ett fel.

    Mer information finns i [Hög tillgänglighet med Apache Kafka i HDInsight](apache-kafka-high-availability.md).

* Azure Monitor-loggar kan användas för att övervaka Kafka på HDInsight. Med Azure Monitor-loggar kan du se information på virtuell dator-nivå såsom mått för diskar och nätverkskort samt JMX-mått från Kafka.

    Mer information finns i [Analysera loggar för Apache Kafka i HDInsight](apache-kafka-log-analytics-operations-management.md).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka på HDInsight-arkitektur

Följande diagram visar en typisk Kafka-konfiguration som använder konsumentgrupper, partitionering och replikering för att erbjuda parallell läsning av händelser med feltolerans:

:::image type="content" source="./media/apache-kafka-introduction/kafka-cluster-diagram.png" alt-text="Diagram över Kafka-klusterkonfiguration" border="false":::

Apache ZooKeeper hanterar Kafka-klustrets status. Zookeeper är utformat för samtidiga och flexibla transaktioner med kort svarstid.

Kafka lagrar poster (data) i **ämnen**. Poster produceras av **producenter**, och används av **konsumenter**. Producenter skickar poster till **asynkrona Kafka-meddelandeköer**. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Ämnen delar upp poster över meddelandeköer. När du förbrukar poster, kan du använda en konsument per partition för att uppnå parallell bearbetning av data.

Replikering används för att duplicera partitioner mellan noder, vilket skyddar mot avbrott på noder (meddelandeköer). En partition som är markerad med *(L)* i diagrammet är ledande för den angivna partitionen. Producenttrafik dirigeras till varje ledande nod med det tillstånd som hanteras av ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Varför bör du använda Apache Kafka i HDInsight?

Följande är vanliga uppgifter och mönster som kan utföras med hjälp av Kafka på HDInsight:

|Användning |Beskrivning |
|---|---|
|Replikering av Apache Kafka data|Kafka innehåller verktyget MirrorMaker som replikerar data mellan olika Kafka-kluster. Information om hur du använder MirrorMaker finns i avsnittet om att [replikera Apache Kafka-ämnen med Apache Kafka i HDInsight](apache-kafka-mirroring.md).|
|Publicera – prenumerera på meddelande mönster|Kafka innehåller ett producent-API för publicering av poster till ett Kafka-ämne. Konsument-API används när det finns en prenumererar på ett ämne. Mer information finns i [Kom igång med Apache Kafka i HDInsight](apache-kafka-get-started.md).|
|Strömbearbetning|Kafka används ofta med Apache Storm eller Spark för dataströmsbearbetning i realtid. Kafka 0.10.0.0 (HDInsight version 3.5 och 3.6) införde en strömmande API som gör att du kan skapa direktuppspelade lösningar utan Storm eller Spark. Mer information finns i [Kom igång med Apache Kafka i HDInsight](apache-kafka-get-started.md).|
|Horisontell skalning|Kafka partitionerar dataströmmar mellan noderna i HDInsight-klustret. Konsumentprocesser kan associeras med enskilda partitioner för att ge belastningsutjämning när poster används. Mer information finns i [Kom igång med Apache Kafka i HDInsight](apache-kafka-get-started.md).|
|Leverans i ordning|Inom varje partition lagras posterna i strömmen i den ordning som de togs emot. Genom att associera en konsumentprocess per partition bearbetas posterna garanterat i ordning. Mer information finns i [Kom igång med Apache Kafka i HDInsight](apache-kafka-get-started.md).|
|Meddelandefunktion|Eftersom Kafka stöder meddelandemönstret publicera-prenumerera används det ofta som en asynkron meddelandekö.|
|Aktivitets spårning|Eftersom Kafka ger ordningsbaserad loggning av poster kan det användas för att spåra och återskapa aktiviteter. Till exempel användaråtgärder på en webbplats eller i ett program.|
|Aggregering|Med hjälp av strömbearbetning kan du sammanställa information från olika strömmar för att kombinera och centralisera information till användningsdata.|
|Datatransformering|Med dataströmsbearbetning kan du kombinera och utöka data från flera inkommande avsnitt i ett eller flera utdataämnen.|

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Snabbstart: Skapa Apache Kafka på HDInsight](apache-kafka-get-started.md)

* [Använda Kafka med REST proxy](rest-proxy.md)

* [Självstudie: Använda Apache Spark med Apache Kafka i HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Självstudie: Använda Apache Storm med Apache Kafka i HDInsight](../hdinsight-apache-storm-with-kafka.md)
