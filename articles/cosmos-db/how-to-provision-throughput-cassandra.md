---
title: Etablera data flöde på Azure Cosmos DB API för Cassandra resurser
description: 'Lär dig hur du etablerar data flöde för behållare, databas och autoskalning i Azure Cosmos DB API för Cassandra resurser. Du kommer att använda Azure Portal, CLI, PowerShell och olika SDK: er.'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086286"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Etablera databas, behållare eller autoskalning av data flöde på Azure Cosmos DB API för Cassandra resurser
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

I den här artikeln beskrivs hur du etablerar data flöde i Azure Cosmos DB API för Cassandra. Du kan etablera standard (manuell) eller autoskalning genom strömning på en behållare, eller en databas och dela den mellan behållarna i databasen. Du kan etablera data flöde med hjälp av Azure Portal, Azure CLI eller Azure Cosmos DB SDK: er.

Om du använder ett annat API, se [SQL API](how-to-provision-container-throughput.md), [API för MONGODB](how-to-provision-throughput-mongodb.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artiklar för att etablera data flödet.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-mongodb-dotnet.md#create-a-database-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Öppna rutan **Datautforskaren** och välj **Ny tabell**. Ange därefter följande information:

   * Ange om du skapar ett nytt disk utrymme eller använder ett befintligt. Välj **data flödes alternativet etablera databas** om du vill etablera data flöde på den här nivån.
   * Ange tabell-ID i CQL-kommandot.
   * Ange ett primär nyckel värde (till exempel `/userrID` ).
   * Ange ett data flöde som du vill etablera (till exempel 1000 ru: er).
   * Välj **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Skärm bild av Datautforskaren när du skapar en ny samling med data flöde på databas nivå":::

> [!Note]
> Om du konfigurerar data flödet på en behållare i ett Azure Cosmos-konto som kon figurer ATS med API för Cassandra använder du `/myPrimaryKey` för sökvägen till partitionsnyckel.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Etablera data flöde för en Cassandra-tabell

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Liknande kommandon kan utfärdas via alla CQL-kompatibla driv rutiner.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Ändra eller ändra data flöde för en Cassandra-tabell

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Liknande kommando kan köras via valfri CQL-kompatibel driv rutin.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager mallar kan användas för att etablera data flöde för autoskalning på databas-eller container nivå resurser för alla Azure Cosmos DB-API: er. Se [Azure Resource Manager mallar för Azure Cosmos DB](templates-samples-cassandra.md) för exempel.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan användas för att etablera autoskalning av data flödet på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns i exempel [på Azure CLI-exempel för Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan användas för att etablera data flöde för autoskalning på en databas eller behållares nivå resurser för alla Azure Cosmos DB-API: er. Exempel finns [Azure PowerShell exempel för Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig hur du etablerar dataflöde i Azure Cosmos DB:

* [Begärandeenheter och dataflöde i Azure Cosmos DB](request-units.md)