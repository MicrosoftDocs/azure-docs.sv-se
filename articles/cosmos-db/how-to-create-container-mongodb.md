---
title: Skapa en behållare i Azure Cosmos DB API för MongoDB
description: 'Lär dig hur du skapar en behållare i Azure Cosmos DB API för MongoDB med hjälp av Azure Portal, .NET, Java, Node.js och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101603"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Skapa en behållare i Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB API för MongoDB. Det visar hur du skapar en behållare med hjälp av Azure Portal, Azure CLI, PowerShell eller SDK: er som stöds. Den här artikeln visar hur du skapar en container, anger partitionsnyckeln och etablerar dataflöde.

I den här artikeln beskrivs olika sätt att skapa en behållare i Azure Cosmos DB API för MongoDB. Om du använder ett annat API, se [SQL API](how-to-create-container.md), [API för Cassandra](how-to-create-container-cassandra.md), [Gremlin-API](how-to-create-container-gremlin.md)och [tabell-API](how-to-create-container-table.md) artiklar för att skapa behållaren.

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Skapa med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller Välj ett befintligt konto.

1. Öppna fönstret **datautforskaren** och välj **ny behållare**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig.
   * Ange ett container-ID.
   * Ange en shardnyckel.
   * Ange ett data flöde som ska tillhandahållas (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Skärm bild av dialog rutan Azure Cosmos DB-API för MongoDB, Lägg till behållare":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Skapa med .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB Wire Protocol förstår inte konceptet för enheter för [programbegäran](request-units.md). Om du vill skapa en ny samling med ett tillhandahållet data flöde på den använder du Azure Portal eller Cosmos DB SDK: er för SQL API.

Om du råkar ut för tids gräns undantag när du skapar en samling, gör du en Läs åtgärd för att verifiera att samlingen har skapats. Läsnings åtgärden genererar ett undantag tills åtgärden Skapa samling har slutförts. En lista över status koder som stöds av Create-åtgärden finns i [HTTP-statuskod för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikeln.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Skapa med Azure CLI

[Skapa en samling för Azure Cosmos dB för MongoDB-API med Azure CLI](./scripts/cli/mongodb/create.md). För en lista över alla Azure CLI-exempel i alla Azure Cosmos DB API: er, se [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Skapa med PowerShell

[Skapa en samling för Azure Cosmos dB för MongoDB-API med PowerShell](./scripts/powershell/mongodb/create.md). En lista över alla PowerShell-exempel i alla Azure Cosmos DB API: er finns i [PowerShell-exempel](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Skapa en behållare med Azure Resource Manager mallar

[Skapa en samling för Azure Cosmos dB för MongoDB-API med Resource Manager-mall](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med ett Azure Cosmos-konto](./account-databases-containers-items.md)