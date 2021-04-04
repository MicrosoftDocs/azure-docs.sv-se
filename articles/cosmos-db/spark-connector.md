---
title: Anslut Apache Spark till Azure Cosmos DB
description: Lär dig mer om Azure Cosmos DB Spark-anslutningsprogrammet som gör att du kan ansluta Apache Spark till Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 06498a27b95a72148497efd2d1e600d802414359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359565"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Påskynda stor data analys genom att använda Apache Spark för att Azure Cosmos DB koppling
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Du kan köra [Spark](https://spark.apache.org/) -jobb med data som lagras i Azure Cosmos dB med hjälp av Cosmos DB Spark-anslutaren. Cosmos kan användas för bearbetning av batch-och data strömmar och som betjänar lager för åtkomst med låg latens.

Du kan använda anslutningen med [Azure Databricks](https://azure.microsoft.com/services/databricks) eller [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), som tillhandahåller hanterade Spark-kluster i Azure. I följande tabell visas de Spark-versioner som stöds.

| Komponent | Version |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2,2. x och 2.1. x |
| Scala | 2,11 |
| Azure Databricks körnings version | > 3,4 |

> [!WARNING]
> Den här anslutningen stöder kärnan (SQL) API för Azure Cosmos DB.
> För Cosmos DB för MongoDB-API använder du [MongoDB Spark-anslutningsprogrammet](https://docs.mongodb.com/spark-connector/master/).
> För Cosmos DB API för Cassandra använder du [Cassandra Spark-anslutaren](https://github.com/datastax/spark-cassandra-connector).

> [!IMPORTANT]
> Azure Cosmos DB Spark Connector stöds för närvarande inte på [Server](serverless.md) lös konton. Detta kommer att åtgärdas eftersom det Server lösa erbjudandet blir allmänt tillgängligt.

## <a name="quickstart"></a>Snabbstart

* Följ stegen i [Kom igång med Java SDK](./create-sql-api-java.md) för att skapa ett Cosmos DB-konto och fyll i några data.
* Följ stegen i [Azure Databricks komma igång](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) för att skapa en Azure Databricks arbets yta och ett kluster.
* Nu kan du skapa nya antecknings böcker och importera Cosmos DB anslutnings biblioteket. Gå till [arbeta med Cosmos DB-anslutaren](#bk_working_with_connector) för mer information om hur du konfigurerar din arbets yta.
* I följande avsnitt finns kod avsnitt om hur du läser och skriver med hjälp av-anslutningen.

### <a name="batch-reads-from-cosmos-db"></a>Batch läser från Cosmos DB

Följande fragment visar hur du skapar en spark-DataFrame för att läsa från Cosmos DB i PySpark.

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

Följande kodfragment visar hur du skriver en data ram som Cosmos DB i PySpark.

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

### <a name="streaming-reads-from-cosmos-db"></a>Strömmande läsningar från Cosmos DB

Följande kodfragment visar hur du ansluter till och läser från Azure Cosmos DB ändra feed.

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

Följande kodfragment visar hur du skriver en data ram som Cosmos DB i PySpark.

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
Fler kodfragment och slut punkt till slut punkts exempel finns i [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Arbeta med anslutningen

Du kan bygga anslutningen från källan i GitHub eller ladda ned Uber-jar v7 från maven i länkarna nedan.

| Spark | Scala | Senaste version |
|---|---|---|
| 2.4.0 | 2,11 | [Azure-cosmosdb – spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Använda Databricks-anteckningsböcker

Skapa ett bibliotek med hjälp av din Databricks-arbetsyta genom att följa anvisningarna i Azure Databrickss guide > [använda Azure Cosmos DB Spark-anslutaren](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Sidan **använd Azure Cosmos DB Spark Connector** är för närvarande inte uppdaterad. I stället för att hämta sex separata jar v7 i sex olika bibliotek kan du Hämta Uber-burken från maven på [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) och installera den här burken/biblioteket.
> 

### <a name="using-spark-cli"></a>Använda Spark-cli

Om du vill arbeta med anslutningen med hjälp av Spark-CLI (det `spark-shell` vill säga, `pyspark` , `spark-submit` ) kan du använda `--packages` parametern med kopplingens [maven-koordinater](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Använda Jupyter-anteckningsböcker

Om du använder Jupyter Notebook i HDInsight kan du använda Spark-Magic- `%%configure` cellen för att ange kopplingens maven-koordinater.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Observera att inkluderingen av `spark.jars.excludes` är unik för att ta bort potentiella konflikter mellan anslutningen, Apache Spark och livy.

### <a name="build-the-connector"></a>Bygg anslutningen

För närvarande använder det här kopplings projektet för att `maven` skapa utan beroenden, du kan köra:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Arbeta med våra exempel

[Cosmos DB Spark GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmosdb-spark) har följande exempel på bärbara datorer och skript som du kan prova.

* **Preflight-prestanda i real tid med Spark och Cosmos dB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connect Spark för att Cosmos dB med hjälp av HDInsight Jupyter Notebook-tjänsten för att presentera Spark SQL, GraphFrames och förutsäga flyg fördröjningar med ml pipelines.
* **Twitter-källa med Apache Spark och Azure Cosmos DB ändra feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Använda Apache Spark för att fråga Cosmos DB grafer**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Ansluta Azure Databricks till Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** med hjälp av `azure-cosmosdb-spark` .  Länkade här är också en Azure Databricks version av den [bärbara datorn för flyg prestanda i real tid](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Lambda-arkitektur med Azure Cosmos DB och HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: du kan minska drifts kostnaderna för att underhålla Big datapipelines med Cosmos DB och Spark.

## <a name="more-information"></a>Mer information

Vi har mer information i `azure-cosmosdb-spark` [wikin](https://github.com/Azure/azure-cosmosdb-spark/wiki) , inklusive:

* [Användar handbok för Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exempel på agg regeringar](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguration och installation

* [Konfiguration av Spark-anslutning](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark to Cosmos DB Connector-installation](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (pågår)
* [Konfigurera Power BI Direct-fråga till Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Felsökning

* [Använda Cosmos DB agg regeringar](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Kända problem](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestanda

* [Prestanda tips](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Fråga test körningar](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Skriver test körningar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Ändringsflöde

* [Bearbetning av ström bearbetnings ändringar med Azure Cosmos DB ändra feed och Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Ändra feed-demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Sammanstrukturerade Stream-demonstrationer](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Övervakning

* [Övervaka Spark-jobb med Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du hämta Spark till Azure Cosmos DB Connector från [Azure-cosmosdb-Spark GitHub-](https://github.com/Azure/azure-cosmosdb-spark) lagringsplatsen. Utforska följande ytterligare resurser i lagrings platsen:

* [Exempel på agg regeringar](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exempel på skript och antecknings böcker](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Du kanske också vill läsa [guiden Apache Spark SQL, DataFrames och data uppsättningar](https://spark.apache.org/docs/latest/sql-programming-guide.html)och [Apache Spark på Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) -artikeln.