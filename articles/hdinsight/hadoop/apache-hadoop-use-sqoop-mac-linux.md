---
title: Apache Sqoop med Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder Apache Sqoop för att importera och exportera mellan Apache Hadoop på HDInsight och Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943149"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Använd Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och Azure SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop för att importera och exportera mellan ett Apache Hadoop kluster i Azure HDInsight och Azure SQL Database eller Microsoft SQL Server. Stegen i det här dokumentet använder `sqoop` kommandot direkt från huvudnoden i Hadoop-klustret. Du kan använda SSH för att ansluta till Head-noden och köra kommandona i det här dokumentet. Den här artikeln är en fortsättning på [användning av Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Förutsättningar

* Slutför [konfiguration av test miljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från att [använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bekant med Sqoop. Mer information finns i [användar handboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Konfigurera

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ange variabler för enkel användning. Ersätt `PASSWORD` , `MYSQLSERVER` och `MYDATABASE` med relevanta värden, och ange sedan följande kommandon:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop-export

Från Hive till SQL.

1. Kontrol lera att Sqoop kan se din databas genom att ange kommandot nedan i den öppna SSH-anslutningen. Det här kommandot returnerar en lista över databaser.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Ange följande kommando för att visa en lista över tabeller för den angivna databasen:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Om du vill exportera data från Hive `hivesampletable` -tabellen till `mobiledata` tabellen i databasen anger du kommandot nedan i den öppna ssh-anslutningen:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Verifiera att data har exporter ATS genom att använda följande frågor från SSH-anslutningen för att Visa exporterade data:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop-import

Från SQL till Azure Storage.

1. Ange kommandot nedan i den öppna SSH-anslutningen för att importera data från `mobiledata` tabellen i SQL till `wasbs:///tutorials/usesqoop/importeddata` katalogen i HDInsight. Fälten i data skiljs åt av ett tabbtecken och raderna avslutas med ett nytt rad-värde.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Du kan också ange en Hive-tabell:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. När importen har slutförts anger du följande kommando i den öppna SSH-anslutningen för att skapa en lista över data i den nya katalogen:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Använd [Beeline](./apache-hadoop-use-hive-beeline.md) för att kontrol lera att tabellen har skapats i Hive.

    1. Ansluta

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Kör varje fråga en i taget och granska utdata:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Avsluta Beeline med `!exit` .

## <a name="limitations"></a>Begränsningar

* Mass export – med Linux-baserat HDInsight har Sqoop-anslutningsprogrammet som används för att exportera data till SQL inte stöd för Mass infogningar.

* Batching – med Linux-baserat HDInsight, och när du använder `-batch` växeln när du utför tillägg, gör Sqoop flera infogningar i stället för att batch-sätta in åtgärderna.

## <a name="important-considerations"></a>Att tänka på

* Både HDInsight och SQL Server måste finnas på samma Azure-Virtual Network.

    Ett exempel finns i [ansluta HDInsight till ditt lokala nätverks](./../connect-on-premises-network.md) dokument.

    Mer information om hur du använder HDInsight med ett Azure-Virtual Network finns i avsnittet [utöka HDInsight med azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) Document. Mer information om Azure Virtual Network finns i [Virtual Network översikts](../../virtual-network/virtual-networks-overview.md) dokument.

* SQL Server måste konfigureras för att tillåta SQL-autentisering. Mer information finns i dokumentet [Välj ett autentiseringsläge](/sql/relational-databases/security/choose-an-authentication-mode) .

* Du kan behöva konfigurera SQL Server för att godkänna fjärr anslutningar. Mer information finns i [fel sökning av anslutning till SQL Server databas motor](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokument.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du använder Sqoop. Mer information finns i:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie-linux-mac.md): Använd Sqoop-åtgärd i ett Oozie-arbetsflöde.
* [Analysera flyg fördröjnings data med HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Använd interaktiv fråga för att analysera flyg fördröjnings data och Använd sedan Sqoop för att exportera data till en databas i Azure.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): hitta andra metoder för att ladda upp data till HDInsight/Azure Blob Storage.