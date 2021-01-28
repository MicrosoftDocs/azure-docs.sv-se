---
title: Fel meddelande visas inte i Apache Hive Visa – Azure HDInsight
description: Frågan Miss lyckas i Apache Hive View utan någon information om Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931000"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: fråga om fel meddelande visas inte i Apache Hive visning i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem vid användning av interaktiva frågekomponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Fel meddelandet Apache Hive för att visa frågan ser ut ungefär så här, utan ytterligare information:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Orsak

Ibland kan fel meddelandet om ett frågefel vara för stort för att visas på huvud sidan för Hive-vyn.

## <a name="resolution"></a>Lösning

Kontrol lera fliken meddelanden i det övre högra hörnet i Hive_view för att se hela stacktrace och fel meddelandet.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]