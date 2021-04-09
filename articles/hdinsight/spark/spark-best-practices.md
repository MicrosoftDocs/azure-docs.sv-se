---
title: Apache Spark rikt linjer på Azure HDInsight
description: Lär dig rikt linjer för att använda Apache Spark i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938817"
---
# <a name="apache-spark-guidelines"></a>Apache Spark rikt linjer

Den här artikeln innehåller olika rikt linjer för att använda Apache Spark på Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hur gör jag för att köra eller skicka Spark-jobb?

| Alternativ | Dokument |
|---|---|
| VSCode | [Använda Spark & Hive-verktyg för Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Självstudie: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Självstudie: använda Azure Toolkit for IntelliJ för att skapa Apache Spark program för ett HDInsight-kluster](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Självstudie: Skapa ett Scala Maven-program för Apache Spark i HDInsight med hjälp av IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Fjärrjobbet skickas med livy | [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie är ett arbets flödes-och samordnings system som hanterar Hadoop-jobb.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Du kan använda livy för att köra interaktiva Spark-gränssnitt eller skicka batch-jobb som ska köras i Spark.|
|[Azure Data Factory för Apache Spark](../../data-factory/transform-data-using-spark.md)|Spark-aktiviteten i en Data Factory pipeline kör ett Spark-program på ditt eget eller [HDInsight-kluster på begäran.|
|[Azure Data Factory för Apache Hive](../../data-factory/transform-data-using-hadoop-hive.md)|HDInsight Hive-aktiviteten i en Data Factory pipeline kör Hive-frågor på ditt eget eller på begäran HDInsight-kluster.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hur gör jag för att övervaka och felsöka Spark-jobb?

| Alternativ | Dokument |
|---|---|
| Azure Toolkit for IntelliJ | [Fel Spark fel sökning av jobb med Azure Toolkit for IntelliJ (för hands version)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ via SSH | [Felsöka Apache Spark-program lokalt eller via fjärranslutning i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ via VPN | [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning i HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Jobb diagram på Apache Spark historik Server | [Använda utökad Apache Spark-historikserver för att felsöka och diagnostisera Apache Spark-program](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hur gör jag för att gör mina Spark-jobb effektivare att köra?

| Alternativ | Dokument |
|---|---|
| IO-cache | [Förbättra prestanda för Apache Spark arbets belastningar med Azure HDInsight IO-cache (för hands version)](./apache-spark-improve-performance-iocache.md) |
| Konfigurationsalternativ | [Optimera Apache Spark-jobb](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hur gör jag för att ansluta till andra Azure-tjänster?

| Alternativ | Dokument |
|---|---|
| Apache Hive på HDInsight | [Integrera Apache Spark och Apache Hive med Hive-lagrets koppling](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase på HDInsight | [Använda Apache Spark för att läsa och skriva Apache HBase-data](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka på HDInsight | [Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Synapse Link för Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Vad är mina lagrings alternativ?

| Alternativ | Dokument |
|---|---|
| Azure Data Lake Storage Gen2 | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Använda Azure Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Använda Azure-lagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)
* [Optimera Apache Spark jobb i HDInsight](apache-spark-perf.md)
