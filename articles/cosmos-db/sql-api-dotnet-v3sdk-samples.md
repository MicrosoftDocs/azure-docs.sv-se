---
title: 'Azure Cosmos DB: .NET (Microsoft. Azure. Cosmos)-exempel för SQL-API'
description: Hitta C# .NET v3 SDK-exemplen på GitHub för vanliga uppgifter med hjälp av Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 30ccd5783b3fa9524072235a58b3c8708962ef1c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434023"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB.NET v3 SDK-exempel (Microsoft. Azure. Cosmos) för SQL-API: et
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

[Azure-Cosmos-dotNet-v3-](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) GitHub-lagringsplatsen innehåller de senaste .net-exempel lösningarna för att utföra CRUD och andra vanliga åtgärder på Azure Cosmos DB-resurser. Om du är bekant med den tidigare versionen av .NET SDK kan du använda villkors samlingen och dokumentet. Eftersom Azure Cosmos DB stöder flera API-modeller, använder version 3,0 av .NET SDK de allmänna villkoren "container" och "Item". En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av C#-projektfilerna.
* Länkar till det relaterade API-referensinnehållet.

## <a name="prerequisites"></a>Förutsättningar

Visual Studio 2019 med arbets flödet Azure Development installerat

- Du kan hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

   [Microsoft. Azure. Cosmos NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

En Azure-prenumeration eller ett kostnads fritt Cosmos DB utvärderings konto

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Du kan [Aktivera Visual Studio-förmåner för prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig kredit varje månad, som du kan använda för betalda Azure-tjänster.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Exemplen är fristående och installeras och rensas efter själva. Varje tillfälle fakturerar din prenumeration för en timmes användning i din behållares prestanda nivå.
> 

## <a name="database-examples"></a>Databasexempel

[RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) -metoden för exempel *DatabaseManagement* -projektet visar hur du utför följande uppgifter. Om du vill veta mer om Azure Cosmos-databaser innan du kör följande exempel kan du läsa [arbeta med databaser, behållare och objekt](account-databases-containers-items.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) |
| [Läsa en databas via ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database. ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync) |
| [Läs alla databaser för ett konto](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator) |
| [Ta bort en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database. DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) |

## <a name="container-examples"></a>Containerexempel

[RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) -metoden för exempel *ContainerManagement* -projektet visar hur du utför följande uppgifter. Information om Azure Cosmos-behållare innan du kör följande exempel finns i [arbeta med databaser, behållare och objekt](account-databases-containers-items.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa en container](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Skapa en behållare med en anpassad index princip](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Ändra konfigurerade prestanda för en behållare](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) |
| [Hämta en behållare efter ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container. ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync) |
| [Läs alla behållare i en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database. GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator) |
| [Ta bort en container](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container. DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync) |

## <a name="item-examples"></a>Objektexempel

[RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) -metoden för exempel *ItemManagement* -projektet visar hur du utför följande uppgifter. Information om Azure Cosmos-objekt innan du kör följande exempel finns i [arbeta med databaser, behållare och objekt](account-databases-containers-items.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container. CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) |
| [Läsa ett objekt via ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[fönster. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) |
| [Fråga efter objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[fönster. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Ersätta ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[fönster. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync) |
| [Upsert ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[fönster. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) |
| [Ta bort ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[fönster. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync) |
| [Ersätta ett objekt med villkorlig ETag-kontroll](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions. IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag) |

## <a name="indexing-examples"></a>Indexeringsexempel

[RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) -metoden för exempel *IndexManagement* -projektet visar hur du utför följande uppgifter. Om du vill veta mer om indexering i Azure Cosmos DB innan du kör följande exempel, se [index principer](index-policy.md), [index typer](index-overview.md#index-types)och [index Sök vägar](index-policy.md#include-exclude-paths). 

| Uppgift | API-referens |
| --- | --- |
| [Undanta ett objekt från indexet](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective) |
| [Använd Lazy-indexering](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode) |
| [Undanta angivna objekt Sök vägar från indexet](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths) |

## <a name="query-examples"></a>Exempelfrågor

[RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) -metoden i exempel *frågan* Project visar hur du utför följande uppgifter med hjälp av SQL Query-grammatik, LINQ-providern med Query och lambda. Om du vill veta mer om SQL-frågan i Azure Cosmos DB innan du kör följande exempel kan du läsa [mer i SQL-fr åga for Azure Cosmos DB](./sql-query-getting-started.md).

| Uppgift | API-referens |
| --- | --- |
| [Fråga efter objekt från en enskild partition](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[fönster. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Fråga efter objekt från flera partitioner](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[fönster. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Fråga med ett SQL-uttryck](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[fönster. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |

## <a name="change-feed-examples"></a>Exempel på ändringsflöde

[RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) -metoden för exempel *ChangeFeed* -projektet visar hur du utför följande uppgifter. Information om hur du ändrar feed i Azure Cosmos DB innan du kör följande exempel finns i [läsa Azure Cosmos DB ändra feed](read-change-feed.md) och [ändra feed-processor](change-feed-processor.md).

| Uppgift | API-referens |
| --- | --- |
| [Grundläggande funktioner för att ändra feed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Läs ändrings flöde från en angiven tid](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Läs ändrings flöde från början](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder. WithStartTime (DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime) |
| [Migrera från ändra flödes processor till ändra feed i v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |

## <a name="server-side-programming-examples"></a>Programmeringsexempel på serversidan

[RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) -metoden för exempel *ServerSideScripts* -projektet visar hur du utför följande uppgifter. Om du vill lära dig mer om Server sidans programmering i Azure Cosmos DB innan du kör följande exempel, se [lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md).

| Uppgift | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts. CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync) |
| [Köra en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync) |
| [Ta bort en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts. DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync) |
