---
title: Analysera program insikts loggar med Spark – Azure HDInsight
description: Lär dig hur du exporterar program insikts loggar till Blob Storage och sedan analysera loggarna med Spark på HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 9ae06565039e53c0096ff0541f7b28a5b041d9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864558"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analysera Application Insights telemetri loggar med Apache Spark på HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) på HDInsight för att analysera telemetri för insikter.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) är en analys tjänst som övervakar dina webb program. Telemetri-data som genereras av Application Insights kan exporteras till Azure Storage. När data är i Azure Storage kan HDInsight användas för att analysera den.

## <a name="prerequisites"></a>Förutsättningar

* Ett program som är konfigurerat för att använda Application Insights.

* Bekant med att skapa ett Linux-baserat HDInsight-kluster. Mer information finns i [skapa Apache Spark i HDInsight](apache-spark-jupyter-spark-sql.md).

* En webbläsare.

Följande resurser användes för att utveckla och testa det här dokumentet:

* Application Insights telemetridata genererades med hjälp av en [Node.js webbapp som kon figurer ATS för att använda Application Insights](../../azure-monitor/app/nodejs.md).

* En Linux-baserad Spark på HDInsight-kluster version 3,5 användes för att analysera data.

## <a name="architecture-and-planning"></a>Arkitektur och planering

Följande diagram illustrerar tjänst arkitekturen i det här exemplet:

:::image type="content" source="./media/apache-spark-analyze-application-insight-logs/application-insights.png" alt-text="Data flödar från Application Insights till Blob Storage, sedan Spark" border="false":::

### <a name="azure-storage"></a>Azure Storage

Application Insights kan konfigureras för att kontinuerligt exportera telemetri information till blobbar. HDInsight kan sedan läsa data som lagras i Blobbarna. Det finns dock vissa krav som du måste följa:

* **Plats**: om lagrings kontot och HDInsight finns på olika platser kan det öka svars tiden. Det ökar också kostnaden, eftersom utgående kostnader tillämpas på data som flyttas mellan regioner.

    > [!WARNING]  
    > Det finns inte stöd för att använda ett lagrings konto på en annan plats än HDInsight.

* **Blob-typ**: HDInsight stöder bara block-blobbar. Application Insights använder block-blobbar som standard, så bör fungera som standard med HDInsight.

Information om hur du lägger till lagrings utrymme i ett befintligt kluster finns i dokumentet [Lägg till ytterligare lagrings konton](../hdinsight-hadoop-add-storage.md) .

### <a name="data-schema"></a>Data schema

Application Insights tillhandahåller information om [export data modell](../../azure-monitor/app/export-data-model.md) för telemetri-dataformatet som exporteras till blobbar. Stegen i det här dokumentet använder Spark SQL för att arbeta med data. Spark SQL kan automatiskt generera ett schema för den JSON-datastruktur som loggats av Application Insights.

## <a name="export-telemetry-data"></a>Exportera telemetridata

Följ stegen i [Konfigurera kontinuerlig export](../../azure-monitor/app/export-telemetry.md) för att konfigurera Application Insights att exportera telemetri-information till en Azure Storage-blob.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurera HDInsight för att komma åt data

Om du skapar ett HDInsight-kluster lägger du till lagrings kontot när klustret skapas.

Om du vill lägga till Azure Storage kontot i ett befintligt kluster använder du informationen i dokumentet [Lägg till ytterligare lagrings konton](../hdinsight-hadoop-add-storage.md) .

