---
title: RpcTimeoutException för Apache Spark Thrift – Azure HDInsight
description: Du ser 502 fel vid bearbetning av stora data mängder med hjälp av Apache Spark Thrift-Server
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932707"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException för Apache Spark Thrift-server i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Spark-programmet Miss lyckas med ett `org.apache.spark.rpc.RpcTimeoutException` undantag och ett meddelande: `Futures timed out` , som i följande exempel:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` och `overhead limit exceeded` fel kan också visas i `sparkthriftdriver.log` som i följande exempel:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Orsak

Dessa fel orsakas av brist på minnes resurser under data bearbetning. Om Java-insamlings processen startar kan det leda till att Spark-programmet slutar svara. Frågorna börjar ta slut och stoppa bearbetningen. `Futures timed out`Felet indikerar ett kluster under allvarlig stress.

## <a name="resolution"></a>Lösning

Öka kluster storleken genom att lägga till fler arbetsnoder eller öka minnes kapaciteten för de befintliga klusternoderna. Du kan också justera data pipelinen för att minska mängden data som bearbetas samtidigt.

`spark.network.timeout`Kontrollerar tids gränsen för alla nätverks anslutningar. Om du ökar nätverks tids gränsen kan det ta längre tid för vissa kritiska åtgärder att slutföras, men problemet löses inte helt och hållet.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]