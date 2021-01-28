---
title: Felsöka Hive med Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med Apache Hive och Azure HDInsight.
keywords: Azure HDInsight, Hive, vanliga frågor, fel söknings guide, vanliga frågor
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a76974b8daddf225b0d9b8b581475d657322847e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931449"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Felsöka Apache Hive med Azure HDInsight

Lär dig mer om de viktigaste frågorna och deras lösningar när du arbetar med Apache Hive nytto laster i Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hur gör jag för att exportera ett Hive-metaarkiv och importera det till ett annat kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Anslut till HDInsight-klustret med hjälp av en SSH-klient (Secure Shell). Mer information finns i [Ytterligare läsning](#additional-reading-end).

2. Kör följande kommando på det HDInsight-kluster som du vill exportera metaarkiv från:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Det här kommandot skapar en fil med namnet allatables. SQL.

3. Kopiera filen alltables. SQL till det nya HDInsight-klustret och kör sedan följande kommando:

    ```apache
    hive -f alltables.sql
    ```

Koden i lösnings stegen förutsätter att data Sök vägar på det nya klustret är desamma som data Sök vägarna i det gamla klustret. Om data Sök vägarna skiljer sig åt kan du redigera den genererade `alltables.sql` filen manuellt för att avspegla eventuella ändringar.

### <a name="additional-reading"></a>Mer att läsa

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hur gör jag för att hitta Hive-loggar i ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Anslut till HDInsight-klustret med hjälp av SSH. Mer information finns i **Ytterligare läsning**.

2. Om du vill visa Hive-klient loggar använder du följande kommando:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Använd följande kommando för att Visa Hive-metaarkiv loggar:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Om du vill visa Hive-Server loggar använder du följande kommando:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Mer att läsa

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hur gör jag för att starta Hive-gränssnittet med vissa konfigurationer i ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Ange ett konfigurations nyckel par när du startar Hive-gränssnittet. Mer information finns i [Ytterligare läsning](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Om du vill visa en lista över alla effektiva konfigurationer i Hive-gränssnittet använder du följande kommando:

   ```apache
   hive> set;
   ```

   Använd till exempel följande kommando för att starta Hive-gränssnittet med fel söknings loggning aktive rad i-konsolen:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Mer att läsa

- [Konfigurations egenskaper för Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hur gör jag för att analysera Apache Tez DAG-data på en kluster kritisk väg?

### <a name="resolution-steps"></a>Lösningssteg

1. Om du vill analysera en Apache-Tez riktad acykliska graf (DAG) i ett kluster kritiskt diagram ansluter du till HDInsight-klustret med hjälp av SSH. Mer information finns i [Ytterligare läsning](#additional-reading-end).

2. Kör följande kommando i en kommandotolk:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Om du vill visa andra analys verktyg som kan användas för att analysera Tez DAG använder du följande kommando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Du måste ange ett exempel program som det första argumentet.

   Giltiga program namn är:
    - **ContainerReuseAnalyzer**: åter användnings information för utskrifts behållare på dag
    - **CriticalPath**: hitta den kritiska sökvägen för en dag
    - **LocalityAnalyzer**: skriva ut information om den lokala datorn på en dag
    - **ShuffleTimeAnalyzer**: analysera informationen om den blandade tiden på en dag
    - **SkewAnalyzer**: analysera skevningen-informationen på en dag
    - **SlowNodeAnalyzer**: skriva ut Node-information på en dag
    - **SlowTaskIdentifier**: skriva ut information om långsamma uppgifter på en dag
    - **SlowestVertexAnalyzer**: skriva ut långsammast hörn Detaljer på en dag
    - **SpillAnalyzer**: information om att skriva ut spill på en dag
    - **TaskConcurrencyAnalyzer**: Skriv ut aktivitetens samtidighets information på en dag
    - **VertexLevelCriticalPathAnalyzer**: hitta den kritiska vägen vid hörn nivå på en dag

### <a name="additional-reading"></a>Mer att läsa

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hur gör jag för att hämta Tez DAG-data från ett kluster?

#### <a name="resolution-steps"></a>Lösningssteg

Det finns två sätt att samla in Tez DAG-data:

- Från kommandoraden:

    Anslut till HDInsight-klustret med hjälp av SSH. Kör följande kommando i kommando tolken:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Använd Ambari Tez-vyn:

  1. Gå till Ambari.
  2. Gå till Tez-vyn (under Panel ikonen i det övre högra hörnet).
  3. Välj den DAG som du vill visa.
  4. Välj **Hämta data**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Mer att läsa

[Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]