---
title: Etablera data flöde på Azure Cosmos DB API för MongoDB-resurser
description: 'Lär dig hur du etablerar data flöde för behållare, databas och autoskalning i Azure Cosmos DB API för MongoDB-resurser. Du kommer att använda Azure Portal, CLI, PowerShell och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086133"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Etablera databas, container eller autoskalning av data flöde på Azure Cosmos DB API för MongoDB-resurser
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här artikeln beskriver hur du etablerar data flöde i Azure Cosmos DB API för MongoDB. Du kan etablera standard (manuell) eller autoskalning genom strömning på en behållare, eller en databas och dela den mellan behållarna i databasen. Du kan etablera data flöde med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

Om du använder ett annat API, se [SQL API](how-to-provision-container-throughput.md), [API för Cassandra](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artiklar för att etablera data flödet.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna fönsterrutan **Data Explorer** och välj **Ny samling**. Ange därefter följande information:

   * Ange om du skapar en ny databas eller använder en befintlig. Välj **data flödes alternativet etablera databas** om du vill etablera data flöde på databas nivå.
   * Ange ett samlings-ID.
   * Ange ett partitionsnyckelvärde (till exempel `/ItemID`).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Skärm bild av Datautforskaren när du skapar en ny samling med data flöde på databas nivå":::

> [!Note]
> Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med Azure Cosmos DB-API för MongoDB, använder du `/myShardKey` för sökvägen till partitionsnyckel.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager mallar kan användas för att etablera data flöde för autoskalning på databas-eller container nivå resurser för alla Azure Cosmos DB-API: er. Se [Azure Resource Manager mallar för Azure Cosmos DB](templates-samples-mongodb.md) för exempel.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan användas för att etablera autoskalning av data flödet på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns i exempel [på Azure CLI-exempel för Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan användas för att etablera data flöde för autoskalning på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns [Azure PowerShell exempel för Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)