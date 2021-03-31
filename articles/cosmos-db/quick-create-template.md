---
title: Snabb start – skapa en Azure Cosmos DB och en behållare med Azure Resource Manager mall
description: Snabb start visar hur du använder en Azure Cosmos-databas och en behållare med hjälp av Azure Resource Manager mall
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339673"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Snabb start: skapa en Azure Cosmos DB och en behållare med en ARM-mall
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB är Microsofts snabba NoSQL-databas med öppna API: er för alla skalor. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-databaser, dokument databaser och Graf-databaser. Den här snabb starten fokuserar på processen att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa en Azure Cosmos-databas och en behållare i databasen. Du kan senare lagra data i den här behållaren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration eller ett kostnads fritt Azure Cosmos DB utvärderings konto

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Tre Azure-resurser definieras i mallen:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): skapa ett Azure Cosmos-konto.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): skapa en Azure Cosmos-databas.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): skapa en Azure Cosmos-behållare.

Du hittar fler Azure Cosmos DB mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Cosmos-konto, en databas och en behållare.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Välj eller ange följande värden.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="ARM-mall, Azure Cosmos DB integrering, distribuera Portal":::

    Om den inte anges använder du standardvärdena för att skapa Azure Cosmos-resurser.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Konto namn**: Ange ett namn för Azure Cosmos-kontot. Det måste vara globalt unikt.
    * **Plats**: Ange en plats där du vill skapa ditt Azure Cosmos-konto. Azure Cosmos-kontot kan finnas på samma plats som resurs gruppen.
    * **Primär region**: det primära replik området för Azure Cosmos-kontot.
    * **Sekundär region**: det sekundära replik området för Azure Cosmos-kontot.
    * **Standard konsekvens nivå**: standard konsekvens nivån för Azure Cosmos-kontot.
    * **Högsta föråldrade prefix**: högsta antal inaktuella begär Anden. Krävs för BoundedStaleness.
    * **Max intervall i sekunder**: maximal fördröjning. Krävs för BoundedStaleness.
    * **Databas namn**: namnet på Azure Cosmos-databasen.
    * **Behållar namn**: namnet på Azure Cosmos-behållaren.
    * **Data flöde**: data flödet för behållaren, minsta data flöde svärdet är 400 ru/s.
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När du har distribuerat Azure Cosmos-kontot får du ett meddelande:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="ARM-mall, Cosmos DB integrering, distribuera Portal meddelande":::

Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan antingen använda Azure Portal för att kontrol lera Azure Cosmos-kontot,-databasen och-behållaren eller använda följande Azure CLI-eller Azure PowerShell-skript för att visa en lista över hemligheten som skapats.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande snabb starter och självstudier, kanske du vill lämna dessa resurser på plats.
När de inte längre behövs tar du bort resurs gruppen, som tar bort Azure Cosmos-kontot och de relaterade resurserna. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Azure Cosmos-konto, en databas och en behållare med hjälp av en ARM-mall och validerade distributionen. Om du vill veta mer om Azure Cosmos DB och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [Översikt över Azure Cosmos DB](introduction.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Hämta andra [Azure Cosmos DB Resource Manager-mallar](./templates-samples-sql.md)