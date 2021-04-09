---
title: Parametriserade frågor i Azure Cosmos DB
description: Lär dig hur SQL-parametriserade frågor ger robust hantering och avvisning av indata från användaren, och förhindra oavsiktlig exponering av data via SQL-inmatning.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549169"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametriserade frågor i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB stöder frågor med parametrar som uttrycks av den välkända @-notationen. Parametriserade SQL ger robust hantering och avvisning av indata från användaren, och förhindrar oavsiktlig exponering av data via SQL-inmatning.

## <a name="examples"></a>Exempel

Du kan till exempel skriva en fråga som använder `lastName` och `address.state` som parametrar och köra den för olika värden för `lastName` och `address.state` baserat på användarindata.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Du kan sedan skicka denna begäran till Azure Cosmos DB som en parameter-JSON-fråga som följande:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

I följande exempel anges det översta argumentet med en parametriserad fråga:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameter värden kan vara valfri giltig JSON: strängar, siffror, booleska värden, null, jämna matriser eller kapslad JSON. Eftersom Azure Cosmos DB är schema löst val IDE ras inte parametrar mot någon typ.

Här följer några exempel på parametriserade frågor i varje Azure Cosmos DB SDK:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
