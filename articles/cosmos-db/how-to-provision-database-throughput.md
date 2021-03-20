---
title: Etablera databas data flöde i Azure Cosmos DB SQL API
description: 'Lär dig hur du etablerar data flöde på databas nivå i Azure Cosmos DB SQL API med Azure Portal, CLI, PowerShell och andra SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342010"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Etablera standard (manuell) genom strömning på en databas i Azure Cosmos DB-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du etablerar standard-genomflödet (manuellt) i en databas i Azure Cosmos DB SQL API. Du kan etablera data flöde för en enskild [behållare](how-to-provision-container-throughput.md), eller för en databas och dela data flödet bland behållare i den. Information om hur du använder data flödes nivå och databas nivå genom strömning finns i avsnittet [användnings fall för etablering av data flöde på behållare och databaser](set-throughput.md) . Du kan etablera dataflöde på databasnivå med hjälp av Azure-portalen eller Azure Cosmos DB-SDK:er.

Om du använder ett annat API, se [API för MongoDB](how-to-provision-throughput-mongodb.md), [API för Cassandra](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artiklar för att etablera data flödet.

## <a name="provision-throughput-using-azure-portal"></a>Etablera dataflöde med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Data Explorer** och välj **Ny databas**. Ange följande information:

   * Ange ett databas-ID.
   * Välj **data flödes alternativet etablera databas** .
   * Ange ett dataflöde (till exempel 1000 RU:er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Skärmbild av dialogrutan Ny databas":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Etablera data flöde med hjälp av Azure CLI eller PowerShell

Information om hur du skapar en databas med delat data flöde finns i

* [Skapa en databas med Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Skapa en databas med PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Etablera dataflöde med hjälp av .NET SDK

> [!Note]
> Du kan använda Azure Cosmos SDK: er för SQL API för att etablera data flöde för alla API: er. Om du vill kan du även använda följande exempel för API för Cassandra.

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

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns information om etablerade data flöden i Azure Cosmos DB:

* [Skala etablerat dataflöde globalt](./request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Så här etablerar du standard (manuell) data flöde för en behållare](how-to-provision-container-throughput.md)
* [Så här etablerar du data flöde för autoskalning för en behållare](how-to-provision-autoscale-throughput.md)
* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)