---
title: 'Azure Cosmos DB: .NET-API för massut executor, SDK & resurser'
description: Lär dig allt om .NET-API och SDK för mass utförare, inklusive utgivningsdatum, tillbakagångar och ändringar som görs mellan varje version av den Azure Cosmos DB massutförande .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 99b6e06b817f98e64968615b5e652b707268fe78
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364506"
---
# <a name="net-bulk-executor-library-download-information"></a>Massuttagningsbibliotek för .NET: Ladda ned information 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Spring-data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring-data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-anslutning](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

| | Länk/anteckningar |
|---|---|
| **Beskrivning**| Med .NET-massuttagningsbiblioteket kan klientprogram utföra massåtgärder på Azure Cosmos DB konton. Det här biblioteket innehåller namnrymderna BulkImport, BulkUpdate och BulkDelete. BulkImport-modulen kan massinläsa dokument på ett optimerat sätt så att dataflödet som etablerats för en samling förbrukas i maximal utsträckning. BulkUpdate-modulen kan massuppdatera befintliga data i Azure Cosmos-containrar som korrigeringar. BulkDelete-modulen kan massbortta dokument på ett optimerat sätt så att dataflödet som etablerats för en samling förbrukas i maximal utsträckning.|
|**SDK-nedladdning**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Massuttagningsbibliotek i GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentation**|[Referensdokumentation för .NET API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Komma igång**|[Kom igång med .NET SDK för massut executor-biblioteket](bulk-executor-dot-net.md)|
| **Aktuellt ramverk som stöds**| Microsoft .NET Framework 4.5.2, 4.6.1 och .NET Standard 2.0 |

> [!NOTE]
> Om du använder massutförande kan du se den senaste versionen 3.x av [.NET SDK,](tutorial-sql-api-dotnet-bulk-import.md)som har en mass utförare som är inbyggd i SDK:n. 

## <a name="release-notes"></a>Viktig information

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-förhandsversion

* TotalElapsedTime har åtgärdats i svaret på BulkDelete för att korrekt mäta den totala tiden, inklusive eventuella återförsök.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-förhandsversion

* SDK-beroendet har ändrats >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Stöd har lagts till för massut executor för att acceptera ttl för hörn och kanter

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Åtgärdat ett problem som orsakade undantag under elastisk skalning av Azure Cosmos DB vid körning i gatewayläge. Den här korrigeringen gör att den fungerar på samma sätt som version 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Stöd har lagts till för BulkDelete för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupplar som ska tas bort. Den här ändringen gör att den fungerar på samma sätt som version 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Inkludera MongoBulkExecutor för att stödja .NET Standard 2.0. Den här funktionen gör att den fungerar på samma sätt som version 1.3.0, med stöd för .NET Standard 2.0 som målramverk.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-förhandsversion

* .NET Standard 2.0 har lagts till som ett av de målramverk som stöds för att massföre executor-biblioteket ska fungera med .NET Core-program.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Ett problem med BulkDeleteAsync har åtgärdats när värden med citattecken som inte är tillgängliga skickades som indataparametrar.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Åtgärdat ett problem på MongoBulkExecutor som oväntat ökade dokumentstorleken genom att lägga till utfyllnad och i vissa fall gå över den maximala gränsen för dokumentstorlek.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Ett problem med BulkDeleteAsync har åtgärdats när samlingen har kapslade partitionsnyckelsökvägar.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor implementerar nu IDiskosable och förväntas tas bort efter användning.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Låset för SDK-versionen har tagits bort. Paketet är nu beroende av SDK>= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Hantering av identifierare vid anrop av BulkImport med en lista över POCO-objekt med numeriska värden har åtgärdats.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* TotalElapsedTime har åtgärdats i svaret för BulkDelete för att korrekt mäta den totala tiden, inklusive eventuella återförsök.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* En hög cpu-förbrukning har åtgärdats i vissa scenarier.
* Spårning använder nu TraceSource. Användare kan definiera lyssnare för `BulkExecutorTrace` källan.
* Åtgärdat ett sällsynt scenario som kan orsaka ett lås när dokument skickas nära 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Massutförandet har uppdaterats så att den nu använder den senaste versionen Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Stöd har lagts till för massut executor för att acceptera ttl för hörn och kanter

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Ett problem har åtgärdats som orsakade undantag vid elastisk skalning av Azure Cosmos DB vid körning i gatewayläge.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Stöd har lagts till för BulkDelete för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupplar som ska tas bort.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Ett problem har åtgärdats som orsakade ett formateringsproblem i användaragenten som användes av massut executor.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Förbättrade IMPORT- och uppdaterings-API:er för masskörare för att transparent anpassa sig till elastisk skalning av Cosmos-containrar när lagringen överskrider den aktuella kapaciteten utan att utlösa undantag.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* .NET SDK-beroendet för DocumentDB har ökats till version 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Ett problem har åtgärdats som gjorde att massut executor rodde på ett JSRT-fel vid import till fasta samlingar.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Stöd har lagts till för BulkDelete-åtgärden för Azure Cosmos DB SQL API-konton.
* Stöd för BulkImport-åtgärden för konton med Azure Cosmos DB API för MongoDB har lagts till.
* Ökade DocumentDB .NET SDK-beroendet till version 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Stöd har lagts till för BulkImport-åtgärden för Azure Cosmos DB Gremlin API-konton.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Mindre felkorrigering för BulkImport-åtgärden för Azure Cosmos DB SQL API-konton.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Stöd har lagts till för BulkImport- och BulkUpdate-åtgärder för Azure Cosmos DB SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Mer information om Java-biblioteket för mass utförare finns i följande artikel:

[SDK för Java-massut executor-bibliotek och versionsinformation](sql-api-sdk-bulk-executor-java.md)