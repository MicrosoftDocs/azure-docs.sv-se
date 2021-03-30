---
title: Skapa en behållare i Azure Cosmos DB Gremlin-API
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB Gremlin API genom att använda Azure Portal, .NET och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: f7e9de1f23ec46af08fe96b5db3170fac9a7eb2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101637"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Skapa en behållare i Azure Cosmos DB Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB Gremlin-API. Det visar hur du skapar en behållare med hjälp av Azure Portal, Azure CLI, PowerShell eller SDK: er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB Gremlin-API. Om du använder ett annat API, se [API för MongoDB](how-to-create-container-mongodb.md), [API för Cassandra](how-to-create-container-cassandra.md), [tabell-API](how-to-create-container-table.md)och [SQL API](how-to-create-container.md) -artiklar för att skapa behållaren.

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Skapa med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-graph-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett graf-ID.
   * Välj **obegränsad** lagringskapacitet.
   * Ange en partitionsnyckel för hörn.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Skärmbild av API för Gremlin, dialogrutan Lägg till diagram":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Skapa med .NET SDK

Om du råkar ut för tids gräns undantag när du skapar en samling, gör du en Läs åtgärd för att verifiera att samlingen har skapats. Läsnings åtgärden genererar ett undantag tills åtgärden Skapa samling har slutförts. En lista över status koder som stöds av Create-åtgärden finns i [HTTP-statuskod för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikeln.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Skapa med Azure CLI

[Skapa en Gremlin-graf med Azure CLI](./scripts/cli/gremlin/create.md). För en lista över alla Azure CLI-exempel i alla Azure Cosmos DB API: er, se [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Skapa med PowerShell

[Skapa ett Gremlin-diagram med PowerShell](./scripts/powershell/gremlin/create.md). En lista över alla PowerShell-exempel i alla Azure Cosmos DB API: er finns i [PowerShell-exempel](powershell-samples.md)

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](./account-databases-containers-items.md)