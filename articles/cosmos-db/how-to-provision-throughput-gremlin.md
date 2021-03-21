---
title: Etablera data flöde på Azure Cosmos DB Gremlin API-resurser
description: 'Lär dig hur du etablerar data flöde för behållare, databas och autoskalning i Azure Cosmos DB Gremlin API-resurser. Du kommer att använda Azure Portal, CLI, PowerShell och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086167"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Etablera databas, container eller autoskalning av data flöde på Azure Cosmos DB Gremlin API-resurser
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här artikeln beskriver hur du etablerar data flöde i Azure Cosmos DB Gremlin-API. Du kan etablera standard (manuell) eller autoskalning genom strömning på en behållare, eller en databas och dela den mellan behållarna i databasen. Du kan etablera data flöde med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

Om du använder ett annat API, se [SQL API](how-to-provision-container-throughput.md), [API för Cassandra](how-to-provision-throughput-cassandra.md), [API för MongoDB](how-to-provision-throughput-mongodb.md) -artiklar för att etablera data flödet.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Datautforskaren** och välj **Ny graf**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig. Välj **data flödes alternativet etablera databas** om du vill etablera data flöde på databas nivå.
   * Ange ett graf-ID.
   * Ange ett partitionsnyckelvärde (till exempel `/ItemID`).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Skärm bild av Datautforskaren när du skapar en ny graf med data flöde på databas nivå":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Använd Cosmos SDK: er för SQL API för att etablera data flöde för alla Azure Cosmos DB-API: er, förutom Cassandra och MongoDB-API.

### <a name="provision-container-level-throughput"></a>Etablera data flöde för container nivå

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

### <a name="provision-database-level-throughput"></a>Etablera data flöde på databas nivå

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager mallar kan användas för att etablera data flöde för autoskalning på databas-eller container nivå resurser för alla Azure Cosmos DB-API: er. Se [Azure Resource Manager mallar för Azure Cosmos DB](templates-samples-gremlin.md) för exempel.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan användas för att etablera autoskalning av data flödet på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns i exempel [på Azure CLI-exempel för Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan användas för att etablera data flöde för autoskalning på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns [Azure PowerShell exempel för Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)