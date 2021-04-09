---
title: Skapa Azure Cosmos-behållare med stor partitionsnyckel
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB med stor partitionsnyckel med Azure Portal och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339313"
---
# <a name="create-containers-with-large-partition-key"></a>Skapa behållare med stor partitionsnyckel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB använder hash-baserad partitionerings schema för att uppnå horisontell skalning av data. Alla Azure Cosmos-behållare som skapats före maj 3 2019 använder en hash-funktion som beräknar hash baserat på den första 100 byte i partitionsnyckel. Om det finns flera partitionstyper som har samma första 100 byte betraktas dessa logiska partitioner som samma logiska partition av tjänsten. Detta kan leda till problem som att kvoten för partitionsstorleken är felaktig och att unika index tillämpas över partitionernas nycklar. Stora partitionsalternativ har introducerats för att lösa det här problemet. Azure Cosmos DB stöder nu stora partitionsnyckel med värden upp till 2 KB.

Stora partitionsuppsättningar stöds med hjälp av funktionerna i en förbättrad version av hash-funktionen, som kan generera en unik hash från stora partitionsnyckel upp till 2 KB. Den här hash-versionen rekommenderas också för scenarier med hög partitionsnyckel, oberoende av storleken på partitionsnyckel. En kardinalitet för partitionsnyckel definieras som antalet unika logiska partitioner, till exempel i ordningen ~ 30000 logiska partitioner i en behållare. Den här artikeln beskriver hur du skapar en behållare med en stor partitionsnyckel med hjälp av Azure Portal och olika SDK: er.

## <a name="create-a-large-partition-key-azure-portal"></a>Skapa en stor partitionsnyckel (Azure Portal)

Om du vill skapa en stor partitionsnyckel när du skapar en ny behållare med hjälp av Azure Portal, kontrollerar du att **nyckeln My partition är större än 100-byte-** alternativet. Avmarkera kryss rutan om du inte behöver stora partitionerings nycklar eller om du har program som körs på SDK-versioner som är äldre än 1,18.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Skapa stora partitionsalternativ med Azure Portal":::

## <a name="create-a-large-partition-key-powershell"></a>Skapa en stor partitionsnyckel (PowerShell)

Om du vill skapa en behållare med stöd för stor partitionsnyckel, se,

* [Skapa en Azure Cosmos-behållare med en stor nyckel storlek](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Skapa en stor partitionsnyckel (.NET SDK)

Om du vill skapa en behållare med en stor partitionsnyckel med hjälp av .NET SDK anger du `PartitionKeyDefinitionVersion.V2` egenskapen. I följande exempel visas hur du anger egenskapen version i PartitionKeyDefinition-objektet och anger den till PartitionKeyDefinitionVersion. v2.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>SDK-versioner som stöds

De stora partitionernas nycklar stöds med följande minst SDK-versioner:

|SDK-typ  | Lägsta version   |
|---------|---------|
|.Net     |    1,18     |
|Java-synkronisering     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST-API | högre version än `2017-05-03` genom att använda `x-ms-version` begär ande huvudet.|
| Resource Manager-mall | version 2 med hjälp av `"version":2` egenskapen i `partitionKey` objektet. |

För närvarande kan du inte använda behållare med stor partitionsnyckel i i Power BI och Azure Logic Apps. Du kan använda behållare utan en stor partitionsnyckel från dessa program.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](./account-databases-containers-items.md)