---
title: Skapa instans av DMS (Azure Resource Manager mall)
description: Lär dig hur du skapar Database Migration Service med Azure Resource Manager-mall (ARM-mall).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963223"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Snabb start: skapa en instans av Azure Database Migration Service med ARM-mall

Använd den här Azure Resource Manager mallen (ARM-mallen) för att distribuera en instans av Azure Database Migration Service. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Azure Database Migration Service ARM-mallen kräver följande: 

- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) och/eller [PowerShell](/powershell/scripting/install/installing-powershell). 
- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

Tre Azure-resurser definieras i mallen: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): skapar det virtuella nätverket. 
- [Microsoft. Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): skapar under nätet. 
- [Microsoft. data migration/Services](/azure/templates/microsoft.datamigration/services): distribuerar en instans av Azure Database migration service. 

Fler Azure Database migration Services-mallar finns i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en instans av Azure Database Migration Service. 

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Välj eller ange följande värden.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj **Skapa ny** för att skapa en ny resurs grupp. 
    * **Region**: platsen där resurserna ska distribueras.
    * **Tjänst namn**: namnet på den nya migrerings tjänsten.
    * **Plats**: platsen för resurs gruppen, lämna den som standard `[resourceGroup().location]` .
    * **VNet-namn**: namnet på det nya virtuella nätverket.
    * **Under näts namn**: namnet på det nya undernät som är associerat med det virtuella nätverket.



3. Välj **Granska + skapa**. När instansen av Azure Database Migration Service har distribuerats får du ett meddelande. 


Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure CLI för att kontrol lera distribuerade resurser. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

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

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Andra sätt att distribuera Azure Database Migration Service finns i: 
- [Azure-portalen](quickstart-create-data-migration-service-portal.md)

Mer information finns i [Översikt över Azure Database migration service](dms-overview.md)