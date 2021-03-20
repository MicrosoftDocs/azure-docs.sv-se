---
title: Resource Manager-mallar för Azure Cosmos DB API för MongoDB
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB-API för MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7664d48bad153b34e7557e9faaf4c8aa0d4215ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340639"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Hantera Azure Cosmos DB MongoDB-API-resurser med Azure Resource Manager-mallar
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I den här artikeln får du lära dig hur du använder Azure Resource Manager mallar för att distribuera och hantera dina Azure Cosmos DB-konton för MongoDB-API, databaser och samlingar.

Den här artikeln innehåller exempel på Azure Cosmos DB s API för MongoDB, för att hitta exempel på andra typer av API-typer finns i: använda Azure Resource Manager mallar med Azure Cosmos DB s API för [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [SQL](templates-samples-sql.md), [tabell](templates-samples-table.md) artiklar.

> [!IMPORTANT]
>
> * Konto namn är begränsade till 44 tecken, alla gemener.
> * Distribuera om mallen med uppdaterade RU/s om du vill ändra data flödes värden.
> * När du lägger till eller tar bort platser i ett Azure Cosmos-konto kan du ändra andra egenskaper samtidigt. Dessa åtgärder måste utföras separat.

Om du vill skapa någon av Azure Cosmos DB resurserna nedan kopierar du följande exempel-mall till en ny JSON-fil. Du kan också skapa en parameter-JSON-fil som ska användas när du distribuerar flera instanser av samma resurs med olika namn och värden. Det finns många sätt att distribuera Azure Resource Manager mallar, inklusive [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Azure Cosmos-konto för MongoDB med autoskalning av allokerat data flöde

Den här mallen skapar ett Azure Cosmos-konto för MongoDB API (3,2 eller 3,6) med två samlingar som delar automatiskt skalnings data flöde på databas nivå. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Azure Cosmos-konto för MongoDB med standard-allokerat data flöde

Den här mallen skapar ett Azure Cosmos-konto för MongoDB API (3,2 eller 3,6) med två samlingar som delar 400 RU/s standard (manuellt) data flöde på databas nivå. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

* [Dokumentation om Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)