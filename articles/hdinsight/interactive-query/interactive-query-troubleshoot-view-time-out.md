---
title: Apache Hive Visa tids gränsen från frågeresultatet – Azure HDInsight
description: Apache Hive Visa tids gränsen vid hämtning av frågeresultat i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930730"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenario: Apache Hive Visa tids gränsen vid hämtning av frågeresultat i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör vissa frågor från vyn Apache Hive kan följande fel uppstå:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Orsak

Det är inte säkert att standardvärdet för timeout-värdet är lämpligt för den fråga som du kör. Den angivna tids perioden är för kort för att Hive-vyn ska hämta frågeresultatet.

## <a name="resolution"></a>Lösning

Öka tids gränsen för Apache Ambari Hive-vyn genom att ange följande egenskaper i `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Värdet för `HIVE_VIEW_INSTANCE_NAME` är tillgängligt i slutet av URL: en för Hive-vyn.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]