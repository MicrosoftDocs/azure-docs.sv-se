---
title: Interagera med Azure Cosmos DB att använda Apache Spark i Azure Synapse-länken
description: Så här interagerar du med Azure Cosmos DB att använda Apache Spark i Azure Synapse-länken
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 32e8ad5028920cefd717cdaa5429786c83367f6d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671275"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Interagera med Azure Cosmos DB att använda Apache Spark i Azure Synapse-länken

I den här artikeln får du lära dig hur du interagerar med Azure Cosmos DB med hjälp av Synapse Apache Spark. Med fullt stöd för Scala, python, SparkSQL och C#, är Synapse Apache Spark central för analys, data teknik, data vetenskap och data utforsknings scenarier i [Azure Synapse-länk för Azure Cosmos DB](../../cosmos-db/synapse-link.md).

Följande funktioner stöds när du interagerar med Azure Cosmos DB:
* Med Synapse Apache Spark kan du analysera data i dina Azure Cosmos DB behållare som är aktiverade med Azure Synapse-länken i nära real tid utan att påverka prestandan för dina transaktions arbets belastningar. Följande två alternativ är tillgängliga för att fråga Azure Cosmos DB [Analytical Store](../../cosmos-db/analytical-store-introduction.md) från Spark:
    + Läs in till Spark DataFrame
    + Skapa Spark-tabell
* Med Synapse Apache Spark kan du också mata in data i Azure Cosmos DB. Det är viktigt att Observera att data alltid matas in i Azure Cosmos DB behållare via transaktions arkivet. När Synapse-länken är aktive rad synkroniseras alla nya infogningar, uppdateringar och borttagningar automatiskt i analys lagret.
* Synapse Apache Spark stöder också Spark-strukturerad strömning med Azure Cosmos DB som källa samt en mottagare. 

I följande avsnitt får du stegvisa anvisningar för funktionerna ovan. Gester i Azure Synapse Analytics-arbetsytan är utformade för att ge en enkel välkomst upplevelse för att komma igång. Gester visas när du högerklickar på en Azure Cosmos DB behållare på fliken **data** i arbets ytan Synapse. Med gester kan du snabbt generera kod och skräddarsy den efter dina behov. Gester passar också perfekt för att upptäcka data med ett enda klick.

## <a name="query-azure-cosmos-db-analytical-store"></a>Fråga Azure Cosmos DB analys lager

Innan du lär dig mer om de två möjliga alternativen för att fråga Azure Cosmos DB analys lager, läsa in till Spark-DataFrame och skapa Spark-tabell, är det värt att utforska skillnaderna i upplevelsen så att du kan välja det alternativ som passar dina behov.

Skillnaden i upplevelsen är ungefär om underliggande data ändringar i Azure Cosmos DB containern automatiskt ska avspeglas i den analys som utförs i Spark. När antingen en spark-DataFrame är registrerad eller en spark-tabell skapas mot en behållares analys lager, hämtas metadata runt den aktuella ögonblicks bilden av data i analys lagret till Spark för effektiv mottagnings av efterföljande analys. Det är viktigt att Observera att eftersom Spark följer en Lazy utvärderings princip, om inte en åtgärd anropas i Spark-DataFrame eller en SparkSQL-fråga körs mot Spark-tabellen, hämtas inte faktiska data från den underliggande behållarens analytiska lager.

Vid **inläsning till Spark DataFrame** cachelagras hämtade metadata under Spark-sessionens livstid och därmed utvärderas efterföljande åtgärder som utförs för DataFrame mot ögonblicksbilden av analysarkivet vid tidpunkten då DataFrame skapades.

Men om **en Spark-tabell skapas** cachelagras inte metadata för analysarkivet i Spark utan läses in på nytt vid varje SparkSQL-frågekörning mot Spark-tabellen.

Det innebär att du kan välja mellan att läsa in till Spark DataFrame och att skapa en Spark-tabell beroende på om du vill att din Spark-analys ska utvärderas mot en fast ögonblicksbild av analysarkivet eller mot den senaste ögonblicksbilden av analysarkivet.

