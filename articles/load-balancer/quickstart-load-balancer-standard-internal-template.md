---
title: 'Snabbstart: Skapa en intern lastbalanserare med hjälp av en mall'
description: Den här snabbstarten visar hur du skapar en intern Azure-lastbalanserare med hjälp av en Azure Resource Manager mall (ARM-mall).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 00126dde55ffe584be611ddf268bb759e127d7a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788753"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Snabbstart: Skapa en intern lastbalanserare som lastbalanserar virtuella datorer med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du använder en Azure Resource Manager (ARM-mall) för att skapa en intern Azure-lastbalanserare.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallarna.](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Flera Azure-resurser har definierats i mallen:

- [**Microsoft.Storage/storageAccounts: Lagringskonton**](/azure/templates/microsoft.storage/storageaccounts)för virtuella datorer för startdiagnostik.
- [**Microsoft.Compute/availabilitySets:**](/azure/templates/microsoft.compute/availabilitySets)Tillgänglighetsuppsättning för virtuella datorer.
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/microsoft.network/virtualNetworks)Virtuellt nätverk för lastbalanserare och virtuella datorer.
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkInterfaces)Nätverksgränssnitt för virtuella datorer.
- [**Microsoft.Network/loadBalancers:**](/azure/templates/microsoft.network/loadBalancers)Intern lastbalanserare.
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualMachines)Virtuella datorer.

Om du vill hitta fler mallar som är relaterade Azure Load Balancer finns i [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i det vänstra fönstret.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standardnamnet för resursgruppen är **myResourceGroupLB**

1. Kontrollera att följande resurser har skapats i resursgruppen:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Användaren Azure Portal att verifiera skapandet av resurser." border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser som finns i den.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Nästa steg

En stegvis självstudiekurs som vägleder dig genom processen med att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
