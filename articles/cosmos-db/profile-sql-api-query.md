---
title: Få prestanda & körnings mått för SQL-fråga
description: Lär dig hur du hämtar SQL-frågor om körnings mått och prestanda för SQL-frågor för Azure Cosmos DB begär Anden.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097404"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Få SQL-fråga om körnings mått och analysera frågeresultat med .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln visar hur du kan profilera prestanda för SQL-frågor på Azure Cosmos DB. Den här profileringen kan göras med hjälp av `QueryMetrics` hämtade från .NET SDK och beskrivs här. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) är ett starkt skrivet objekt med information om Server delens frågekörning. Dessa mått är dokumenterade i detalj i artikeln [finjustera prestanda](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>Ange parametern FeedOptions

Alla överlagringar för [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) tar en valfri [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) -parameter. Det här alternativet är vad som tillåter körning av frågor och parametrar. 

Om du vill samla in SQL-frågans körnings mått måste du ange parametern [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) i [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) till `true` . `PopulateQueryMetrics`Om värdet är true blir det så att det `FeedResponse` innehåller relevant `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Hämta Frågeregler med AsDocumentQuery ()
Följande kod exempel visar hur du hämtar mått när du använder metoden [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agg regering av QueryMetrics

I föregående avsnitt ser du att det finns flera anrop till [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) -metoden. Varje anrop returnerade ett `FeedResponse` objekt som har en ordbok av `QueryMetrics` ; en för varje fortsättning av frågan. I följande exempel visas hur du sammanställer dessa `QueryMetrics` med LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Gruppera Frågeregler efter partitions-ID

Du kan gruppera `QueryMetrics` efter partitions-ID. Genom att gruppera efter partitions-ID kan du se om en speciell partition orsakar prestanda problem jämfört med andra. I följande exempel visas hur du grupperar `QueryMetrics` med LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ on DocumentQuery

Du kan också hämta `FeedResponse` från en LINQ-fråga med hjälp av `AsDocumentQuery()` metoden:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Dyra frågor

Du kan avbilda de enheter för programbegäran som används av varje fråga för att undersöka dyra frågor eller frågor som använder stora data flöden. Du kan hämta begär ande avgiften genom att använda egenskapen [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) i `FeedResponse` . Om du vill veta mer om hur du hämtar begär ande avgiften med hjälp av Azure Portal och olika SDK: er, se [hitta artikeln om enhets avgiften för begäran](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Hämta frågans körnings tid

När du beräknar den tid som krävs för att köra en fråga på klient sidan, se till att du bara tar med tiden att anropa `ExecuteNextAsync` metoden och inte andra delar av kodbasen. Bara de här anropen hjälper dig att beräkna hur lång tid det tog att köra frågekörningen, vilket visas i följande exempel:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Sök efter frågor (ofta långsam och dyrt)

En skannings fråga refererar till en fråga som inte hanterades av indexet, på grund av vilken. många dokument läses in innan resultat uppsättningen returneras.

Nedan visas ett exempel på en genomsöknings fråga:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Den här frågans filter använder systemfunktionen versal, som inte hanteras från indexet. Om du kör den här frågan mot en stor samling skapas följande Frågeregler för den första fortsättningen:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Observera följande värden från frågeresultatet:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Den här frågan läste in 60 951 dokument, som sammanlagt 399 998 938 byte. Inläsning av många byte resulterar i höga kostnader eller begär enhets avgifter. Det tar också lång tid att köra frågan, vilket är klart med egenskapen total tid som använts:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Det innebär att frågan tog 4,5 sekunder att köras (och det var bara en fortsättning).

Undvik att använda övre delen i filtret för att optimera den här exempel frågan. I stället `c.description` måste värdena infogas med enbart versaler när dokument skapas eller uppdateras. Frågan blir då: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Den här frågan kan nu hanteras från indexet.

Mer information om hur du finjusterar prestanda för frågor finns i artikeln [Justera frågeresultat](./sql-api-query-metrics.md) .

## <a name="references"></a><a id="References"></a>Referenser

- [Azure Cosmos DB SQL-specifikation](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Nästa steg

- [Justera frågeprestanda](sql-api-query-metrics.md)
- [Indexeringsöversikt](index-overview.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)