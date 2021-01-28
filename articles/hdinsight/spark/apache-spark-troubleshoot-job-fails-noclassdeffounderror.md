---
title: NoClassDefFoundError – Apache Spark med Apache Kafka data i Azure HDInsight
description: Apache Spark strömnings jobb som läser data från ett Apache Kafka-kluster Miss lyckas med en NoClassDefFoundError i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946379"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark strömmande jobb som läser Apache Kafkas data Miss lyckas med NoClassDefFoundError i HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Spark-klustret kör ett Spark streaming-jobb som läser data från ett Apache Kafka-kluster. Spark streaming-jobbet Miss lyckas om Kafka Stream-komprimeringen är aktive rad. I det här fallet misslyckades Spark streaming-appen application_1525986016285_0193 på grund av ett fel:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Orsak

Felet kan bero på att du anger en annan version av `spark-streaming-kafka` jar-filen än den version av Kafka-klustret som du kör.

Om du till exempel kör en Kafka kluster version 0.10.1, kommer följande kommando att resultera i ett fel:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Lösning

Använd kommandot Spark-Submit med `–packages` alternativet och se till att den version av Kafka jar-filen för Spark-direktuppspelning är samma som den version av Kafka-klustret som du kör.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]