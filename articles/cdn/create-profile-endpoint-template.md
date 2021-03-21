---
title: 'Snabb start: skapa en profil och en slut punkt för Resource Manager-mall'
titleSuffix: Azure Content Delivery Network
description: I den här snabb starten lär du dig att skapa en Azure Content Delivery Network-profil och en slut punkt för en Resource Manager-mall
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: a36484bea3a2bc9caadc5cf4482aae3bea84eafa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560471"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Snabb start: skapa en mall för Azure CDN profil och slut punkts ARM

Kom igång med Azure Content Delivery Network (CDN) med hjälp av en Azure Resource Manager-mall (ARM-mall). Mallen distribuerar en profil och en slut punkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Den här mallen har kon figurer ATS för att skapa en:

* Profil
* Slutpunkt

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* **[Microsoft. CDN/profiler](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Distribuera mallen

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portalen

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **resurs grupper** i det vänstra fönstret.

3. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är **myResourceGroupCDN**

4. Kontrol lera att följande resurser har skapats i resurs gruppen:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN resurs grupp" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="azure-cli"></a>Azure CLI

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

Ta bort resurs gruppen, CDN-profilen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupps **myResourceGroupCDN** som innehåller CDN-profilen och slut punkten och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* CDN-profil
* Slutpunkt

Om du vill veta mer om Azure CDN och Azure Resource Manager fortsätter du till artiklarna nedan.

> [!div class="nextstepaction"]
> [Självstudie: Använd CDN för att hantera statiskt innehåll från en webbapp](cdn-add-to-web-app.md)
