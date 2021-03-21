---
title: Använd Apache Spark för att läsa och skriva data till Azure SQL Database
description: Lär dig hur du konfigurerar en anslutning mellan HDInsight Spark-kluster och Azure SQL Database. Läsa data, skriva data och strömma data till en SQL-databas
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 4d42cedbc5dc20c929703be106e732b4806f3902
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940605"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Använd HDInsight Spark-kluster för att läsa och skriva data till Azure SQL Database

Lär dig hur du ansluter ett Apache Spark kluster i Azure HDInsight med Azure SQL Database. Läs, skriv och strömma data till SQL-databasen. Anvisningarna i den här artikeln använder en Jupyter Notebook för att köra Scala-kodfragment. Du kan dock skapa ett fristående program i Scala eller python och utföra samma uppgifter.

## <a name="prerequisites"></a>Förutsättningar

* Azure HDInsight Spark kluster.  Följ instruktionerna i [skapa ett Apache Spark kluster i HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Följ instruktionerna i [skapa en databas i Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Se till att du skapar en databas med exemplet **AdventureWorksLT** schema och data. Se också till att du skapar en brand Väggs regel på server nivå för att tillåta att klientens IP-adress får åtkomst till SQL-databasen. Anvisningarna för att lägga till brand Väggs regeln finns i samma artikel. När du har skapat SQL-databasen ser du till att du behåller följande värden. Du behöver dem för att kunna ansluta till databasen från ett Spark-kluster.

    * Server namn.
    * Databas namn.
    * Azure SQL Database administratörens användar namn/lösen ord.

* SQL Server Management Studio (SSMS). Följ anvisningarna i [använda SSMS för att ansluta och fråga efter data](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter Notebook

Börja med att skapa en Jupyter Notebook som är associerad med Spark-klustret. Du använder den här antecknings boken för att köra kodfragment som används i den här artikeln.

1. Öppna klustret från [Azure Portal](https://portal.azure.com/).
1. Välj **Jupyter Notebook** under **kluster instrument paneler** på höger sida.  Om du inte ser **kluster instrument paneler** väljer du **Översikt** på den vänstra menyn. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    ![Jupyter Notebook på Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter Notebook i Spark")

   > [!NOTE]  
   > Du kan också komma åt Jupyter Notebook i Spark-kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. I Jupyter Notebook, i det övre högra hörnet, klickar du på **nytt** och sedan på **Spark** för att skapa en Scala Notebook. Jupyter-anteckningsböcker i HDInsight Spark-kluster tillhandahåller även **PySpark** -kärnan för Python2-program och **PySpark3** -kärnan för python3-program. I den här artikeln skapar vi en Scala Notebook.

    ![Kernels för Jupyter Notebook i Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels för Jupyter Notebook i Spark")

    Mer information om kernelerna finns i [använda Jupyter Notebook kernels med Apache Spark kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > I den här artikeln använder vi en spark-kernel (Scala) eftersom strömning av data från Spark till SQL Database bara stöds i Scala och Java. Även om du kan läsa från och skriva till SQL kan du göra det med hjälp av python, för konsekvens i den här artikeln använder vi Scala för alla tre åtgärderna.

1. En ny antecknings bok öppnas med ett standard namn ( **Namnlös**). Klicka på antecknings bokens namn och ange önskat namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för anteckningsboken")

Nu kan du börja skapa ditt program.

## <a name="read-data-from-azure-sql-database"></a>Läs data från Azure SQL Database

I det här avsnittet ska du läsa data från en tabell (till exempel **SalesLT. address**) som finns i AdventureWorks-databasen.

1. I en kod cell i en ny Jupyter Notebook klistrar du in följande kodfragment och ersätter plats hållar värden med värdena för din databas.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Använd kodfragmentet nedan för att bygga en JDBC-URL som du kan skicka till Spark dataframe-API: erna. Koden skapar ett- `Properties` objekt som ska innehålla parametrarna. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Använd kodfragmentet nedan för att skapa en dataframe med data från en tabell i databasen. I det här kodfragmentet använder vi en `SalesLT.Address` tabell som är tillgänglig som en del av **AdventureWorksLT** -databasen. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Nu kan du utföra åtgärder på dataframe, till exempel hämta data schemat:

    ```scala
    sqlTableDF.printSchema
    ```

    Du ser utdata som liknar följande bild:

    ![schemats utdata](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schemats utdata")

1. Du kan också utföra åtgärder, t. ex. Hämta de 10 översta raderna.

    ```scala
    sqlTableDF.show(10)
    ```

1. Du kan också hämta vissa kolumner från data uppsättningen.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Skriv in data i Azure SQL Database

I det här avsnittet använder vi en exempel-CSV-fil som är tillgänglig i klustret för att skapa en tabell i databasen och fylla den med data. Exempel-CSV-filen (**HVAC.csv**) är tillgänglig i alla HDInsight-kluster på `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. I en kod cell i en ny Jupyter Notebook klistrar du in följande kodfragment och ersätter plats hållar värden med värdena för din databas.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.  

1. Följande fragment skapar en JDBC-URL som du kan skicka till Spark dataframe-API: erna. Koden skapar ett- `Properties` objekt som ska innehålla parametrarna. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Använd följande kodfragment för att extrahera schemat för data i HVAC.csv och använda schemat för att läsa in data från CSV-filen i en dataframe `readDf` . Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Använd `readDf` dataframe för att skapa en tillfällig tabell `temphvactable` . Använd sedan den temporära tabellen för att skapa en Hive-tabell `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Använd slutligen Hive-tabellen för att skapa en tabell i databasen. Följande fragment skapas `hvactable` i Azure SQL Database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Anslut till Azure SQL Database med SSMS och kontrol lera att du ser där `dbo.hvactable` .

    a. Starta SSMS och Anslut till Azure SQL Database genom att tillhandahålla anslutnings information som visas på skärm bilden nedan.

    ![Ansluta till SQL Database med SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Ansluta till SQL Database med SSMS1")

    b. Från **Object Explorer** expanderar du databasen och noden tabell för att se **dbo. hvactable** som skapats.

    ![Ansluta till SQL Database med SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Ansluta till SQL Database med SSMS2")

1. Kör en fråga i SSMS för att se kolumnerna i tabellen.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Strömma data till Azure SQL Database

I det här avsnittet strömmar vi data till den `hvactable` som du skapade i föregående avsnitt.

1. Det första steget är att se till att det inte finns några poster i `hvactable` . Kör följande fråga i tabellen med hjälp av SSMS.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Skapa en ny Jupyter Notebook på HDInsight Spark-klustret. Klistra in följande kodfragment i en Code-cell och tryck sedan på **SKIFT + RETUR**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Vi strömmar data från **HVAC.csv** till `hvactable` . HVAC.csv-filen är tillgänglig i klustret på `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . I följande kodfragment hämtar vi först schemat för de data som ska strömmas. Sedan skapar vi en strömmande dataframe med det schemat. Klistra in kodfragmentet i en kod cell och tryck på **SKIFT + RETUR** för att köra.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. Utdata visar schemat för **HVAC.csv**. `hvactable`Har samma schema också. Utdata visar kolumnerna i tabellen.

    ![HDInsight Apache Spark schema tabell](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema för tabell")

1. Använd slutligen följande kodfragment för att läsa data från HVAC.csv och strömma dem till `hvactable` i databasen. Klistra in kodfragmentet i en kod cell, Ersätt plats hållarnas värden med värdena för din databas och tryck sedan på **SKIFT + RETUR** för att köra.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Kontrol lera att data strömmas till `hvactable` genom att köra följande fråga i SQL Server Management Studio (SSMS). Varje gång du kör frågan visas antalet rader i tabellen som ökar.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Nästa steg

* [Använd HDInsight Spark-kluster för att analysera data i Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Läs in data och kör frågor på ett Apache Spark kluster i Azure HDInsight](apache-spark-load-data-run-query.md)
* [Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
