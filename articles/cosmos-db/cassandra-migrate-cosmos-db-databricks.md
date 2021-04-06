---
title: Migrera data från Apache Cassandra till Azure Cosmos DB API för Cassandra med Databricks (Spark)
description: Lär dig hur du migrerar data från en Apache Cassandra-databas till Azure Cosmos DB API för Cassandra med Azure Databricks och Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801017"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrera data från Cassandra till ett Azure Cosmos DB API för Cassandra konto genom att använda Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API för Cassandra i Azure Cosmos DB har blivit ett bra val för företags arbets belastningar som körs på Apache Cassandra av flera orsaker:

* **Ingen omkostnader för hantering och övervakning:** Den eliminerar behovet av att hantera och övervaka inställningar över operativ system, JVM och YAML-filer och deras interaktioner.

* **Betydande kostnads besparingar:** Du kan spara kostnader med Azure Cosmos DB, vilket innefattar kostnaden för virtuella datorer, bandbredd och tillämpliga licenser. Du behöver inte hantera data Center, servrar, SSD-lagring, nätverks-och el kostnader.

* **Möjlighet att använda befintlig kod och befintliga verktyg:** Azure Cosmos DB ger till gång till en kompatibilitet med befintliga Cassandra SDK: er och verktyg. Den här kompatibiliteten garanterar att du kan använda din befintliga kodbas med Azure Cosmos DB API för Cassandra med enkla ändringar.

Det finns många sätt att migrera databas arbets belastningar från en plattform till en annan. [Azure Databricks](https://azure.microsoft.com/services/databricks/) är ett PaaS-erbjudande (Platform as a Service) för [Apache Spark](https://spark.apache.org/) som ger ett sätt att utföra offline-migreringar i stor skala. I den här artikeln beskrivs de steg som krävs för att migrera data från interna Apache Cassandra-standardvärden och tabeller till Azure Cosmos DB API för Cassandra med Azure Databricks.

## <a name="prerequisites"></a>Förutsättningar

* [Etablera ett Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account).

* [Granska grunderna för att ansluta till en Azure Cosmos DB API för Cassandra](cassandra-spark-generic.md).

* Granska de [funktioner som stöds i Azure Cosmos DB API för Cassandra](cassandra-support.md) för att säkerställa kompatibiliteten.

* Se till att du redan har skapat tomma blank steg och tabeller i mål Azure Cosmos DB API för Cassandra konto.

* [Använd cqlsh eller värdbaserad Shell för verifiering](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Etablera ett Azure Databricks kluster

Du kan följa anvisningarna för att [etablera ett Azure Databricks kluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Vi rekommenderar att du väljer Databricks runtime version 7,5, som stöder Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Skärm bild som visar hur du hittar Databricks runtime-versionen.":::

## <a name="add-dependencies"></a>Lägg till beroenden

Du måste lägga till Apache Spark Cassandra Connector-biblioteket till klustret för att kunna ansluta till både interna och Azure Cosmos DB Cassandra-slutpunkter. I klustret väljer du **bibliotek**  >  **installera ny**  >  **maven** och lägger sedan till `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` i maven-koordinater.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Skärm bild som visar sökning efter maven-paket i Databricks.":::

Välj **Installera** och starta sedan om klustret när installationen är klar.

> [!NOTE]
> Kontrol lera att du startar om Databricks-klustret när Cassandra Connector-biblioteket har installerats.

## <a name="create-scala-notebook-for-migration"></a>Skapa Scala Notebook för migrering

Skapa en Scala-anteckningsbok i Databricks. Ersätt dina käll-och mål Cassandra-konfigurationer med motsvarande autentiseringsuppgifter, och käll-och mål tecken och tabeller. Kör sedan följande kod:

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
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows`Värdena och `spark.cassandra.output.concurrent.writes` antalet arbetare i ditt Spark-kluster är viktiga konfigurationer för att justera för att undvika [hastighets begränsning](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). Hastighets begränsningen inträffar när begär anden till Azure Cosmos DB överskrider det etablerade data flödet eller [begär ande enheter](./request-units.md) (ru: er). Du kan behöva justera inställningarna, beroende på antalet körningar i Spark-klustret och eventuellt storleken (och därför RU-kostnaden) för varje post som skrivs till mål tabellerna.

## <a name="troubleshoot"></a>Felsöka

### <a name="rate-limiting-429-error"></a>Hastighets begränsning (429-fel)

Du kan se att en 429-felkod eller "begär ande frekvensen är stor" är en fel text även om du har reducerat inställningarna till deras lägsta värden. Följande scenarier kan orsaka hastighets begränsning:

* **Data flödet som tilldelas tabellen är mindre än 6 000 [enheter för programbegäran](./request-units.md)**. Spark kan till och med lägsta inställningar skriva till en hastighet av cirka 6 000 enheter för programbegäran eller mer. Om du har allokerat en tabell i ett tecken utrymme med delat data flöde, är det möjligt att den här tabellen har färre än 6 000 ru: er tillgängliga vid körning.

    Se till att den tabell som du migrerar till har minst 6 000 ru: er tillgänglig när du kör migreringen. Om det behövs allokerar du dedikerade enheter för programbegäran till tabellen.

* **Onödig data lutning med stor data volym**. Om du har en stor mängd data som ska migreras till en specifik tabell men har en betydande skev i data (det vill säga ett stort antal poster som skrivs för samma partitionsnyckel) kan du ändå uppleva begränsningen även om du har flera [enheter för programbegäran](./request-units.md) som är etablerade i tabellen. Enheter för programbegäran delas lika mellan fysiska partitioner och tung data skevning kan orsaka en Flask hals för begär anden till en enda partition.

    I det här scenariot minskar du till minimalt data flödes inställningar i Spark och tvingar migreringen att köras långsamt. Det här scenariot kan vara vanligare när du migrerar referens-eller kontroll tabeller, där åtkomsten är mindre frekvent och skevning kan vara hög. Men om en betydande skevning finns i någon annan typ av tabell, kanske du vill granska din data modell för att undvika problem med hot på partitionen för arbets belastningen under stabilt läge.

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Metod tips för partitions nyckel](partitioning-overview.md#choose-partitionkey)
* [Beräkna RU/s med hjälp av Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
* [Elastisk skala i Azure Cosmos DB API för Cassandra](manage-scale-cassandra.md)
