---
title: Skapa en behållare i Azure Cosmos DB SQL API
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB SQL API genom att använda Azure Portal, .NET, Java, python, Node.js och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006856"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Skapa en behållare i Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB SQL API. Det visar hur du skapar en behållare med hjälp av SDK: er för Azure Portal, Azure CLI, PowerShell eller som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB SQL API. Om du använder ett annat API, se [API för MongoDB](how-to-create-container-mongodb.md), [API för Cassandra](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)och [tabell-API](how-to-create-container-table.md) artiklar för att skapa behållaren.

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Skapa en container med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt konto.

1. Öppna fönstret **datautforskaren** och välj **ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID.
   * Ange en partitionsnyckel.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Skärm bild av Datautforskaren fönstret med en ny behållare markerad":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Skapa en container med hjälp av Azure CLI

[Skapa en behållare med Azure CLI](manage-with-cli.md#create-a-container). För en lista över alla Azure CLI-exempel i alla Azure Cosmos DB API: er, se [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Skapa en behållare med hjälp av PowerShell

[Skapa en behållare med PowerShell](manage-with-powershell.md#create-container). En lista över alla PowerShell-exempel i alla Azure Cosmos DB API: er finns i [PowerShell-exempel](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Skapa en container med hjälp av .NET SDK

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

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](./account-databases-containers-items.md)