## <a name="analyze-the-data-pyspark"></a>Analysera data: PySpark

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` där kluster namn är namnet på klustret.

2. I det övre högra hörnet på sidan Jupyter väljer du **nytt** och sedan **PySpark**. En ny flik i webbläsaren som innehåller en python-baserad Jupyter Notebook öppnas.

3. I det första fältet (kallas en **cell**) på sidan anger du följande text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Den här koden konfigurerar Spark för att rekursivt komma åt katalog strukturen för indata. Application Insights telemetri loggas till en katalog struktur som liknar `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Använd **Shift + Retur** för att köra koden. Till vänster i cellen visas ett ' \* ' mellan hakparenteserna för att indikera att koden i den här cellen körs. När den är klar \* visas ändringarna i ett tal, och utdata som liknar följande text visas under cellen:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` med det Azure Storage konto namnet och namnet på den BLOB-behållare som innehåller Application Insights data.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **Shift + Retur** för att köra den här cellen. Du ser ett resultat som liknar följande text:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Wasbs-sökvägen som returneras är platsen för Application Insights telemetridata. Ändra `hdfs dfs -ls` linjen i cellen så att den använder wasbs-sökvägen som returnerades och Använd sedan **Shift + Retur** för att köra cellen igen. Den här gången ska resultaten Visa de kataloger som innehåller telemetridata.

   > [!NOTE]  
   > I resten av stegen i det här avsnittet `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` användes katalogen. Din katalog struktur kan vara annorlunda.

6. I nästa cell anger du följande kod: Ersätt `WASB_PATH` med sökvägen från föregående steg.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataframe från JSON-filerna som exporteras av den löpande export processen. Använd **Shift + Retur** för att köra den här cellen.
7. I nästa cell anger och kör du följande för att visa schemat som Spark skapade för JSON-filerna:

   ```python
   jsonData.printSchema()
   ```

    Schemat för varje typ av telemetri är annorlunda. Följande exempel är det schema som genereras för webbegäranden (data som lagras i under `Requests` katalogen):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Använd följande för att registrera dataframe som en temporär tabell och köra en fråga mot data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Den här frågan returnerar Orts informationen för de 20 främsta posterna där context. location. City inte är null.

   > [!NOTE]  
   > Kontext strukturen finns i all telemetri som loggats av Application Insights. Orts elementet får inte vara ifyllt i loggarna. Använd schemat för att identifiera andra element som du kan fråga efter som kan innehålla data för dina loggar.

    Den här frågan returnerar information som liknar följande text:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Analysera data: Scala

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` där kluster namn är namnet på klustret.

2. I det övre högra hörnet på sidan Jupyter väljer du **nytt** och sedan **Scala**. En ny flik i webbläsaren som innehåller en Scala-baserad Jupyter Notebook visas.

3. I det första fältet (kallas en **cell**) på sidan anger du följande text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Den här koden konfigurerar Spark för att rekursivt komma åt katalog strukturen för indata. Application Insights telemetri loggas till en katalog struktur som liknar `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Använd **Shift + Retur** för att köra koden. Till vänster i cellen visas ett ' \* ' mellan hakparenteserna för att indikera att koden i den här cellen körs. När den är klar \* visas ändringarna i ett tal, och utdata som liknar följande text visas under cellen:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` med namnet på Azure Storage kontot och namnet på den BLOB-behållare som innehåller Application Insights loggar.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **Shift + Retur** för att köra den här cellen. Du ser ett resultat som liknar följande text:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Wasbs-sökvägen som returneras är platsen för Application Insights telemetridata. Ändra `hdfs dfs -ls` linjen i cellen så att den använder wasbs-sökvägen som returnerades och Använd sedan **Shift + Retur** för att köra cellen igen. Den här gången ska resultaten Visa de kataloger som innehåller telemetridata.

   > [!NOTE]  
   > I resten av stegen i det här avsnittet `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` användes katalogen. Den här katalogen kanske inte finns om inte dina telemetridata är för en webbapp.

6. I nästa cell anger du följande kod: Ersätt `WASB\_PATH` med sökvägen från föregående steg.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataframe från JSON-filerna som exporteras av den löpande export processen. Använd **Shift + Retur** för att köra den här cellen.

7. I nästa cell anger och kör du följande för att visa schemat som Spark skapade för JSON-filerna:

   ```scala
   jsonData.printSchema
   ```

    Schemat för varje typ av telemetri är annorlunda. Följande exempel är det schema som genereras för webbegäranden (data som lagras i under `Requests` katalogen):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Använd följande för att registrera dataframe som en temporär tabell och köra en fråga mot data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Den här frågan returnerar Orts informationen för de 20 främsta posterna där context. location. City inte är null.

   > [!NOTE]  
   > Kontext strukturen finns i all telemetri som loggats av Application Insights. Orts elementet får inte vara ifyllt i loggarna. Använd schemat för att identifiera andra element som du kan fråga efter som kan innehålla data för dina loggar.

    Den här frågan returnerar information som liknar följande text:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Nästa steg

Fler exempel på hur du använder Apache Spark för att arbeta med data och tjänster i Azure finns i följande dokument:

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

Information om hur du skapar och kör Spark-program finns i följande dokument:

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärr anslutning på ett Apache Spark kluster med livy](apache-spark-livy-rest-interface.md)
