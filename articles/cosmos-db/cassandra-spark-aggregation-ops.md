---
title: Aggregate operations on Azure Cosmos DB Cassandra API tables from Spark (Mängdåtgärder i Azure Cosmos DB med API för Cassandra-tabeller från Spark)
description: Den här artikeln beskriver grundläggande agg regerings åtgärder mot Azure Cosmos DB API för Cassandra tabeller från Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087629"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Aggregate operations on Azure Cosmos DB Cassandra API tables from Spark (Mängdåtgärder i Azure Cosmos DB med API för Cassandra-tabeller från Spark) 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

I den här artikeln beskrivs grundläggande aggregeringsåtgärder mot Azure Cosmos DB med API för Cassandra-tabeller från Spark. 

> [!NOTE]
> Filtrering på Server sidan och insamling på Server sidan stöds för närvarande inte i Azure Cosmos DB API för Cassandra.

## <a name="cassandra-api-configuration"></a>API för Cassandra konfiguration

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="sample-data-generator"></a>Exempel på data Generator

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Count-åtgärd


### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Utdataparametrar**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Dataframe-API

Count to dataframes stöds inte för närvarande.  Exemplet nedan visar hur du kör ett dataframe-antal när du har sparat dataframe till minnet som en lösning.

Välj ett [lagrings alternativ]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) från följande tillgängliga alternativ för att undvika att köra "slut på minne"-problem:

* MEMORY_ONLY: det här är standard alternativet för lagring. Lagrar RDD som avserialiserade Java-objekt i JVM. Om RDD inte passar i minnet kommer vissa partitioner inte att cachelagras, och de omberäknas på samma sätt varje tid de behöver.

* MEMORY_AND_DISK: lagrar RDD som avserialiserade Java-objekt i JVM. Om RDD inte passar i minnet lagrar du partitionerna som inte ryms på disken, och när det behövs kan du läsa dem från den plats där de lagras.

* MEMORY_ONLY_SER (Java/Scala): lagrar RDD som serialiserade Java-objekt – en byte mat ris per partition. Det här alternativet är utrymmes effektivt jämfört med avserialiserade objekt, särskilt när du använder en snabb serialisering, men mer processor intensiv att läsa.

* MEMORY_AND_DISK_SER (Java/Scala): det här lagrings alternativet är som MEMORY_ONLY_SER, den enda skillnaden är att den inte tar bort partitioner som inte ryms i disk minnet i stället för att omdistribuera dem när de behövs.

* DISK_ONLY: lagrar endast RDD-partitionerna på disken.

* MEMORY_ONLY_2 MEMORY_AND_DISK_2...: samma som nivåerna ovan, men replikerar varje partition på två klusternoder.

* OFF_HEAP (experimentell): liknar MEMORY_ONLY_SER, men lagrar data i minnet på annan heap, och det krävs minne av heap för att aktive ras i förväg. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Genomsnittlig åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Utdataparametrar**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Dataframe-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Utdataparametrar**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Utdataparametrar**
```
16.016000175476073
```

## <a name="min-operation"></a>Min åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Utdataparametrar**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Dataframe-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Utdataparametrar**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Utdataparametrar**
```
11.33
```

## <a name="max-operation"></a>Maximal åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Dataframe-API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Utdataparametrar**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Utdataparametrar**
```
22.45
```

## <a name="sum-operation"></a>Sum-åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Utdataparametrar**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Dataframe-API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Utdataparametrar**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Utdataparametrar**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Topp eller jämförbar åtgärd

### <a name="rdd-api"></a>RDD-API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Utdataparametrar**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Dataframe-API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Utdataparametrar**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Nästa steg

Information om hur du utför tabell kopierings åtgärder finns i:

* [Åtgärder för tabell kopiering](cassandra-spark-table-copy-ops.md)
