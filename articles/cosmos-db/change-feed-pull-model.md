---
title: Hämtningsmodell för ändringsflöde
description: Lär dig hur du använder pull-modellen för Azure Cosmos DB ändra feed för att läsa ändrings flödet och skillnaderna mellan pull-modellen och ändra flödes processor
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/04/2021
ms.reviewer: sngun
ms.openlocfilehash: e227e230c4de1234e068f72958367dc2ac709426
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881981"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Ändra flödes hämtnings modell i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med pull-modellen för ändrings flöden kan du använda Azure Cosmos DB ändra feed i din egen takt. Eftersom du redan kan göra med en [bytes processor](change-feed-processor.md)kan du använda pull-modellen för ändrings flöden för att parallellisera bearbetningen av ändringar i flera bytes konsumenter.

> [!NOTE]
> Pull-modellen för ändrings flöden är för närvarande [en för hands version i Azure Cosmos dB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . För hands versionen är inte tillgänglig ännu för andra SDK-versioner.

## <a name="comparing-with-change-feed-processor"></a>Jämför med byte av byte av flödes processor

Många scenarier kan bearbeta ändrings flödet med hjälp av antingen [change feed-processorn](change-feed-processor.md) eller pull-modellen. Pull-modellens fortsättnings-och förändrings behållar behållar behållare är båda bok märken för det senast bearbetade objektet (eller objekt gruppen) i ändrings flödet.

Du kan dock inte konvertera fortsättnings-token till en Lease container (eller vice versa).

> [!NOTE]
> I de flesta fall när du behöver läsa från ändrings flödet är det enklaste alternativet att använda processorn för [byte av byte](change-feed-processor.md).

Du bör överväga att använda pull-modellen i följande scenarier:

- Läs ändringar från en viss partitionsnyckel
- Styr i vilken takt klienten får ändringar för bearbetning
- Utföra en engångs läsning av befintliga data i ändrings flödet (till exempel för att göra en datamigrering)

Här är några viktiga skillnader mellan processorn för förändrings matnings processor och pull-modell:

|Funktion  | Ändringsflödesprocessor| Hämta modell |
| --- | --- | --- |
| Hålla koll på den aktuella punkten vid bearbetning av ändrings flöde | Lån (lagras i en Azure Cosmos DB container) | Fortsättnings-token (lagras i minnet eller sparas manuellt) |
| Möjlighet att spela upp tidigare ändringar | Ja, med push-modell | Ja, med pull-modell|
| Söker efter framtida ändringar | Söker automatiskt efter ändringar baserat på användardefinierad `WithPollInterval` | Manuell |
| Beteende där det inte finns några nya ändringar | Vänta `WithPollInterval` och kontrol lera automatiskt | Måste fånga upp undantag och manuellt kontrol lera manuellt |
| Bearbeta ändringar från hela behållaren | Ja, och automatiskt parallellt över flera trådar/datorer som konsumeras från samma behållare| Ja, och manuellt parallellt med FeedTokens |
| Bearbeta ändringar från bara en enda partitionsnyckel | Stöds inte | Ja|
| Support nivå | Allmänt tillgänglig | Förhandsgranskning |

> [!NOTE]
> Till skillnad från när du läser med Change feed-processorn måste du uttryckligen hantera fall där det inte finns några nya ändringar. 

## <a name="consuming-an-entire-containers-changes"></a>Konsumera en hel behållares ändringar

Du kan skapa en `FeedIterator` för att bearbeta ändrings flödet med hjälp av pull-modellen. När du skapar en första `FeedIterator` måste du ange ett obligatoriskt `ChangeFeedStartFrom` värde som består av både start positionen för att läsa ändringar samt önskad `FeedRange` . `FeedRange`Är ett intervall med värden för partitionsnyckel och anger vilka objekt som ska läsas från den ändrade feeden med den aktuella `FeedIterator` .

Du kan välja att ange `ChangeFeedRequestOptions` en `PageSizeHint` . `PageSizeHint`Är det maximala antalet objekt som kommer att returneras på en enda sida.

Det `FeedIterator` kommer att finnas i två varianter. Förutom exemplen nedan som returnerar enhets objekt kan du också få svar med `Stream` stöd. Med strömmar kan du läsa data utan att först avserialiseras, vilket sparar på klient resurserna.

Här är ett exempel på hur du hämtar en `FeedIterator` som returnerar enhets objekt, i det här fallet ett `User` objekt:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

Här är ett exempel på hur du kan hämta en `FeedIterator` som returnerar en `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

Om du inte anger en `FeedRange` till a `FeedIterator` kan du bearbeta en hel behållares ändrings flöde i din egen takt. Här är ett exempel som börjar läsa alla ändringar som börjar vid aktuell tidpunkt:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Eftersom ändrings flödet faktiskt är en oändlig lista över objekt som omfattar alla framtida skrivningar och uppdateringar, värdet för `HasMoreResults` är alltid sant. När du försöker läsa ändrings flödet och det inte finns några nya tillgängliga ändringar får du ett undantag. I ovanstående exempel hanteras undantaget genom att vänta 5 sekunder innan du kontrollerar efter ändringar.

## <a name="consuming-a-partition-keys-changes"></a>Använda ändringar i en partitionsnyckel

I vissa fall kanske du bara vill bearbeta ändringar i en viss partitionsnyckel. Du kan hämta en `FeedIterator` för en speciell partitionsnyckel och bearbeta ändringarna på samma sätt som du kan för en hel behållare.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Använda FeedRange för parallellisering

I [change feed-processorn](change-feed-processor.md)sprids arbetet automatiskt över flera konsumenter. I pull-modellen för ändrings flöden kan du använda `FeedRange` för att parallellisera bearbetningen av ändrings flödet. En `FeedRange` representerar ett intervall med värden för partitionerings nycklar.

Här är ett exempel som visar hur du hämtar en lista över intervall för din behållare:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

När du hämtar en lista över FeedRanges för din behållare får du en `FeedRange` per [fysisk partition](partitioning-overview.md#physical-partitions).

Med hjälp av en `FeedRange` kan du skapa en `FeedIterator` för att parallellisera bearbetning av ändrings flödet över flera datorer eller trådar. Till skillnad från föregående exempel som visade hur du hämtar en `FeedIterator` för hela behållaren eller en enskild partitionsnyckel, kan du använda FeedRanges för att hämta flera FeedIterators som kan bearbeta ändrings flödet parallellt.

I de fall där du vill använda FeedRanges måste du ha en Orchestrator-process som erhåller FeedRanges och distribuerar dem till dessa datorer. Distributionen kan vara:

* Använder `FeedRange.ToJsonString` och distribuerar detta sträng värde. Konsumenterna kan använda det här värdet med `FeedRange.FromJsonString`
* Om distributionen är i processen skickar du `FeedRange` objekt referensen.

Här är ett exempel som visar hur du läser från början av behållarens ändrings flöde med två hypotetiska separata datorer som läses parallellt:

Dator 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Dator 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Sparar fortsättnings-token

Du kan spara positionen `FeedIterator` genom att skapa en fortsättnings-token. En fortsättnings-token är ett sträng värde som håller koll på dina FeedIterator senaste bearbetade ändringar. Detta gör `FeedIterator` att du senare kan återuppta den här tidpunkten. Följande kod läser igenom ändrings flödet sedan containern skapades. När inga fler ändringar är tillgängliga behåller den en fortsättnings-token så att förbrukningen av ändrings flödet kan återupptas senare.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

Så länge Cosmos-containern fortfarande finns går det inte att ändra en FeedIterators fortsättnings-token.

## <a name="next-steps"></a>Nästa steg

* [Översikt över ändra feed](change-feed.md)
* [Använda bearbetningen av Change feeds](change-feed-processor.md)
* [Utlösa Azure Functions](change-feed-functions.md)