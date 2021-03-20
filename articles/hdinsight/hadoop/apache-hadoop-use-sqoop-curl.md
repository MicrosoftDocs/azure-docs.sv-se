---
title: Använda sväng för att exportera data med Apache Sqoop i Azure HDInsight
description: Lär dig hur du skickar Sqoop-jobb från Apache till Azure HDInsight med hjälp av sväng.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 4de42bf30824fd71228aa27cc478a54ec3741da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928362"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Köra apache Sqoop-jobb i HDInsight med sväng

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder en sväng för att köra apache Sqoop-jobb på ett Apache Hadoop kluster i HDInsight. Den här artikeln visar hur du exporterar data från Azure Storage och importerar dem till en SQL Server-databas med hjälp av sväng. Den här artikeln är en fortsättning på [användning av Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

Sväng används för att demonstrera hur du kan interagera med HDInsight genom att använda RAW HTTP-begäranden för att köra, övervaka och hämta resultatet av Sqoop-jobb. Detta fungerar med hjälp av WebHCat-REST API (tidigare kallat Templeton) som tillhandahålls av ditt HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [konfiguration av test miljö](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) från att [använda Apache Sqoop med Hadoop i HDInsight](./hdinsight-use-sqoop.md).

* En klient för att fråga Azure SQL Database. Överväg att använda [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Sväng](https://curl.haxx.se/). Sväng är ett verktyg för att överföra data från eller till ett HDInsight-kluster.

* [JQ](https://stedolan.github.io/jq/). Verktyget JQ används för att bearbeta JSON-data som returneras från REST-begäranden.

* Bekant med Sqoop. Mer information finns i [användar handboken för Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Skicka Apache Sqoop-jobb med hjälp av sväng

Använd sväng för att exportera data med Apache Sqoop-jobb från Azure Storage till SQL Server.

> [!NOTE]  
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.

För kommandona i det här avsnittet ersätter du `USERNAME` med användaren att autentisera till klustret och ersätter `PASSWORD` med lösen ordet för användar kontot. Ersätt `CLUSTERNAME` med namnet på klustret.

REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Ange variablerna nedan för enkel användning. Det här exemplet baseras på en Windows-miljö och ändras efter behov för din miljö.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Du bör få ett svar som liknar följande:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Använd följande för att skicka ett Sqoop-jobb:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    De parametrar som används i det här kommandot är följande:

   * **-d** -eftersom `-G` inte används, begär standardvärdet post-metoden. `-d` anger de data värden som skickas med begäran.

       * **User.name** – användaren som kör kommandot.

       * **kommando** – det Sqoop-kommando som ska köras.

       * **statusdir** – den katalog som status för det här jobbet skrivs till.

     Det här kommandot returnerar ett jobb-ID som kan användas för att kontrol lera jobbets status.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Använd följande kommando för att kontrol lera jobbets status. Ersätt `JOBID` med värdet som returnerades i föregående steg. Om returvärdet t `{"id":"job_1415651640909_0026"}` `JOBID` . ex. är `job_1415651640909_0026` . Ändra plats efter `jq` behov.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Om jobbet har slutförts kommer statusen att **lyckas**.

   > [!NOTE]  
   > Denna förfrågan returnerar ett JavaScript Object Notation (JSON)-dokument med information om jobbet. JQ används för att hämta endast State-värdet.

1. När jobbets tillstånd har ändrats till **lyckades** kan du hämta resultatet från Azure Blob Storage. Den `statusdir` parameter som skickades med frågan innehåller platsen för utdatafilen, i det här fallet `wasb:///example/data/sqoop/curl` . Den här adressen lagrar utdata från jobbet i `example/data/sqoop/curl` katalogen på den standard lagrings behållare som används av ditt HDInsight-kluster.

    Du kan använda Azure Portal för att komma åt stderr-och stdout-blobbar.

1. Verifiera att data har exporter ATS genom att använda följande frågor från din SQL-klient för att Visa exporterade data:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Begränsningar

* Mass export – med Linux-baserat HDInsight har Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database för närvarande inte stöd för Mass infogningar.
* Batchering – med Linux-baserat HDInsight, när du använder `-batch` växeln när du utför infogningar, kommer Sqoop att utföra flera infogningar i stället för att batchen infoga åtgärder.

## <a name="summary"></a>Sammanfattning

Som det visas i det här dokumentet kan du använda en RAW HTTP-begäran för att köra, övervaka och visa resultaten av Sqoop-jobb i ditt HDInsight-kluster.

Mer information om det REST-gränssnitt som används i den här artikeln finns <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">REST API guide för Apache-Sqoop</a>.

## <a name="next-steps"></a>Nästa steg

[Använda Apache Sqoop med Apache Hadoop på HDInsight](hdinsight-use-sqoop.md)

För andra HDInsight-artiklar som rör sväng:

* [Skapa Apache Hadoop kluster med Azure-REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Kör Apache Hive frågor med Apache Hadoop i HDInsight med REST](apache-hadoop-use-hive-curl.md)
* [Kör MapReduce-jobb med Apache Hadoop på HDInsight med REST](apache-hadoop-use-mapreduce-curl.md)
