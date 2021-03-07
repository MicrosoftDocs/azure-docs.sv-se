---
title: Använd Graph-utförar .NET-bibliotek med Azure Cosmos DB Gremlin API
description: Lär dig hur du använder bulk utförar-biblioteket för att importera graf-data i en Azure Cosmos DB Gremlin API-behållare.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e94dac02770bf28aae4cbfc4e337cb68b8be40
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425333"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Använda Graph-utförar .NET-bibliotek för att utföra Mass åtgärder i Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här självstudien innehåller instruktioner om hur du använder Azure CosmosDBs utförar .NET-bibliotek för att importera och uppdatera diagram objekt till en Azure Cosmos DB Gremlin API-behållare. Den här processen använder graf-klassen i [bulk utförar-biblioteket](./bulk-executor-overview.md) för att skapa hörn-och kant objekt program mässigt för att sedan infoga flera av dem per nätverks förfrågan. Det här beteendet kan konfigureras via bulk utförar-biblioteket för att göra optimal användning av både databas-och lokala minnes resurser.

Till skillnad från när du skickar Gremlin-frågor till en databas, där kommandot utvärderas och sedan körs en i taget, måste du i stället använda utförar för att skapa och validera objekten lokalt. När objekten har skapats gör biblioteket att du kan skicka diagramobjekt till databasen sekventiellt. Med den här metoden kan hastigheten för datainmatning bli upp till 100 gånger snabbare, vilket gör metoden utmärkt för initiala datamigreringar eller periodiska dataflyttningsåtgärder. Lär dig mer genom att besöka sidan GitHub i [Azure Cosmos DB Graph utförar exempel program](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Massåtgärder med diagramdata

[Bulk utförar-biblioteket](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) innehåller ett `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` namn område för att tillhandahålla funktioner för att skapa och importera diagram objekt. 

Följande process beskriver hur datamigrering kan användas för en Gremlin API-container:
1. Hämta poster från datakällan.
2. Konstruera `GremlinVertex`- och `GremlinEdge`-objekt från de erhållna posterna och lägg till dem i en `IEnumerable`-datastruktur. I den här delen av programmet bör logik för att identifiera och lägga till relationer implementeras, om datakällan inte är en diagramdatabas.
3. Använd [Graph BulkImportAsync-metoden](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync) för att infoga diagramobjekt i samlingen.

Den här mekanismen förbättrar effektiviteten för datamigrering jämfört med att använda en Gremlin-klient. Den här förbättringen uppstår eftersom infogande av data med Gremlin kräver att programmet skickar en fråga åt gången som måste verifieras, utvärderas och sedan köras för att skapa data. Bulk utförar-biblioteket hanterar verifieringen i programmet och skickar flera graf-objekt i taget för varje nätverks förfrågan.

### <a name="creating-vertices-and-edges"></a>Skapa hörn och kanter

`GraphBulkExecutor` tillhandahåller den `BulkImportAsync`-metod som kräver en `IEnumerable`-lista över `GremlinVertex`- eller `GremlinEdge`-objekt, som både definieras i `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`-namnrymden. I det här exemplet delade vi in kanterna och hörnen i två BulkExecutor-importuppgifter. Se exemplet nedan:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Mer information om parametrarna i bulk utförar-biblioteket finns i [avsnittet BulkImportData to Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

Nyttolasten behöver instansieras till `GremlinVertex`- och `GremlinEdge`-objekt. Så här kan dessa objekt skapas:

**Hörn**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Kanter**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Verktyget för Mass utförar söker inte automatiskt efter befintliga formhörn innan kanterna läggs till. Det här måste verifieras i programmet innan du kör BulkImport-uppgifter.

## <a name="sample-application"></a>Exempelprogram

### <a name="prerequisites"></a>Förutsättningar
* Visual Studio 2019 med arbets belastningen Azure Development. Du kan komma igång med [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) kostnads fritt.
* En Azure-prenumeration. Du kan skapa [ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Du kan också skapa ett Cosmos Database-konto med [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
* En Azure Cosmos DB Gremlin API-databas med en **obegränsad samling**. Den här guiden visar hur du kommer igång med [Azure Cosmos DB Gremlin API i .NET](./create-graph-dotnet.md).
* Git. Mer information finns på [sidan för Git-nedladdningar](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet
I den här självstudien följer vi stegen för att komma igång med [Azure Cosmos DB graphs utförar-exempel](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) som finns på GitHub. Det här programmet består av en .NET-lösning som slumpmässigt genererar hörn- och kantobjekt och därefter kör massinfogningar till det angivna diagramdatabaskontot. För att hämta programmet kör du `git clone`-kommandot nedan:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Den här lagringsplatsen innehåller GraphBulkExecutor-exemplet med följande filer:

Fil|Description
---|---
`App.config`|Det här är där program- och databasspecifika parametrar anges. Den här filen ska först ändras för att ansluta till måldatabasen och -samlingarna.
`Program.cs`| Den här filen innehåller logiken bakom skapandet av `DocumentClient` samlingen, hantering av rensningar och sändning av Mass utförar-begäranden.
`Util.cs`| Den här filen innehåller en hjälpklass som innehåller logiken bakom att generera testdata och kontrollera om databasen och samlingarna finns.

I `App.config`-filen är följande konfigurationsvärden som kan tillhandahållas:

Inställning|Beskrivning
---|---
`EndPointUrl`|Det här är **din .NET SDK-slutpunkt** som finns i bladet Översikt för ditt Cosmos DB Gremlin API-databaskonto. Det har formatet för `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Det här är den primära eller sekundära nyckel som visas under ditt Azure Cosmos DB-konto. Läs mer om att [skydda åtkomst till Azure Cosmos DB-data](./secure-access-to-data.md#primary-keys)
`DatabaseName`, `CollectionName`|Det här är **måldatabas- och samlingsnamnen**. När `ShouldCleanupOnStart` är inställt på `true` används dessa värden, tillsammans med `CollectionThroughput`, för att ta bort dem och skapa en ny databas och samling. På ett liknande sätt används de om `ShouldCleanupOnFinish` är inställt på `true` för att ta bort databasen när datainmatningen är klar. Observera att målsamlingen måste vara **en obegränsad samling**.
`CollectionThroughput`|Detta används för att skapa en ny samling om `ShouldCleanupOnStart`-alternativet är inställt på `true`.
`ShouldCleanupOnStart`|Detta kommer ta bort databaskontot och samlingarna innan programmet körs och sedan skapa nya med värdena `DatabaseName`, `CollectionName` och `CollectionThroughput`.
`ShouldCleanupOnFinish`|Detta kommer att ta bort databaskontot och samlingarna med angivna `DatabaseName` och `CollectionName` när programmet har körts.
`NumberOfDocumentsToImport`|Detta avgör hur många testhörn och testgränser som skapas i exemplet. Detta antal gäller för både hörn och kanter.
`NumberOfBatches`|Detta avgör hur många testhörn och testgränser som skapas i exemplet. Detta antal gäller för både hörn och kanter.
`CollectionPartitionKey`|Detta används för att skapa testhörn och testkanter, där den här egenskapen tilldelas automatiskt. Detta används även vid återskapande av databasen och samlingarna om `ShouldCleanupOnStart`-alternativet är inställt på `true`.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

1. Lägg till dina specifika databaskonfigurationsparametrar i `App.config`. Detta används för att skapa en DocumentClient-instans. Om databasen och containern inte har skapats ännu skapas de automatiskt.
2. Kör appen. Detta anropar `BulkImportAsync` två gånger, en gång för att importera hörn och en gång för att importera kanter. Om några av objekten genererar ett fel när de infogas läggs de till i antingen `.\BadVertices.txt` eller `.\BadEdges.txt`.
3. Utvärdera resultaten genom att köra frågor mot diagramdatabasen. Om `ShouldCleanupOnFinish`-alternativet är inställt på true (sant) tas databasen bort automatiskt.

## <a name="next-steps"></a>Nästa steg

* Information om NuGet-paket och viktig information om utförar .NET-bibliotek finns i [UTFÖRAR SDK-information](sql-api-sdk-bulk-executor-dot-net.md). 
* Kolla in [prestanda tipsen](./bulk-executor-dot-net.md#performance-tips) för att ytterligare optimera användningen av Mass utförar.
* Läs [referensartikeln om BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph) för mer information om de klasser och metoder som definieras i den här namnrymden.