---
title: Transaktionella batch-åtgärder i Azure Cosmos DB med hjälp av .NET SDK
description: Lär dig hur du använder TransactionalBatch i Azure Cosmos DB .NET SDK för att utföra en grupp punkt åtgärder som antingen lyckas eller Miss lyckas.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347565"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Transaktionella batch-åtgärder i Azure Cosmos DB med hjälp av .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Transaktionell batch beskriver en grupp punkt åtgärder som måste utföras eller Miss lyckas tillsammans med samma partitionsnyckel i en behållare. I .NET SDK `TransactionalBatch` används klassen för att definiera den här batchen med åtgärder. Om alla åtgärder lyckas i den ordning som de beskrivs i transaktions batch-åtgärden, kommer transaktionen att bekräftas. Men om en åtgärd Miss lyckas återställs hela transaktionen.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Vad är en transaktion i Azure Cosmos DB

En transaktion i en typisk databas kan definieras som en sekvens med åtgärder som utförs som en enda logisk arbets enhet. Varje transaktion ger garantier för syra (atomiska, konsekvens, isolering, hållbarhet).

* **Atomisk** garanterar att alla åtgärder som utförs i en transaktion behandlas som en enda enhet, och att alla är antingen bekräftade eller inga av dem.
* **Konsekvens** säkerställer att data alltid är i ett giltigt tillstånd för transaktioner.
* **Isolering** garanterar att inga två transaktioner stör varandra – många kommersiella system ger flera isolerings nivåer som kan användas baserat på programmets behov.
* **Hållbarhet** garanterar att alla ändringar som är allokerade i en databas alltid finns.
Azure Cosmos DB stöder [fullständiga syror-kompatibla transaktioner med ögonblicks bild isolering](database-transactions-optimistic-concurrency.md) för åtgärder inom samma [logiska partitionsnyckel](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Transaktionella batch-åtgärder kontra lagrade procedurer

Azure Cosmos DB stöder för närvarande lagrade procedurer, som även tillhandahåller transaktions omfattningen för åtgärder. Transaktionella batch-åtgärder ger dock följande fördelar:

* **Språk alternativ** – transaktionell batch stöds på SDK och språk som du arbetar med redan, medan lagrade procedurer måste skrivas i Java Script.
* **Kod versions** hantering – versions program kod och att publicera den på din CI/CD-pipeline är mycket mer naturlig än att dirigera uppdateringen av en lagrad procedur och se till att överrullningen sker vid rätt tidpunkt. Det gör det också enklare att återställa ändringar.
* **Prestanda** – minskad latens för motsvarande åtgärder med upp till 30% jämfört med den lagrade procedur körningen.
* **Innehålls serialisering** – varje åtgärd i en transaktions grupp kan använda anpassade serialiserings alternativ för nytto lasten.

## <a name="how-to-create-a-transactional-batch-operation"></a>Så här skapar du en transaktionell batch-åtgärd

När du skapar en transaktionell batch-åtgärd börjar du med en behållar instans och anropar `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Sedan måste du anropa `ExecuteAsync` batchen:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

När svaret har tagits emot kontrollerar du om det lyckas eller inte och extraherar resultaten:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Om det uppstår ett fel kommer den misslyckade åtgärden att ha en status kod för sitt motsvarande fel. Alla andra åtgärder har en 424-status kod (misslyckat beroende). I exemplet nedan Miss lyckas åtgärden eftersom den försöker skapa ett objekt som redan finns (409 HttpStatusCode. konflikt). Status koden gör att det går att identifiera orsaken till transaktions felet.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Hur körs transaktions batch-åtgärder

När `ExecuteAsync` metoden anropas grupperas alla åtgärder i `TransactionalBatch` objektet, serialiseras till en enda nytto last och skickas som en enskild begäran till tjänsten Azure Cosmos dB.

Tjänsten tar emot begäran och kör alla åtgärder inom en transaktions omfattning och returnerar ett svar med samma serialiserings protokoll. Detta svar är antingen lyckat eller misslyckat, och tillhandahåller enskilda åtgärds svar per åtgärd.

SDK visar svaret för att verifiera resultatet och eventuellt extrahera var och en av de interna åtgärds resultaten.

## <a name="limitations"></a>Begränsningar

Det finns för närvarande två kända begränsningar:

* Storleks gränsen för Azure Cosmos DB-begäran begränsar `TransactionalBatch` nytto lastens storlek till inte överstiger 2 MB och den maximala körnings tiden är 5 sekunder.
* Det finns en aktuell gräns på 100 åtgärder per `TransactionalBatch` för att säkerställa att prestandan är som förväntat och inom service avtal.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [vad du kan göra med TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Besök våra [exempel](sql-api-dotnet-v3sdk-samples.md) för fler sätt att använda vår Cosmos dB .NET SDK
