---
title: 'Snabb start: Apache HBase & Apache Phoenix – Azure HDInsight'
description: I den här snabb starten får du lära dig hur du använder Apache Phoenix i HDInsight. Lär dig också hur du installerar och konfigurerar SQLLine på datorn för att ansluta till ett HBase-kluster i HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935982"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Snabb start: fråga Apache HBase i Azure HDInsight med Apache Phoenix

I den här snabb starten får du lära dig hur du använder Apache Phoenix för att köra HBase-frågor i Azure HDInsight. Apache Phoenix är en SQL-frågemotor för Apache HBase. Den används som en JDBC-drivrutin, och gör att du kan ställa frågor och hantera HBase-tabeller med SQL. [SQLLine](http://sqlline.sourceforge.net/) är ett kommando rads verktyg för att köra SQL.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache HBase-kluster. Se [skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) för att skapa ett HDInsight-kluster.  Se till att du väljer kluster typen **HBase** .

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifiera en ZooKeeper-nod

När du ansluter till ett HBase-kluster måste du ansluta till en av de Apache ZooKeeper noderna. Varje HDInsight-kluster har tre ZooKeeper-noder. Du kan använda sväng för att snabbt identifiera en ZooKeeper-nod. Redigera spiral kommandot nedan genom att ersätta `PASSWORD` och `CLUSTERNAME` med relevanta värden, och ange sedan kommandot i en kommando tolk:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

En del av resultatet ser ut ungefär så här:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Anteckna värdet för `host_name` för senare användning.

## <a name="create-a-table-and-manipulate-data"></a>Skapa en tabell och manipulera data

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda Apache Phoenix för att skapa HBase-tabeller, infoga data och fråga efter data.

1. Använd `ssh` kommandot för att ansluta till ditt HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ändra katalog till Phoenix-klienten. Ange följande kommando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Starta [SQLLine](http://sqlline.sourceforge.net/). Redigera kommandot nedan genom att ersätta `ZOOKEEPER` med ZooKeeper-noden som identifierades tidigare. Ange sedan kommandot:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Skapa en HBase-tabell. Ange följande kommando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Använd kommandot SQLLine `!tables` för att visa alla tabeller i HBase. Ange följande kommando:

    ```sqlline
    !tables
    ```

6. Infoga värden i tabellen. Ange följande kommando:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Fråga tabellen. Ange följande kommando:

    ```sql
    SELECT * FROM Company;
    ```

8. Ta bort en post. Ange följande kommando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Ta bort tabellen. Ange följande kommando:

    ```hbase
    DROP TABLE Company;
    ```

10. Använd kommandot SQLLine `!quit` för att avsluta SQLLine. Ange följande kommando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Apache Phoenix för att köra HBase-frågor i Azure HDInsight. Om du vill veta mer om Apache Phoenix får du en djupare undersökning i nästa artikel.

> [!div class="nextstepaction"]
> [Apache Phoenix i HDInsight](../hdinsight-phoenix-in-hdinsight.md)
