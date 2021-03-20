---
title: Kopiera data mellan Data Lake Storage Gen1 och Azure SQL-Sqoop | Microsoft Docs
description: Använd Sqoop för att kopiera data mellan Azure SQL Database och Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109195"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopiera data mellan Data Lake Storage Gen1 och Azure SQL Database med Sqoop

Lär dig hur du använder Apache Sqoop för att importera och exportera data mellan Azure SQL Database och Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Vad är Sqoop?

Big data program är ett naturligt val för bearbetning av ostrukturerade och delvis strukturerade data, till exempel loggar och filer. Du kan dock också ha ett behov av att bearbeta strukturerade data som lagras i Relations databaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) är ett verktyg som utformats för att överföra data mellan Relations databaser och en stor data lagrings plats, till exempel data Lake Storage gen1. Du kan använda den för att importera data från ett relationellt databas hanterings system (RDBMS) som Azure SQL Database till Data Lake Storage Gen1. Du kan sedan omvandla och analysera data med hjälp av Big data-arbetsbelastningar och sedan exportera data tillbaka till en RDBMS. I den här artikeln använder du en databas i Azure SQL Database som Relations databas för att importera/exportera från.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar kontot finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage gen1-konto. Se [skapa ett HDInsight-kluster med data Lake Storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Den här artikeln förutsätter att du har ett HDInsight Linux-kluster med Data Lake Storage Gen1 åtkomst.
* **Azure SQL Database**. Instruktioner för hur du skapar en databas i Azure SQL Database finns i [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Skapa exempel tabeller i databasen

1. Starta genom att skapa två exempel tabeller i databasen. Använd [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) eller Visual Studio för att ansluta till databasen och kör sedan följande frågor.

    **Skapa Table1**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Skapa tabell2**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. Kör följande kommando för att lägga till några exempel data i **Table1**. Lämna **tabell2** tomt. Senare ska du importera data från **Table1** till data Lake Storage gen1. Sedan exporterar du data från Data Lake Storage Gen1 till **tabell2**.

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Använda Sqoop från ett HDInsight-kluster med åtkomst till Data Lake Storage Gen1

Det finns redan Sqoop-paket tillgängliga för det An-HDInsight klustret. Om du har konfigurerat HDInsight-klustret så att det använder Data Lake Storage Gen1 som ytterligare lagrings utrymme kan du använda Sqoop (utan konfigurations ändringar) för att importera/exportera data mellan en Relations databas som Azure SQL Database och ett Data Lake Storage Gen1-konto.

1. I den här artikeln förutsätter vi att du har skapat ett Linux-kluster så att du kan använda SSH för att ansluta till klustret. Se [ansluta till ett Linux-baserat HDInsight-kluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Kontrol lera om du kan komma åt Data Lake Storage Gen1 kontot från klustret. Kör följande kommando från SSH-prompten:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Det här kommandot innehåller en lista över filer/mappar i Data Lake Storage Gen1-kontot.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importera data från Azure SQL Database till Data Lake Storage Gen1

1. Navigera till katalogen där Sqoop-paket är tillgängliga. Normalt är den här platsen `/usr/hdp/<version>/sqoop/bin` .

1. Importera data från **Table1** till data Lake Storage gen1-kontot. Använd följande syntax:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   Plats hållaren **SQL-Database-Server-Name** representerar namnet på den server där databasen körs. **SQL-databasens namn** plats hållare representerar det faktiska databas namnet.

   Exempel:

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Verifiera att data har överförts till Data Lake Storage Gen1-kontot. Kör följande kommando:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   Du bör se följande utdata.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Varje **del-m-_-** fil motsvarar en rad i käll tabellen, _ *Table1 * *. Du kan visa innehållet i de del-m-* filer som ska verifieras.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportera data från Data Lake Storage Gen1 till Azure SQL Database

1. Exportera data från Data Lake Storage Gen1-kontot till den tomma tabellen **tabell2**, i Azure SQL Database. Använd följande syntax.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Exempel:

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Verifiera att data har överförts till SQL Database tabellen. Använd [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) eller Visual Studio för att ansluta till Azure SQL Database och kör sedan följande fråga.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Kommandot ska ha följande utdata.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Prestanda överväganden vid användning av Sqoop

Information om prestanda justering av Sqoop-jobbet för att kopiera data till Data Lake Storage Gen1 finns i [blogg inlägget Sqoop Performance](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

## <a name="next-steps"></a>Nästa steg

* [Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)