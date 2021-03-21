---
title: Apache Hive Zeppelin-tolken fel – Azure HDInsight
description: Apache Zeppelin Hive JDBC-tolken pekar på fel URL i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930745"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenario: Apache Hive Zeppelin-tolken ger ett Zookeeper-fel i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I ett Apache Hive LLAP-kluster ger Zeppelin-tolken följande fel meddelande vid försök att köra en fråga:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Orsak

Zeppelin Hive JDBC-tolken pekar på fel URL.

## <a name="resolution"></a>Lösning

1. Navigera till sammanfattningen av Hive-komponenten och kopiera "Hive JDBC URL" till Urklipp.

1. Navigera till `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Redigera inställningarna för JDBC: uppdatera värdet Hive. URL till Hive-URL: en som kopierades i steg 1

1. Spara och försök sedan köra frågan igen

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]