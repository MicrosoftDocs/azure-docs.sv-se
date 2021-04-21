---
title: 'Snabbstart: Skapa en profil och slutpunkt – Resource Manager mall'
titleSuffix: Azure Content Delivery Network
description: I den här snabbstarten lär du dig hur du skapar en Azure Content Delivery Network-profil och slutpunkt för en Resource Manager mall
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
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779050"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Snabbstart: Skapa en Azure CDN och slutpunkt – ARM-mall

Kom igång med Azure Content Delivery Network (CDN) med hjälp av en Azure Resource Manager (ARM-mall). Mallen distribuerar en profil och en slutpunkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Den här mallen är konfigurerad för att skapa en:

* Profil
* Slutpunkt

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

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

2. Välj **Resursgrupper** i det vänstra fönstret.

3. Välj den resursgrupp som du skapade i föregående avsnitt. Standardnamnet för resursgruppen är **myResourceGroupCDN**

4. Kontrollera att följande resurser har skapats i resursgruppen:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN resursgrupp" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="azure-cli"></a>Azure CLI

När den inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla resurser som finns i den.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser i den.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portalen

Ta bort resursgruppen, CDN-profilen och alla relaterade resurser när de inte längre behövs. Välj resursgruppen **myResourceGroupCDN som** innehåller CDN-profilen och CDN-slutpunkten och välj sedan Ta **bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* CDN-profil
* Slutpunkt

Om du vill veta Azure CDN och Azure Resource Manager kan du fortsätta till artiklarna nedan.

> [!div class="nextstepaction"]
> [Självstudie: Använda CDN för att betjäna statiskt innehåll från en webbapp](cdn-add-to-web-app.md)
