---
title: Sid brytning i Azure Cosmos DB
description: Lär dig mer om växlings begrepp och fortsättnings-token
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: d47a7b9e2229ffbd747b1ff7b9491ce99e9b190f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492242"
---
# <a name="pagination-in-azure-cosmos-db"></a>Sid brytning i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I Azure Cosmos DB kan frågor ha flera sidor med resultat. Det här dokumentet beskriver de villkor som Azure Cosmos DBs frågemotor använder för att bestämma om frågeresultaten ska delas upp på flera sidor. Du kan välja att använda fortsättnings-token för att hantera frågeresultat som sträcker sig över flera sidor.

## <a name="understanding-query-executions"></a>Förstå frågekörningen

Ibland delas frågeresultatet över flera sidor. Varje sidas resultat genereras av en separat frågekörningen. Om frågeresultatet inte kan returneras i en enda körning, delas Azure Cosmos DB automatiskt in resultat på flera sidor.

Du kan ange det maximala antalet objekt som returneras av en fråga genom att ange `MaxItemCount` . `MaxItemCount`Anges per begäran och talar om för frågemotor att returnera antalet objekt eller färre. Du kan ställa in `MaxItemCount` på `-1` om du inte vill placera en gräns för antalet resultat per frågekörningen.

Dessutom finns det andra orsaker till att frågemotor kan behöva dela frågeresultaten på flera sidor. Dessa omfattar:

- Behållaren har begränsats och det inte fanns några tillgängliga ru: er för att returnera fler frågeresultat
- Svar för frågekörningen var för stort
- Frågans körnings tid var för lång
- Det var mer effektivt för frågemotor att returnera resultat i ytterligare körningar

Antalet objekt som returneras per frågekörning är alltid mindre än eller lika med `MaxItemCount` . Det är dock möjligt att andra kriterier kan ha begränsade antalet resultat som frågan kan returnera. Om du kör samma fråga flera gånger är det inte säkert att antalet sidor är konstant. Om en fråga till exempel har begränsats kan det finnas färre tillgängliga resultat per sida, vilket innebär att frågan har ytterligare sidor. I vissa fall är det också möjligt att frågan kan returnera en tom sida med resultat.

## <a name="handling-multiple-pages-of-results"></a>Hantera flera resultat sidor

För att säkerställa korrekta frågeresultat bör du fortsätta med alla sidor. Du bör fortsätta att köra frågor tills det inte finns några ytterligare sidor.

Här följer några exempel på hur du bearbetar resultat från frågor med flera sidor:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Fortsättnings-token

I .NET SDK och Java SDK kan du välja att använda fortsättnings-token som ett bok märke för frågans förlopp. Azure Cosmos DB frågekörning är tillstånds lösa på Server sidan och kan återupptas när som helst med hjälp av en fortsättnings-token. Fortsättnings-token stöds inte i Node.js SDK. För python SDK stöds det för Single partition-frågor och PK måste anges i Options-objektet eftersom det inte är tillräckligt för att ha det i själva frågan.

Här följer några exempel på hur du använder fortsättnings-token:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Om frågan returnerar en fortsättnings-token finns det ytterligare frågeresultat.

I Azure Cosmos DB REST API kan du hantera fortsättnings-token med `x-ms-continuation` sidhuvudet. Som vid frågor med .NET eller Java SDK, om `x-ms-continuation` svars huvudet inte är tomt, innebär det att frågan har ytterligare resultat.

Så länge du använder samma SDK-version upphör aldrig fortsättnings-token. Du kan också [begränsa storleken på en fortsättnings-token](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Oavsett mängden data eller antalet fysiska partitioner i din behållare returnerar frågor en enda fortsättnings-token.

Du kan inte använda tilläggs-token för frågor med [Group by](sql-query-group-by.md) eller [DISTINCT](sql-query-keywords.md#distinct) eftersom dessa frågor kräver lagring av en stor mängd tillstånd. För frågor med `DISTINCT` kan du använda tilläggs-token om du lägger till `ORDER BY` frågan.

Här är ett exempel på en fråga med `DISTINCT` som kan använda en fortsättnings-token:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY-sats](sql-query-order-by.md)
