---
title: Problem med pulsslag från Apache Ambari i Azure HDInsight
description: Granskning av olika orsaker till problem med Apache Ambari-pulsslag i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946739"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problem med pulsslag från Apache Ambari i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="scenario-high-cpu-utilization"></a>Scenario: hög processor användning

### <a name="issue"></a>Problem

Ambari-agenten har hög processor användning, vilket resulterar i aviseringar från Ambari-ANVÄNDARGRÄNSSNITTET som för vissa noder går Ambari agent-pulsslag förlorat. Aviseringen om förlorat pulsslag är vanligt vis tillfällig.

### <a name="cause"></a>Orsak

På grund av olika Ambari-buggar, i sällsynta fall, kan din Ambari-agent ha hög (nära 100) processor användning i procent.

### <a name="resolution"></a>Lösning

1. Identifiera process-ID (PID) för Ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Kör sedan följande kommando för att visa processoranvändningen:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Starta om Ambari – agent för att undvika problem:

    ```bash
    service ambari-agent restart
    ```

1. Om starta om inte fungerar, avsluta Ambari-agent-processen och starta sedan den:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agenten har inte startat

### <a name="issue"></a>Problem

Ambari-agenten har inte startat som resulterar i aviseringar från Ambari-gränssnittet för vissa noder. Ambari agent-pulsslag förloras.

### <a name="cause"></a>Orsak

Aviseringarna orsakas av att Ambari-agenten inte körs.

### <a name="resolution"></a>Lösning

1. Bekräfta status för Ambari-agent:

    ```bash
    service ambari-agent status
    ```

1. Bekräfta om tjänster för redundans körs:

    ```bash
    ps -ef | grep failover
    ```

    Om tjänster för redundansväxling inte körs beror det förmodligen på ett problem som förhindrar att HDInsight-agenten startar redundansväxling. Kontrol lera HDInsight-agentens logg från `/var/log/hdinsight-agent/hdinsight-agent.out` filen.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: pulsslag förlorat för Ambari

### <a name="issue"></a>Problem

Ambari pulsslags agent förlorades.

### <a name="cause"></a>Orsak

OMS-loggar orsakar hög CPU-användning.

### <a name="resolution"></a>Lösning

* Inaktivera Azure Monitor loggning med hjälp av PowerShell-cmdleten [disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Ta bort `mdsd.warn` logg filen

---

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]