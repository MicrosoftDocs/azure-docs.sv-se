---
title: Läs API för Cassandra tabell data med Spark
titleSufix: Azure Cosmos DB
description: I den här artikeln beskrivs hur du läser data från API för Cassandra tabeller i Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073504"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Läsa data från Azure Cosmos DB API för Cassandra tabeller med Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Den här artikeln beskriver hur du läser data som lagras i Azure Cosmos DB API för Cassandra från Spark.

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
## <a name="dataframe-api"></a>Dataframe-API

### <a name="read-table-using-sessionreadformat-command"></a>Läs tabell med kommandot session. Read. format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Läs tabell med Spark. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Läs vissa kolumner i tabellen

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Använda filter

Du kan push-överföra predikat till databasen för att möjliggöra bättre optimerade Spark-frågor. Ett predikat är ett villkor för en fråga som returnerar true eller false, vanligt vis finns i WHERE-satsen. Ett predikat push-filter filtrerar data i databas frågan, vilket minskar antalet poster som hämtas från databasen och förbättrar frågans prestanda. Som standard tar Spark-datauppsättnings-API: et automatiskt över giltiga WHERE-satser till-databasen. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

Avsnittet PushedFilters i den fysiska planen innehåller GreaterThan push-filter. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="partitioner":::

## <a name="rdd-api"></a>RDD-API

### <a name="read-table"></a>Läs tabell
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Läs vissa kolumner i tabellen

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL-vyer 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Skapa en tillfällig vy från en dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Köra frågor mot vyn

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Nästa steg

Följande är ytterligare artiklar om hur du arbetar med Azure Cosmos DB API för Cassandra från Spark:
 
 * [Upsert-åtgärder](cassandra-spark-upsert-ops.md)
 * [Borttagningsåtgärder](cassandra-spark-delete-ops.md)
 * [Sammansättningsåtgärder](cassandra-spark-aggregation-ops.md)
 * [Åtgärder för tabell kopiering](cassandra-spark-table-copy-ops.md)

