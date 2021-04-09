---
title: Apache Ambari-ANVÄNDARGRÄNSSNITTET visar värdar och tjänster i Azure HDInsight
description: Felsöka ett Apache Ambari UI-problem när du visar värdar och tjänster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943265"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari UI visar värdar och tjänster i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache Ambari UI är tillgängligt, men användar gränssnittet visar att nästan alla tjänster är inaktiverade, alla värdar som visar pulsslag förlorat.

## <a name="cause"></a>Orsak

I de flesta fall är detta ett problem med att Ambari-servern inte körs på den aktiva huvudnoden. Kontrol lera vilken huvudnoden som är aktiv huvudnoden och se till att Ambari-servern körs på rätt skrivare. Starta inte Ambari manuellt-Server, låt tjänsten för redundans vara ansvarig för att starta Ambari-servern på rätt huvudnoden. Starta om den aktiva huvudnoden för att framtvinga en redundansväxling.

Nätverks problem kan också orsaka det här problemet. Se om du kan pinga från varje klusternod `headnodehost` . Det finns en sällsynt situation där ingen klusternod kan ansluta till `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Lösning

Vanligt vis minimerar du det här problemet genom att starta om den aktiva huvudnoden. Kontakta HDInsight-supporten om du inte vill.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]