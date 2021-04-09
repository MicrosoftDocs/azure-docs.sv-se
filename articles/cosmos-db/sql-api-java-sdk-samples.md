---
title: 'Azure Cosmos DB SQL API: Java SDK v4-exempel'
description: Hitta Java-exempel på GitHub för vanliga uppgifter med Azure Cosmos DB SQL API, inklusive CRUD-åtgärder.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019015"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: Java SDK v4-exempel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET v2 SDK-exempel](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Exempel för Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Exempel på våren data v3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Läs mer om Java SDK v4 i Azure Cosmos DB Java SDK v4 [viktig](sql-api-sdk-java-v4.md)information, [maven-lagringsplats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md)och Azure Cosmos DB Java SDK v4- [fel söknings guide](troubleshoot-java-sdk-v4-sql.md) för mer information. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

De senaste exempel programmen som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser ingår i GitHub-lagringsplatsen [Azure-Cosmos-Java-SQL-API-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) . Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Java-projektfilerna. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

Du behöver följande för att köra det här exempelprogrammet:

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Du kan också använda Maven för att hämta de senaste Azure Cosmos DB Java SDK v4-binärfilerna för användning i projektet. Maven lägger automatiskt till alla nödvändiga beroenden. Du kan även ladda ned beroendena direkt som listas i filen pom.xml och lägga till dem till din byggsökväg.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Köra exempelprogrammen**

Klona lagringsplatsexemplet:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Du kan köra exemplen med antingen en IDE (Sol förmörkelse, IntelliJ eller VSCODE) eller från kommando raden med hjälp av Maven.

De här miljövariablerna måste anges

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

för att ge exempel Läs-och skriv åtkomst till ditt konto.

Om du vill köra ett exempel anger du dess huvud klass 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

där *Sample. Synchronicity. MainClass* kan vara
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. async. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. async. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. async. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(Changefeed har bara ett asynkront exempel, inget Sync-exempel.)* ... osv...

> [!NOTE]
> Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen utfärdar flera anrop för att skapa en `CosmosContainer` . Varje gång det sker faktureras din prenumeration för en timmes användning för prestandanivån för den skapade samlingen. 
> 
> 

## <a name="database-examples"></a>Databasexempel
[Databasen CRUD exempel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) filen visar hur du utför följande uppgifter. Information om Azure Cosmos-databaser innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](account-databases-containers-items.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient. createDatabaseIfNotExists |
| [Läsa en databas via ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient. getDatabase |
| [Läsa alla databaser](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Ta bort en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Samlingsexempel
[Exempel filen Collection CRUD-exempel](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) visar hur du utför följande uppgifter. Mer information om Azure Cosmos-samlingarna innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](account-databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase. createContainerIfNotExists |
| [Ändra konfigurerade prestanda i en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Hämta en samling via ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Läsa alla samlingar i en databas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Ta bort en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Exempel för autoskalning samling

Om du vill veta mer om autoskalning innan du kör dessa exempel kan du ta en titt på de här instruktionerna för att aktivera autoskalning i ditt [konto](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) och i dina [databaser och behållare](./provision-throughput-autoscale.md).

[CRUD exempel filen för autoskalning av databasen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) visar hur du utför följande uppgifter.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas med angivet Max data flöde för autoskalning](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient. createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

[CRUD exempel filen för autoskalning i samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) visar hur du utför följande uppgifter. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling med angivet autoskalning Max-genomflöde](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase. createContainerIfNotExists |
| [Ändra konfigurerat högsta data flöde för autoskalning av en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Läs automatisk skalning av data flödes konfigurationen för en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Samlings exempel för analys lagring

I [CRUD exempel filen för analys lagrings samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) visas hur du utför följande uppgifter. Om du vill veta mer om Azure Cosmos-samlingarna innan du kör följande exempel kan du läsa om Azure Cosmos DB Synapse och analys lager.

| Uppgift | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase. createContainerIfNotExists |

## <a name="document-examples"></a>Exempel på dokument
Filen [CRUD exempel för dokument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) visar hur du utför följande uppgifter. Information om Azure Cosmos-dokumenten innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](account-databases-containers-items.md) konceptuell artikel.

| Uppgift | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Läsa ett dokument efter ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Fråga för dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Ersätta ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Upsertera ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Ta bort ett dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Ersätta ett dokument med villkorlig ETag-kontroll](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Läs dokument endast om dokumentet har ändrats](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Indexeringsexempel
[Exempel filen Collection CRUD-exempel](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) visar hur du utför följande uppgifter. Om du vill veta mer om indexering i Azure Cosmos DB innan du kör följande exempel, se [indexerings principer](index-policy.md), [indexerings typer](index-overview.md#index-types)och [indexerings Sök vägar](index-policy.md#include-exclude-paths) konceptuella artiklar. 

| Uppgift | API-referens |
| --- | --- |
| Undanta ett dokument från indexet | ExcludedIndex<br>IndexingPolicy |
| Använd Lazy-indexering | IndexingPolicy.IndexingMode |
| [Inkludera angivna dokument Sök vägar i indexet](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Undanta angivna dokument Sök vägar från indexet](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Skapa ett sammansatt index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Tvinga en intervallsökning av en hash-indexerad sökväg | FeedOptions.EnableScanInQuery |
| Använda intervall index i strängar | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Utföra en indextransformering | - |
| [Skapa ett geospatialt index](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Mer information om indexering finns i [Azure Cosmos DB indexeringsprinciper](index-policy.md).

## <a name="query-examples"></a>Exempelfrågor
I [fråge exempel](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) filen visas hur du utför följande uppgifter med hjälp av SQL-frågespråket. Om du vill veta mer om SQL-frågan i Azure Cosmos DB innan du kör följande exempel kan du läsa [mer i SQL-fr åga for Azure Cosmos DB](./sql-query-getting-started.md). 

| Uppgift | API-referens |
| --- | --- |
| [Fråga för alla dokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Fråga för likhet med ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Fråga för olikhet med != och NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Fråga med intervalloperatorer som >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Fråga med intervalloperatorer mot strängar](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Fråga med ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Fråga med distinkt](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Fråga med mängd funktioner](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Arbeta med underdokument](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Fråga med intradokumentkopplingar](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Fråga med operatorer av sträng, matematik och matris](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Fråga med parametriserad SQL med SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Fråga med explicit sidindelning](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Fråga partitionerade samlingar parallellt](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Fråga med ORDER BY för partitionerade samlingar | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Exempel på ändringsflöde 
Exempel filen för [change feed processor](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) visar hur du utför följande uppgifter. Information om hur du ändrar feed i Azure Cosmos DB innan du kör följande exempel finns i [läsa Azure Cosmos DB ändra feed](read-change-feed.md) och [ändra feed-processor](change-feed-processor.md).

| Uppgift | API-referens |
| --- | --- |
| [Grundläggande funktioner för att ändra feed](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Läs ändrings flöde från en angiven tid | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Läs ändrings flöde från början](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Programmeringsexempel på serversidan

Exempel filen för [lagrade procedurer](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) visar hur du utför följande uppgifter. Om du vill lära dig mer om Server sidans programmering i Azure Cosmos DB innan du kör följande exempel, se [lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Köra en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exesöta |
| [Ta bort en lagrad procedur](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Exempel för användarhantering
Exempel filen för användar hantering visar hur du utför följande uppgifter:

| Uppgift | API-referens |
| --- | --- |
| Skapa en användare | - |
| Ange behörigheter för en samling eller dokument | - |
| Hämta en lista över an användares behörigheter |- |
