---
title: Migrera data från Apache Cassandra till Azure Cosmos DB API för Cassandra med Databricks (Spark)
description: Lär dig hur du migrerar data från Apache Cassandra Database till Azure Cosmos DB API för Cassandra med Azure Databricks och Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 74088d749279ab72851e714a50b558dc2adbc0d7
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516551"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrera data från Cassandra till Azure Cosmos DB API för Cassandra konto med Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB har blivit ett bra val för företags arbets belastningar som körs på Apache Cassandra av en rad olika orsaker, till exempel: 

* **Ingen omkostnader för hantering och övervakning:** Det eliminerar behovet av att hantera och övervaka en myriaden av inställningar i OS-, JVM-och yaml-filer och deras interaktioner.

* **Betydande kostnads besparingar:** Du kan spara kostnader med Azure Cosmos DB, vilket inkluderar kostnaden för VM, bandbredd och tillämpliga licenser. Dessutom behöver du inte hantera data Center, servrar, SSD-lagring, nätverk och el kostnader. 

* **Möjlighet att använda befintlig kod och befintliga verktyg:** Azure Cosmos DB tillhandahåller kompatibilitet med protokoll nivå för Cassandra med befintliga SDK: er och verktyg för. Den här kompatibiliteten garanterar att du kan använda din befintliga kodbas med Cassandra-API:et för Azure Cosmos DB med minimala ändringar.

Det finns olika sätt att migrera databas arbets belastningar från en plattform till en annan. [Azure Databricks](https://azure.microsoft.com/services/databricks/) är en plattform som ett tjänst erbjudande för [Apache Spark](https://spark.apache.org/) som ger ett sätt att utföra offline-migreringar i stor skala. I den här artikeln beskrivs de steg som krävs för att migrera data från interna Apache Cassandra-tecken/tabeller till Azure Cosmos DB API för Cassandra med Azure Databricks.

## <a name="prerequisites"></a>Förutsättningar

* [Etablera ett Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account)

* [Granska grunderna för att ansluta till Azure Cosmos DB API för Cassandra](cassandra-spark-generic.md)

* Granska de [funktioner som stöds i Azure Cosmos DB API för Cassandra](cassandra-support.md) för att säkerställa kompatibiliteten.

* Se till att du redan har skapat tomma och tabeller i mål Azure Cosmos DB API för Cassandra konto

* [Använd cqlsh eller värdbaserad Shell för verifiering](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Etablera ett Azure Databricks kluster

Du kan följa anvisningarna för att [etablera ett Azure Databricks kluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Observera dock att Apache Spark 3. x för närvarande inte stöds för Apache Cassandra-anslutningen. Du måste tillhandahålla en Databricks-körning med en v2. x-version som stöds av Apache Spark. Vi rekommenderar att du väljer en version av Databricks runtime som stöder den senaste versionen av Spark 2. x, med en senare version än Scala version 2,11:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks-körning":::


## <a name="add-dependencies"></a>Lägg till beroenden

Du måste lägga till Apache Spark Cassandra Connector-biblioteket till klustret för att kunna ansluta till både interna och Cosmos DB Cassandra-slutpunkter. I klustret väljer du bibliotek – > installerar New-> maven-> Sök paket:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks Sök paket":::

Skriv `Cassandra` i sökrutan och välj den senaste `spark-cassandra-connector` maven-databasen som är tillgänglig och välj sedan installera:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks Välj paket":::

> [!NOTE]
> Se till att du startar om Databricks-klustret när Cassandra Connector-biblioteket har installerats.

## <a name="create-scala-notebook-for-migration"></a>Skapa Scala Notebook för migrering

Skapa en Scala-anteckningsbok i Databricks med följande kod. Ersätt dina käll-och mål Cassandra-konfigurationer med motsvarande autentiseringsuppgifter och käll-/mål-och käll-/mål-och tabell och kör sedan:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes` `connections_per_executor_max` Konfigurationerna och är viktiga för att undvika [hastighets begränsning](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), vilket inträffar när begär anden till Cosmos DB överskrider ett allokerat data flöde ([enheter för programbegäran](./request-units.md)). Du kan behöva justera de här inställningarna beroende på antalet körningar i Spark-klustret och eventuellt storleken (och därför RU-kostnaden) för varje post som skrivs till mål tabellerna.

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna ru/s med Azure Cosmos DB kapacitets planerings](estimate-ru-with-capacity-planner.md) artiklar
* [Elastisk skala i Azure Cosmos DB API för Cassandra](manage-scale-cassandra.md)