---
title: Etablera data flöde för behållare i Azure Cosmos DB SQL API
description: 'Lär dig hur du etablerar data flöde på behållar nivån i Azure Cosmos DB SQL API med Azure Portal, CLI, PowerShell och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100107"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Etablera standard (manuell) data flöde i en Azure Cosmos-behållare – SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du etablerar standard-genomflödet (manuellt) i en behållare i Azure Cosmos DB SQL API. Du kan etablera data flöde på en enskild behållare eller [etablera data flöde på en databas](how-to-provision-database-throughput.md) och dela den mellan behållarna i databasen. Du kan etablera data flöde på en behållare med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

Om du använder ett annat API, se [API för MongoDB](how-to-provision-throughput-mongodb.md), [API för Cassandra](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artiklar för att etablera data flödet.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna fönstret **datautforskaren** och välj **ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID.
   * Ange ett partitionsnyckelvärde (till exempel `/ItemID`).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Skärmbild av Data Explorer med Ny samling markerat":::

## <a name="azure-cli-or-powershell"></a>Azure CLI eller PowerShell

Om du vill skapa en behållare med dedikerat data flöde ser du

* [Skapa en container med hjälp av Azure CLI](manage-with-cli.md#create-a-container)
* [Skapa en behållare med hjälp av PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Använd Cosmos SDK: er för SQL API för att etablera data flöde för alla Cosmos DB-API: er, förutom Cassandra och MongoDB-API.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Så här etablerar du standard data flöde (manuell) i en databas](how-to-provision-database-throughput.md)
* [Så här etablerar du data flöde för autoskalning på en databas](how-to-provision-autoscale-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)
