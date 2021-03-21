---
title: Vad är en interaktiv fråga i Azure HDInsight?
description: En introduktion till interaktiv fråga, även kallat Apache Hive LLAP, i Azure HDInsight
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 2813554700e015c0ac34e47d632d16d97c948c4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941088"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Vad är interaktiv fråga i Azure HDInsight?

Interaktiv fråga (kallas även Apache Hive LLAP eller [analytisk bearbetning av låg latens](https://cwiki.apache.org/confluence/display/Hive/LLAP)) är en [kluster typ](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)för Azure HDInsight. Interaktiv fråga stöder cachelagring i minnet, vilket gör Apache Hive frågor snabbare och mycket mer interaktivt. Kunder använder interaktiv fråga för att fråga data som lagras i Azure Storage & Azure Data Lake Storage på ett snabbt sätt. Interaktiv fråga gör det enkelt för utvecklare och data expert att arbeta med stora data med BI-verktyg som de älskar mest. En interaktiv HDInsight-fråga stöder flera verktyg för att få åtkomst till Big data på ett enkelt sätt.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Ett interaktivt Query-kluster skiljer sig från ett Apache Hadoop kluster. Den innehåller bara Hive-tjänsten.

Du kan bara komma åt Hive-tjänsten i det interaktiva fråge klustret via Apache Ambari Hive-vyn, Beeline och Microsoft Hive Open Database Connectivity-drivrutinen (Hive ODBC). Du kan inte komma åt den via Hive-konsolen, Templeton, den klassiska Azure-CLI: en eller Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Skapa ett interaktivt Query-kluster

Information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj kluster typen interaktiv fråga.

> [!IMPORTANT]
> Den minsta huvudnoden storleken för interaktiva fråge kluster är Standard_D13_v2. Mer information finns i [diagrammet storleks schema för virtuell Azure-dator](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Köra Apache Hive frågor från interaktiv fråga

Om du vill köra Hive-frågor kan du välja mellan följande alternativ:

|Metod |Beskrivning |
|---|---|
|Microsoft Power BI|Se [visualisera interaktiva frågor Apache Hive data med Power BI i Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)och [visualisera big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Se [ansluta till Azure HDInsight och köra apache Hive frågor med hjälp av data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visuell Studio-kod|Se [använda Visual Studio Code för Apache Hive, LLAP eller pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari Hive-vy|Se [använda Apache Hive vy med Apache Hadoop i Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Hive-vyn är inte tillgänglig för HDInsight 4,0.|
|Apache Beeline|Se [använda Apache Hive med Apache Hadoop i HDInsight med Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Du kan använda Beeline antingen från Head-noden eller från en tom Edge-nod. Vi rekommenderar att du använder Beeline från en tom Edge-nod. Information om hur du skapar ett HDInsight-kluster med hjälp av en tom Edge-nod finns i [använda tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).|
|Hive ODBC|Se [ansluta Excel till Apache Hadoop med Microsoft HIVE ODBC-drivrutin](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Så här hittar du anslutnings strängen för Java Database Connectivity (JDBC):

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` , där `CLUSTERNAME` är namnet på klustret.
1. Om du vill kopiera URL: en väljer du urklipps ikonen:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar interaktiva Query-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Lär dig hur du kan [visualisera Big data med Power BI i Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Lär dig hur du [använder Apache Zeppelin för att köra apache Hive frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