> [!NOTE]
> Om du vill fråga Azure Cosmos DB-API: et för mongo DB-konton kan du läsa mer om [schema representationen full Fidelity](../../cosmos-db/analytical-store-introduction.md#analytical-schema) i analys lagret och de utökade egenskaps namn som ska användas.

### <a name="load-to-spark-dataframe"></a>Läs in till Spark DataFrame

I det här exemplet ska du skapa en spark-DataFrame som pekar på den Azure Cosmos DB analytiska butiken. Du kan sedan utföra ytterligare analyser genom att anropa Spark-åtgärder mot DataFrame. Den här åtgärden påverkar inte transaktions arkivet.

Syntaxen i **python** skulle vara följande:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

Motsvarande syntax i **Scala** skulle vara följande:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Skapa Spark-tabell

I det här exemplet ska du skapa en spark-tabell som pekar på den Azure Cosmos DB analys butiken. Du kan sedan utföra ytterligare analyser genom att anropa SparkSQL-frågor mot tabellen. Den här åtgärden påverkar inte transaktions lagret eller medför någon data förflyttning. Om du bestämmer dig för att ta bort den här Spark-tabellen påverkas inte den underliggande Azure Cosmos DBs behållaren och motsvarande analys lager. 

Det här scenariot är praktiskt att återanvända Spark-tabeller via verktyg från tredje part och ge åtkomst till underliggande data för körnings tillfället.

Syntaxen för att skapa en spark-tabell är följande:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Om du har scenarier där schemat för en underliggande Azure Cosmos DB-container ändras med tiden, och om du vill att det uppdaterade schemat ska speglas automatiskt i frågorna mot Spark-tabellen kan du göra detta genom att ange alternativet `spark.cosmos.autoSchemaMerge`  för att `true` i Spark-tabellalternativen.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Skriv Spark-DataFrame till Azure Cosmos DB container

I det här exemplet ska du skriva en spark-DataFrame till en Azure Cosmos DB-behållare. Den här åtgärden påverkar prestanda för transaktions arbets belastningar och förbrukar enheter för programbegäran som tillhandahålls på Azure Cosmos DB containern eller den delade databasen.

Syntaxen i **python** skulle vara följande:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Motsvarande syntax i **Scala** skulle vara följande:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Läs in strömmande DataFrame från behållare
I den här gesten använder du Spark streaming-funktioner för att läsa in data från en behållare till en dataframe. Data lagras i det primära data Lake-kontot (och fil systemet) som du anslöt till arbets ytan. 
> [!NOTE]
> Om du vill referera till externa bibliotek i Synapse Apache Spark kan du läsa mer [här](#external-library-management). Om du till exempel vill mata in en spark-DataFrame till en behållare med Cosmos DB API för mongo DB kan du använda Mongo DB Connector för Spark [här](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Läs in strömmande DataFrame från Azure Cosmos DB container
I det här exemplet ska du använda Spark: s strukturerade strömnings kapacitet för att läsa in data från en Azure Cosmos DB-behållare till en spark streaming-DataFrame med funktionen ändra feed i Azure Cosmos DB. Kontroll punkts data som används av Spark kommer att lagras i det primära data Lake-konto (och fil systemet) som du anslöt till arbets ytan.

Om mappen */localReadCheckpointFolder* inte skapas (i exemplet nedan) skapas den automatiskt. Den här åtgärden påverkar prestanda för transaktions arbets belastningar och förbrukar enheter för programbegäran som tillhandahålls på Azure Cosmos DB containern eller den delade databasen.

Syntaxen i **python** skulle vara följande:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Motsvarande syntax i **Scala** skulle vara följande:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Skriv strömmande DataFrame till Azure Cosmos DB container
I det här exemplet ska du skriva en strömmande DataFrame till en Azure Cosmos DB behållare. Den här åtgärden påverkar prestanda för transaktions arbets belastningar och förbrukar enheter för programbegäran som tillhandahålls på Azure Cosmos DB containern eller den delade databasen. Om mappen */localWriteCheckpointFolder* inte skapas (i exemplet nedan) skapas den automatiskt. 

Syntaxen i **python** skulle vara följande:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Motsvarande syntax i **Scala** skulle vara följande:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Hantering av externa bibliotek

I det här exemplet lär du dig att referera till externa bibliotek från JAR-filer när du använder Spark-anteckningsböcker i Synpase Apache Spark-arbetsytor. Du kan placera JAR-filerna i en behållare i det primära data Lake-kontot som du anslöt till arbets ytan och sedan lägga till följande- `%configure` instruktion i din spark-anteckningsbok:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Om du vill skicka fjärran sluten Spark-jobb till en server lös Apache Spark pool, kan du lära dig hur du refererar till externa bibliotek genom att följa den här [självstudien](../spark/apache-spark-job-definitions.md).

## <a name="next-steps"></a>Nästa steg

* [Exempel för att komma igång med Azure Synapse-länk på GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Lär dig vad som stöds i Azure Synapse-länken för Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Anslut till Synapse-länk för Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
