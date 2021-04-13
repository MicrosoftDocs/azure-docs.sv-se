---
title: Anslut Apache Spark 2 till Azure Cosmos DB
description: Läs mer om Azure Cosmos DB Spark 2 OLTP-anslutning som gör att du kan ansluta Apache Spark till Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 9d4a1a7d359bac83159d0e5ca4bddfc5935394a5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364285"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-v2-to-azure-cosmos-db-oltp-connector"></a>Påskynda analys av stordata med hjälp av Apache Spark v2 för att Azure Cosmos DB OLTP-anslutning
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Du kan köra [Spark v2-jobb](https://spark.apache.org/) med data som lagras i Azure Cosmos DB med Cosmos DB Spark 2 OLTP-anslutning. Cosmos kan användas för batch- och dataströmbearbetning och som ett betjänande lager för åtkomst med kort svarstid.

Du kan använda anslutningsappen [med Azure Databricks](https://azure.microsoft.com/services/databricks) [eller Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster i Azure. Följande tabell visar Spark-versioner som stöds.

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x och 2.1.x |
| Scala | 2,11 |
| Azure Databricks-körningsversion | > 3.4 |

> [!WARNING]
> Den här anslutningsappen stöder kärn-API:et (SQL) för Azure Cosmos DB.
> För Cosmos DB mongoDB-API:et använder du [MongoDB Spark-anslutningsappen](https://docs.mongodb.com/spark-connector/master/).
> För Cosmos DB API för Cassandra du [Cassandra Spark-anslutningsappen](https://github.com/datastax/spark-cassandra-connector).

> [!IMPORTANT]
> Den Azure Cosmos DB Spark OLTP-anslutningsappen stöds för närvarande inte på [serverlösa](serverless.md) konton. Detta åtgärdas när det serverlösa erbjudandet blir allmänt tillgängligt.

## <a name="quickstart"></a>Snabbstart

* Följ stegen i [Kom igång med Java SDK för](./create-sql-api-java.md) att konfigurera ett Cosmos DB konto och fylla i vissa data.
* Följ stegen i Azure Databricks [komma igång för](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) att konfigurera Azure Databricks arbetsyta och kluster.
* Nu kan du skapa nya notebook-datorer och importera Cosmos DB connector-biblioteket. Gå vidare [till Arbeta med Cosmos DB-anslutningsappen](#bk_working_with_connector) för mer information om hur du ställer in din arbetsyta.
* Följande avsnitt innehåller kodfragment om hur du läser och skriver med anslutningsappen.

### <a name="batch-reads-from-cosmos-db"></a>Batch-läsningar från Cosmos DB

Följande kodfragment visar hur du skapar en Spark DataFrame för att läsa från Cosmos DB i PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Och samma kodfragment i Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch-skrivningar till Cosmos DB

Följande kodfragment visar hur du skriver en dataram för Cosmos DB i PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Och samma kodfragment i Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Strömningsläsningar från Cosmos DB

Följande kodfragment visar hur du ansluter till och läser från Azure Cosmos DB ändringsflöde.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
Och samma kodfragment i Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Strömmande skrivningar till Cosmos DB

Följande kodfragment visar hur du skriver en dataram för Cosmos DB i PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

Och samma kodfragment i Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Fler kodfragment och exempel från slutet till slut finns i [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Arbeta med anslutningsappen

Du kan skapa anslutningsappen från källan i GitHub eller ladda ned uber-jar-filer från Maven i länkarna nedan.

| Spark | Scala | Senaste version |
|---|---|---|
| 2.4.0 | 2,11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Använda Notebook-datorer i Databricks

Skapa ett bibliotek med databricks-arbetsytan genom att följa riktlinjerna i Azure Databricks Guide > Use the Azure Cosmos DB Spark connector (Använda [Spark-Azure Cosmos DB anslutningsappen)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Sidan **Använd Azure Cosmos DB Spark Connector** är för närvarande inte uppdaterad. I stället för att ladda ned de sex separata jar-filarna till sex olika bibliotek kan du ladda ned uber-jar-filen från maven [på azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) och installera det här jar-biblioteket.
> 

### <a name="using-spark-cli"></a>Använda spark-cli

Om du vill arbeta med anslutningsappen med hjälp av spark-cli (det vill säga , , ), kan du använda parametern med `spark-shell` `pyspark` `spark-submit` anslutningsappens `--packages` [maven-koordinater](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Använda Jupyter Notebooks

Om du använder en Jupyter Notebook HDInsight kan du använda spark-magic-cellen för att ange `%%configure` anslutningsappens maven-koordinater.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Observera att inkluderingen av är specifik för att ta bort potentiella konflikter `spark.jars.excludes` mellan anslutningsappen, Apache Spark och Livy.

### <a name="build-the-connector"></a>Skapa anslutningsappen

För närvarande använder det här anslutningsprojekt så att du kan skapa utan `maven` beroenden genom att köra:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Arbeta med våra exempel

[Lagringsplatsen Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) innehåller följande exempelanteckningsböcker och skript som du kan prova.

* Prestanda för flyg på tid med **Spark och Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)html: Anslut Spark till Cosmos DB med HDInsight Jupyter Notebook-tjänsten för att demonstrera Spark SQL, GraphFrames och förutsäga flygförseningar med hjälp av  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)ML-pipelines.
* **Twitter-källa med Apache Spark och Azure Cosmos DB ändringsflöde:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Använda Apache Spark för att Cosmos DB diagram:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ansluta Azure Databricks till Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** med `azure-cosmosdb-spark` .  Länkad här är också Azure Databricks version av [notebook-programmet On-Time Flight Performance.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** Du kan minska driftkostnaderna för att underhålla stordatapipelines med hjälp av Cosmos DB och Spark.

## <a name="more-information"></a>Mer information

Vi har mer information på `azure-cosmosdb-spark` [wikin,](https://github.com/Azure/azure-cosmosdb-spark/wiki) inklusive:

* [Azure Cosmos DB användarhandbok för Spark-anslutningsappen](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Sammansättningsexempel](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguration och installation

* [Konfiguration av Spark-anslutningsapp](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Installation av Spark Cosmos DB-anslutningsprogram](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (pågår)
* [Konfigurera Power BI Direct Query för att Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Felsökning

* [Använda Cosmos DB aggregat](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Kända problem](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestanda

* [Prestandatips](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Frågetestkörningar](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Skriva testkörningar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Ändringsflöde

* [Dataströmbearbetningsändringar med Azure Cosmos DB ändringsflöde och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demo om ändringsflöde](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Structured Stream-demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Övervakning

* [Övervaka Spark-jobb med Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det laddar du ned Spark till Azure Cosmos DB-anslutningsappen från [GitHub-lagringsplatsen azure-cosmosdb-spark.](https://github.com/Azure/azure-cosmosdb-spark) Utforska följande ytterligare resurser i lagringsplatsen:

* [Sammansättningsexempel](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempelskript och notebook-datorer](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kanske också vill läsa [Apache Spark SQL, DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html)och Datasets Guide och Apache Spark om [Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artikeln.