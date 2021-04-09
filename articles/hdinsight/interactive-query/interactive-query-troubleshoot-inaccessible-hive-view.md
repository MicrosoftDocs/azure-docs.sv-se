---
title: Apache Hive anslutningar till Apache Zookeeper – Azure HDInsight
description: Apache Hive vyn är inte tillgänglig på grund av problem med Apache Zookeeper i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939301"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scenario: Apache Hive inte upprätta en anslutning till Apache Zookeeper i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Det går inte att komma åt Hive-vyn och loggarna i `/var/log/hive` visar ett fel som liknar följande:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Orsak

Det är möjligt att Hive inte kan upprätta en anslutning till Zookeeper, vilket förhindrar att Hive-vyn startas.

## <a name="resolution"></a>Lösning

1. Kontrol lera att Zookeeper-tjänsten är felfri.

1. Kontrol lera om Zookeeper-tjänsten har en ZNode-post för Hive Server2. Värdet saknas eller är felaktigt.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Starta om Zookeeper-noderna och starta om HiveServer2 för att återupprätta anslutningen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]