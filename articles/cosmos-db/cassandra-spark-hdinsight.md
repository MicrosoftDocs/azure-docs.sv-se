---
title: Åtkomst Azure Cosmos DB API för Cassandra från Spark on garn med HDInsight
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB API för Cassandra från Spark on garn med HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359605"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Åtkomst Azure Cosmos DB API för Cassandra från Spark on garn med HDInsight
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Den här artikeln beskriver hur du kommer åt Azure Cosmos DB API för Cassandra från Spark on garn med HDInsight-Spark från Spark-Shell. HDInsight är Microsofts Hortonworks Hadoop-PaaS på Azure som utnyttjar objekt lagring för HDFS och ingår i flera varianter, inklusive [Spark](../hdinsight/spark/apache-spark-overview.md).  Innehållet i det här dokumentet refererar till HDInsight-Spark, det gäller för alla Hadoop-distributioner.  

## <a name="prerequisites"></a>Förutsättningar

* [Etablera Azure Cosmos DB API för Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Granska grunderna för att ansluta till Azure Cosmos DB API för Cassandra](cassandra-spark-generic.md)

* [Etablera ett HDInsight-Spark kluster](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Granska kod exemplen för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för verifiering om du föredrar det](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **API för Cassandra konfiguration i Spark2** – Spark-anslutningen för Cassandra kräver att Cassandra anslutnings information initieras som en del av Spark-kontexten. När du startar en Jupyter-anteckningsbok är Spark-sessionen och-kontexten redan initierad och det är inte lämpligt att stoppa och initiera Spark-kontexten om den inte är slutförd med varje konfigurations uppsättning som en del av HDInsight standard Jupyter Notebook start. En lösning är att lägga till information om Cassandra-instanserna i Ambari, Spark2 service Configuration direkt. Detta är en engångs aktivitet per kluster som kräver att Spark2-tjänsten startas om.
 
  1. Gå till Ambari, Spark2 service och välj configs

  2. Gå sedan till anpassade spark2-standardvärden och Lägg till en ny egenskap med följande och starta om Spark2-tjänsten:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Åtkomst Azure Cosmos DB API för Cassandra från Spark-gränssnittet

Spark-gränssnittet används i testnings-och utforsknings syfte.

* Starta Spark-Shell med nödvändiga maven-beroenden som är kompatibla med klustrets Spark-version.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Köra vissa DDL-och DML-åtgärder

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Kör CRUD-åtgärder

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Åtkomst Azure Cosmos DB API för Cassandra från Jupyter-anteckningsböcker

HDInsight-Spark levereras med Zeppelin-och Jupyter Notebook-tjänster. De är både webbaserade Notebook-miljöer som stöder Scala och python. Bärbara datorer är fantastiska för interaktiv analys och samarbete, men är inte avsedda för drift/produktions processer.

Följande Jupyter-anteckningsböcker kan laddas upp till ditt HDInsight Spark-kluster och tillhandahålla färdiga exempel för att arbeta med Azure Cosmos DB API för Cassandra. Se till att gå igenom den första antecknings boken för `1.0-ReadMe.ipynb` att granska Spark service-konfigurationen för att ansluta till Azure Cosmos DB API för Cassandra.

Ladda ned de här antecknings böckerna under [Azure-Cosmos-DB-Cassandra-API-Spark-Notebook-Jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) till din dator.
  
### <a name="how-to-upload"></a>Ladda upp:
När du startar Jupyter navigerar du till Scala. Skapa först en katalog och ladda sedan upp antecknings böckerna till katalogen. Knappen upload (överför) är högst upp på höger sida.  

### <a name="how-to-run"></a>Så här kör du:
Kör genom antecknings böckerna och varje Notebook-cell sekventiellt.  Klicka på knappen Kör överst i varje antecknings bok för att köra alla celler eller SKIFT + RETUR för varje cell.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Åtkomst med Azure Cosmos DB API för Cassandra från Spark Scala-programmet

För automatiserade processer i produktion skickas Spark-program till klustret via [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Nästa steg

* [Så här skapar du ett Spark Scala-program i en IDE och skickar det till HDInsight Spark-klustret via livy för körning](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Så här ansluter du till Azure Cosmos DB API för Cassandra från ett Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Fullständig lista över kod exempel för att arbeta med API för Cassandra](cassandra-spark-generic.md)
