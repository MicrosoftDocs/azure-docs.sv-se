---
title: Skapa en dedikerad SQL-pool (tidigare SQL DW) med hjälp Azure Resource Manager en mall
description: Lär dig hur du skapar en Azure Synapse Analytics SQL-pool med hjälp Azure Resource Manager mall.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.author: jrasnick
ms.date: 06/09/2020
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 73f4baf1e48b5495d513fbabe66763538c8cca57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568479"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Snabbstart: Skapa en Azure Synapse Analytics dedikerad SQL-pool (tidigare SQL DW) med hjälp av en ARM-mall

Den Azure Resource Manager mallen (ARM-mall) skapar en dedikerad SQL-pool (tidigare SQL DW) med transparent datakryptering aktiverat. Dedikerad SQL-pool (tidigare SQL DW) refererar till informationslagerfunktioner för företag som är allmänt tillgängliga i Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Mallen definierar en resurs:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen. Den här mallen skapar en dedikerad SQL-pool (tidigare SQL DW).
   
   [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Ange eller uppdatera följande värden:

   * **Prenumeration:** Välj en Azure-prenumeration.
   * **Resursgrupp:** Välj **Skapa ny** och ange ett unikt namn för resursgruppen och välj **OK.** En ny resursgrupp underlättar rensning av resurser.
   * **Region:** Välj en region.  Välj till exempel **USA, centrala**.
   * **SQL Server namn:** Godkänn standardnamnet eller ange ett namn SQL Server namnet.
   * **SQL-administratörsinloggning:** Ange administratörens användarnamn för SQL Server.
   * **SQL-administratörslösenord:** Ange administratörslösenordet för SQL Server.
   * **Data Warehouse namn:** Ange ett dedikerat SQL-poolnamn.
   * **transparent datakryptering:** Acceptera standardinställningen aktiverad. 
   * **Servicenivåmål:** Acceptera standardvärdet DW400c.
   * **Plats:** Acceptera standardplatsen för resursgruppen.
   * **Granska och skapa:** Välj.
   * **Skapa:** Välj.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrollera de distribuerade resurserna eller använda Azure CLI Azure PowerShell för att visa en lista över de distribuerade resurserna.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du ta bort den med hjälp av Azure CLI eller Azure PowerShell:

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

I den här snabbstarten skapade du en dedikerad SQL-pool (tidigare SQL DW) med hjälp av en ARM-mall och verifierade distributionen. Mer information om Azure Synapse Analytics och Azure Resource Manager finns i artiklarna nedan.

- Läs en [översikt över Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Skapa och distribuera din första ARM-mall](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
