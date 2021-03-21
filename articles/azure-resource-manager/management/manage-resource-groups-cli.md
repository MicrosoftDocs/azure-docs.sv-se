---
title: Hantera resurs grupper – Azure CLI
description: Använd Azure CLI för att hantera dina resurs grupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resurs grupper.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172104"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Hantera Azure Resource Manager resurs grupper med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](overview.md) för att hantera Azures resurs grupper. Information om hur du hanterar Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure CLI](manage-resources-cli.md).

Andra artiklar om att hantera resurs grupper:

- [Hantera Azure-resurs grupper med hjälp av Azure Portal](manage-resources-portal.md)
- [Hantera Azure-resurs grupper med hjälp av Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Vad är en resurs grupp?

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg vanligt vis till resurser som delar samma livs cykel i samma resurs grupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att dina data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resurs gruppen anger du var metadata lagras.

## <a name="create-resource-groups"></a>Skapa resurs grupper

Följande CLI-kommando skapar en resurs grupp.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Lista resurs grupper

Följande CLI-skript listar resurs grupperna under din prenumeration.

```azurecli-interactive
az group list
```

Så här hämtar du en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

Följande CLI-skript tar bort en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resurs grupp

Se [distribuera resurser till en befintlig resurs grupp](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resurs grupp och resurser

Du kan skapa en resurs grupp och distribuera resurser till gruppen med hjälp av en Resource Manager-mall. Mer information finns i [skapa resurs grupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen Miss lyckas

Den här funktionen kallas även *återgång vid fel*. Mer information finns i [omdistribuera när distributionen Miss lyckas](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resurs grupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resurs grupp. Mer information finns i [Flytta resurser](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Lås resurs grupper

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs.

Följande skript låser en resurs grupp så att det inte går att ta bort resurs gruppen.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

Följande skript hämtar alla Lås för en resurs grupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

Följande skript tar bort ett lås:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resurs grupper

Du kan använda taggar för resurs grupper och resurser för att logiskt organisera dina till gångar. Mer information finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportera resurs grupper till mallar

När du har konfigurerat resurs gruppen kanske du vill visa Resource Manager-mallen för resurs gruppen. Att exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller all fullständig infrastruktur.
- Lär dig mer om mallens syntax genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

Om du vill exportera alla resurser i en resurs grupp använder du [AZ Group export](/cli/azure/group#az_group_export) och anger resurs gruppens namn.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

Skriptet visar mallen i-konsolen. Kopiera JSON och Spara som en fil.

I stället för att exportera alla resurser i resurs gruppen kan du välja vilka resurser som ska exporteras.

Överför resurs-ID för att exportera en resurs.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

Om du vill exportera fler än en resurs skickar du de blankstegsavgränsad resurs-ID: na. Om du vill exportera alla resurser ska du inte ange det här argumentet eller ange "*".

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

När du exporterar mallen kan du ange om parametrar ska användas i mallen. Som standard ingår parametrar för resurs namn, men de har inget standardvärde. Du måste skicka parameter värde under distributionen.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

I resursen används parametern för namnet.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Om du använder `--include-parameter-default-value` -parametern när du exporterar mallen, innehåller parametern Template ett standardvärde som är inställt på det aktuella värdet. Du kan antingen använda standardvärdet eller skriva över standardvärdet genom att skicka ett annat värde.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Om du använder `--skip-resource-name-params` -parametern när du exporterar mallen ingår inte parametrar för resurs namn i mallen. I stället anges resurs namnet direkt på resursen till det aktuella värdet. Du kan inte anpassa namnet under distributionen.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Funktionen Exportera mall stöder inte export av Azure Data Factory-resurser. Information om hur du kan exportera Data Factory-resurser finns i [Kopiera eller klona en data fabrik i Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Om du vill exportera resurser som skapats via den klassiska distributions modellen måste du [migrera dem till distributions modellen för Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Mer information finns i avsnittet [Exportera en och flera resurser till mallen i Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resurs grupper

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) är hur du hanterar åtkomst till resurser i Azure. Mer information finns i [lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../templates/template-syntax.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](../index.yml).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).