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
ms.openlocfilehash: 3cbcb7eb3695e6f57daef741d4cd4b15577d8f58
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493285"
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
> `spark.cassandra.output.batch.size.rows`- `spark.cassandra.output.concurrent.writes` Och- `connections_per_executor_max` konfigurationerna är viktiga för att undvika [hastighets begränsning](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), vilket inträffar när begär anden till Azure Cosmos DB överskrider etablerade data flöde/([enheter för programbegäran](./request-units.md)). Du kan behöva justera de här inställningarna beroende på antalet körningar i Spark-klustret och eventuellt storleken (och därför RU-kostnaden) för varje post som skrivs till mål tabellerna.

## <a name="troubleshooting"></a>Felsökning

### <a name="rate-limiting-429-error"></a>Hastighets begränsning (429-fel)
Du kan få en felkod på 429 eller `request rate is large` fel text, trots att de båda inställningarna sänks till sina lägsta värden. Här följer några sådana scenarier:

- **Data flödet som tilldelas tabellen är mindre än 6000 [enheter för programbegäran](./request-units.md)**. Med lägsta möjliga inställningar kommer Spark att kunna köra skrivningar till en hastighet av cirka 6000 enheter för programbegäran eller mer. Om du har etablerad en tabell i ett disk utrymme med delat data flöde, är det möjligt att den här tabellen har mindre än 6000 ru: er tillgänglig vid körning. Se till att tabellen som du migrerar till har minst 6000 ru: er tillgänglig för den när du kör migreringen, och om det behövs allokera enheter för dedikerade enheter till tabellen. 
- **Onödig data lutning med stor data volym**. Om du har en stor mängd data (d.v.s. tabell rader) som ska migreras till en specifik tabell men har en betydande skev i data (dvs. ett stort antal poster som skrivs för samma partitionsnyckel), kan du fortfarande uppleva hastighets begränsning även om du har en stor mängd [enheter för programbegäran](./request-units.md) som tillhandahålls i tabellen. Detta beror på att enheter för programbegäran delas lika mellan fysiska partitioner och att tung data skevning kan resultera i en Flask hals för begär anden till en enda partition, vilket orsakar en hastighets begränsning. I det här scenariot rekommenderar vi att du minskar till minimala data flödes inställningar i Spark för att undvika hastighets begränsning och tvingar migreringen att köras långsamt. Det här scenariot kan vara vanligare när du migrerar referens-eller kontroll tabeller, där åtkomsten är mindre frekvent men skevningen kan vara hög. Men om en betydande skevning finns i någon annan typ av tabell, kan det också vara tillrådligt att granska data modellen för att undvika problem med hot mot partitioner för arbets belastningen under stabilitets åtgärder. 
- **Det går inte att hämta Count i stor tabell**. Körning `select count(*) from table` stöds för närvarande inte för stora tabeller. Du kan hämta Count från mått i Azure Portal (se vår [fel söknings artikel](cassandra-troubleshoot.md)), men om du behöver fastställa antalet i en stor tabell inom ramen för ett Spark-jobb kan du kopiera data till en tillfällig tabell och sedan använda Spark SQL för att hämta Count, t. ex. nedan (Ersätt `<primary key>` med ett fält från den resulterande tillfälliga tabellen).

  ```scala
  val ReadFromCosmosCassandra = sqlContext
    .read
    .format("org.apache.spark.sql.cassandra")
    .options(cosmosCassandra)
    .load

  ReadFromCosmosCassandra.createOrReplaceTempView("CosmosCassandraResult")
  %sql
  select count(<primary key>) from CosmosCassandraResult
  ```

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna ru/s med Azure Cosmos DB kapacitets planerings](estimate-ru-with-capacity-planner.md) artiklar
* [Elastisk skala i Azure Cosmos DB API för Cassandra](manage-scale-cassandra.md)
