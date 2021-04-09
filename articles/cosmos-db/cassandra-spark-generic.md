---
title: Arbeta med Azure Cosmos DB API för Cassandra från Spark
description: Den här artikeln är huvud sidan för Cosmos DB API för Cassandra integrering från Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090519"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluta till Azure Cosmos DB Cassandra-API:et från Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Den här artikeln är en serie artiklar om Azure Cosmos DB API för Cassandra-integrering från Spark. Artiklarna behandlar anslutning, DDL-åtgärder (Data Definition Language), DML-åtgärder (Basic data Manipulation Language) och avancerad Azure Cosmos DB API för Cassandra-integrering från Spark. 

## <a name="prerequisites"></a>Förutsättningar
* [Etablera ett Azure Cosmos DB API för Cassandra konto.](create-cassandra-dotnet.md#create-a-database-account)

* Tillhandahåll ditt val av Spark-miljö [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Andra].

## <a name="dependencies-for-connectivity"></a>Beroenden för anslutning
* **Spark-anslutning för Cassandra:** Spark-anslutningen används för att ansluta till Azure Cosmos DB API för Cassandra.  Identifiera och Använd den version av anslutningen som finns i [maven Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibel med Spark-och Scala-versionerna av Spark-miljön.

* **Azure Cosmos DB hjälp bibliotek för API för Cassandra:** Förutom Spark-anslutningsprogrammet behöver du ett annat bibliotek som kallas [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) från Azure Cosmos dB. Det här biblioteket innehåller en anpassad anslutnings fabrik och återförsöks princip klasser.

  Principen för återförsök i Azure Cosmos DB har kon figurer ATS för att hantera HTTP-statuskoden 429-undantag ("begär ande frekvens Large"). Azure Cosmos DB API för Cassandra översätter dessa undantag till överlagrade fel på det inbyggda Cassandra-protokollet och du kan försöka igen med backend. Eftersom Azure Cosmos DB använder etablerade data flödes modeller, uppstår begränsning av begär ande undantag när ingångs-/utgångs frekvensen ökar. Principen för återförsök skyddar dina Spark-jobb mot data toppar som tillfälligt överskrider det data flöde som allokerats för din behållare.

  > [!NOTE] 
  > Principen för återförsök kan skydda dina Spark-jobb mot tillfälliga toppar. Om du inte har konfigurerat tillräckligt många ru: er som krävs för att köra arbets belastningen, är inte återförsöks principen tillämpbar och princip klassen för återförsök återutlöser undantaget.

* **Information om Azure Cosmos DB konto anslutning:** Ditt Azure API för Cassandra konto namn, konto slut punkt och nyckel.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Konfigurations parametrar för Spark-anslutningsprogrammet

I följande tabell visas Azure Cosmos DB API för Cassandra-specificerade data flödes konfigurations parametrar som tillhandahålls av anslutningen. En detaljerad lista över alla konfigurations parametrar finns i sidan [konfigurations referens](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) för Spark Cassandra Connector GitHub-lagringsplatsen.

| **Egenskapens namn** | **Standardvärde** | **Beskrivning** |
|---------|---------|---------|
| spark.cassandra.output.batCH. size. Rows |  1 |Antal rader per enskild batch. Ange den här parametern till 1. Den här parametern används för att uppnå högre data flöde för stora arbets belastningar. |
| spark.cassandra.connection.connections_per_executor_max  | Inget | Maximalt antal anslutningar per nod per utförar. 10 * n motsvarar 10 anslutningar per nod i ett Cassandra-kluster med n-nod. Så om du kräver 5 anslutningar per nod per utförar för ett Cassandra-kluster med fem noder, bör du ange den här konfigurationen till 25. Ändra det här värdet baserat på graden av parallellitet eller antalet körningar som dina Spark-jobb har kon figurer ATS för.   |
| Spark. Cassandra. output. samtidig. skrivningar  |  100 | Definierar antalet parallella skrivningar som kan ske per utförar. Eftersom du ställer in "batch. size. Rows" på 1, måste du skala upp det här värdet efter detta. Ändra det här värdet baserat på graden av parallellitet eller det data flöde som du vill uppnå för din arbets belastning. |
| Spark. Cassandra. samtidig. läsningar |  512 | Definierar antalet parallella läsningar som kan ske per utförar. Ändra det här värdet baserat på graden av parallellitet eller det data flöde som du vill uppnå för din arbets belastning  |
| spark.cassandra.output.throughput_mb_per_sec  | Inget | Definierar det totala Skriv data flödet per utförar. Den här parametern kan användas som en övre gräns för ditt data flöde för Spark-jobbet och basera det på det etablerade data flödet för din Cosmos-behållare.   |
| spark.cassandra.input.reads_per_sec| Inget   | Definierar det totala Läs data flödet per utförar. Den här parametern kan användas som en övre gräns för ditt data flöde för Spark-jobbet och basera det på det etablerade data flödet för din Cosmos-behållare.  |
| spark.cassandra.output.batCH. Grouping. Buffer. size |  1000  | Definierar antalet batchar per enskild Spark-aktivitet som kan lagras i minnet innan de skickas till API för Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definierar efter hur lång tid som oanvända anslutningar är tillgängliga. | 

Justera data flödet och graden av parallellitet för dessa parametrar baserat på den arbets belastning du förväntar dig för dina Spark-jobb och det data flöde som du har allokerat för ditt Cosmos DB-konto.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ansluta till Azure Cosmos DB Cassandra-API:et från Spark

### <a name="cqlsh"></a>cqlsh
Följande kommandon beskriver hur du ansluter till Azure CosmosDB API för Cassandra från cqlsh.  Detta är användbart för verifiering när du kör genom exemplen i Spark.<br>
**Från Linux/UNIX/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
Artikeln nedan beskriver Azure Databricks kluster etablering, kluster konfiguration för att ansluta till Azure Cosmos DB API för Cassandra och flera exempel antecknings böcker som täcker DDL-åtgärder, DML-åtgärder med mera.<BR>
[Arbeta med Azure Cosmos DB API för Cassandra från Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
Artikeln nedan beskriver HDinsight-Spark tjänst, etablering, kluster konfiguration för att ansluta till Azure Cosmos DB API för Cassandra och flera exempel antecknings böcker som täcker DDL-åtgärder, DML-åtgärder med mera.<BR>
[Arbeta med Azure Cosmos DB API för Cassandra från Azure HDInsight – Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Spark-miljö i allmänhet
Även om avsnitten ovan var speciella för Azure Spark-baserade PaaS-tjänster täcker det här avsnittet alla allmänna Spark-miljöer.  Anslutnings beroenden, import och Spark-sessionsinformation beskrivs nedan. Avsnittet "nästa steg" behandlar kod exempel för DDL-åtgärder, DML-åtgärder med mera.  

#### <a name="connector-dependencies"></a>Kopplings beroenden:

1. Lägg till maven-koordinaterna för att hämta [Cassandra-anslutaren för Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Lägg till maven-koordinaterna för [Azure Cosmos DB Helper-biblioteket](cassandra-spark-generic.md#dependencies-for-connectivity) för API för Cassandra

#### <a name="imports"></a>Kina

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguration av Spark-session:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar Spark-integrering med Azure Cosmos DB API för Cassandra. 
 
* [DDL-åtgärder](cassandra-spark-ddl-ops.md)
* [Skapa/infoga åtgärder](cassandra-spark-create-ops.md)
* [Läsåtgärder](cassandra-spark-read-ops.md)
* [Upsert-åtgärder](cassandra-spark-upsert-ops.md)
* [Borttagningsåtgärder](cassandra-spark-delete-ops.md)
* [Sammansättningsåtgärder](cassandra-spark-aggregation-ops.md)
* [Åtgärder för tabell kopiering](cassandra-spark-table-copy-ops.md)