---
title: Resource Manager-mallar för Azure Cosmos DB API för Cassandra
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB API för Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: eb746e8105717883b029586db2bfce8e31290e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340676"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB API för Cassandra resurser med Azure Resource Manager mallar
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

I den här artikeln får du lära dig hur du använder Azure Resource Manager mallar för att distribuera och hantera dina Azure Cosmos DB-konton,-tecken och-tabeller.

Den här artikeln innehåller exempel på API för Cassandra-konton, för att hitta exempel på andra typer av API-typer finns i: använda Azure Resource Manager mallar med Azure Cosmos DBs API för [SQL](templates-samples-sql.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md), [tabell](templates-samples-table.md) artiklar.

> [!IMPORTANT]
>
> * Konto namn är begränsade till 44 tecken, alla gemener.
> * Distribuera om mallen med uppdaterade RU/s om du vill ändra data flödes värden.
> * När du lägger till eller tar bort platser i ett Azure Cosmos-konto kan du ändra andra egenskaper samtidigt. Dessa åtgärder måste utföras separat.

Om du vill skapa någon av Azure Cosmos DB resurserna nedan kopierar du följande exempel-mall till en ny JSON-fil. Du kan också skapa en parameter-JSON-fil som ska användas när du distribuerar flera instanser av samma resurs med olika namn och värden. Det finns många sätt att distribuera Azure Resource Manager mallar, inklusive [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Azure Cosmos-konto för Cassandra med autoskalning av allokerat data flöde

Den här mallen skapar ett Azure Cosmos-konto i två regioner med alternativ för konsekvens och redundans, med ett nyckel utrymme och en tabell som kon figurer ATS för automatisk skalning av data flödet. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Azure Cosmos-konto för Cassandra med standard-allokerat data flöde

Den här mallen skapar ett Azure Cosmos-konto i två regioner med alternativ för konsekvens och redundans, med ett tecken utrymme och en tabell som är konfigurerad för standard data flöde. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

* [Dokumentation om Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)