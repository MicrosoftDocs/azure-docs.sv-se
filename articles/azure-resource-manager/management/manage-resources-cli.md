---
title: Hantera resurser – Azure CLI
description: Använd Azure CLI och Azure Resource Manager för att hantera dina resurser. Visar hur du distribuerar och tar bort resurser.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 077ebdb4dd33249923064a4f5ed20c5641a82e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695896"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Hantera Azure-resurser med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](overview.md) för att hantera dina Azure-resurser. Information om hur du hanterar resurs grupper finns i [Hantera Azure-resurs grupper med hjälp av Azure CLI](manage-resource-groups-cli.md).

Andra artiklar om att hantera resurser:

- [Hantera Azure-resurser med hjälp av Azure Portal](manage-resources-portal.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resurs grupp

Du kan distribuera Azure-resurser direkt med hjälp av Azure CLI eller distribuera en Resource Manager-mall för att skapa Azure-resurser.

### <a name="deploy-a-resource"></a>Distribuera en resurs

Följande skript skapar ett lagrings konto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Distribuera en mall

Följande skript skapar distribution av en snabb starts mall för att skapa ett lagrings konto. Mer information finns i [snabb start: skapa arm-mallar med Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resurs grupp och resurser

Du kan skapa en resurs grupp och distribuera resurser till gruppen. Mer information finns i [skapa resurs grupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuera resurser till flera prenumerationer eller resurs grupper

Normalt distribuerar du alla resurser i mallen till en enda resurs grupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resurs grupper eller prenumerationer. Mer information finns i [Distribuera Azure-resurser till flera prenumerationer eller resurs grupper](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Ta bort resurser

Följande skript visar hur du tar bort ett lagrings konto.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](delete-resource-group.md).

## <a name="move-resources"></a>Flytta resurser

Följande skript visar hur du tar bort ett lagrings konto från en resurs grupp till en annan resurs grupp.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Lås resurser

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs. 

Följande skript låser ett lagrings konto så att kontot inte kan tas bort.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Följande skript hämtar alla Lås för ett lagrings konto:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Följande skript tar bort ett lås för ett lagrings konto:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggning hjälper till att organisera resurs gruppen och resurserna logiskt. Mer information finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) är hur du hanterar åtkomst till resurser i Azure. Mer information finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../templates/template-syntax.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](../index.yml).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).