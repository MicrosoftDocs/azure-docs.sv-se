---
title: Azure Monitor loggar för Apache Kafka – Azure HDInsight
description: Lär dig hur du använder Azure Monitor loggar för att analysera loggar från Apache Kafka-kluster på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 51301bd38bf0700ce42ef33a47b9e763da8d4ed6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595292"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka i HDInsight

Lär dig hur du använder Azure Monitor loggar för att analysera loggar som genereras av Apache Kafka i HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Plats för loggar

Apache Kafka loggar i klustret finns på `/var/log/kafka` . Kafka-loggar sparas eller bevaras inte över klustrets livs cykel, oavsett om hanterade diskar används. I följande tabell visas de tillgängliga loggarna.

|Loggas |Description |
|---|---|
|Kafka. ut|STDOUT och stderr för Kafka-processen. Du hittar Kafka start-och avslutnings loggar i den här filen.|
|Server. log|Huvud server loggen för Kafka. Alla Kafka Broker-loggar slutförs här.|
|kontrollant. log|Styrenhets loggar om Broker fungerar som kontrollant.|
|statechange. log|Alla tillstånds ändrings händelser till mäklare loggas i den här filen.|
|Kafka-GC. log|Kafka skräp insamlings statistik.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Aktivera Azure Monitor loggar för Apache Kafka

Stegen för att aktivera Azure Monitor loggar för HDInsight är desamma för alla HDInsight-kluster. Använd följande länkar om du vill veta hur du skapar och konfigurerar nödvändiga tjänster:

1. Skapa en Log Analytics-arbetsyta. Mer information finns i [loggarna i Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) -dokument.

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i dokumentet [starta med Apache Kafka på HDInsight](apache-kafka-get-started.md) .

3. Konfigurera Kafka-klustret så att det använder Azure Monitor loggar. Mer information finns i avsnittet [använda Azure Monitor loggar för att övervaka HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) -dokument.

> [!IMPORTANT]  
> Det kan ta ungefär 20 minuter innan data är tillgängliga för Azure Monitor loggar.

## <a name="query-logs"></a>Fråga efter loggar

1. Välj arbets ytan Log Analytics från [Azure Portal](https://portal.azure.com).

2. I den vänstra menyn under **Allmänt** väljer du **loggar**. Härifrån kan du söka i data som samlas in från Kafka. Ange en fråga i frågefönstret och välj sedan **Kör**. Följande är några exempel på sökningar:

* Disk användning:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-användning:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Inkommande meddelanden per sekund: (Ersätt `your_kafka_cluster_name` med ditt kluster namn.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Inkommande byte per sekund: (Ersätt `wn0-kafka` med en arbetsnodens värd namn.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Utgående byte per sekund: (Ersätt `your_kafka_cluster_name` med ditt kluster namn.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Du kan också ange `*` om du vill söka efter alla typer som loggas. För närvarande finns följande loggar för frågor:

    | Loggtyp | Description |
    | ---- | ---- |
    | Logga \_ kafkaserver \_ cl | Kafka Broker-Server. log |
    | Logga \_ kafkacontroller \_ cl | Kafka Broker-styrenhet. log |
    | mått \_ Kafka \_ cl | Kafka JMX-mått |

    ![Apache Kafka Log Analytics CPU-användning](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Monitor finns i [Azure Monitor översikt](../../azure-monitor/overview.md)och [fråga Azure Monitor loggar för att övervaka HDInsight-kluster](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Mer information om hur du arbetar med Apache Kafka finns i följande dokument:

* [Spegla Apache Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
* [Öka skalningen för Apache Kafka i HDInsight](apache-kafka-scalability.md)
* [Använda Apache Spark strömning (DStreams) med Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Använd Apache Spark strukturerad strömning med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)