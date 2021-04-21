---
title: Skapa en Azure App Configuration med hjälp av Azure Resource Manager (ARM-mall)
titleSuffix: Azure App Configuration
description: Lär dig hur du skapar en Azure App Configuration med hjälp av Azure Resource Manager (ARM-mall).
author: GrantMeStrength
ms.author: jken
ms.date: 10/16/2020
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 92ca80a6c807394c45be8f0187c7add736ba83ce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831788"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Snabbstart: Skapa en Azure App Configuration med hjälp av en ARM-mall

Den här snabbstarten beskriver hur du :

- Distribuera en App Configuration med hjälp av en Azure Resource Manager mall (ARM-mall).
- Skapa nyckelvärden i ett App Configuration med hjälp av ARM-mall.
- Läsa nyckelvärden i ett App Configuration från ARM-mall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Det skapar en ny App Configuration med två nyckelvärden inuti. Den använder sedan funktionen `reference` för att mata ut värdena för de två nyckel/värde-resurserna. Om du läser nyckelns värde på det här sättet kan det användas på andra ställen i mallen.

Snabbstarten använder `copy` elementet för att skapa flera instanser av nyckel/värde-resursen. Mer information om elementet finns `copy` i Resurs [iteration i ARM-mallar.](../azure-resource-manager/templates/copy-resources.md)

> [!IMPORTANT]
> Den här mallen kräver App Configuration version av `2020-07-01-preview` resursprovidern eller senare. Den här versionen använder `reference` funktionen för att läsa nyckelvärden. Funktionen `listKeyValue` som användes för att läsa nyckelvärden i den tidigare versionen är inte tillgänglig från och med version `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft.AppConfiguration/configurationStores:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores)skapa en App Configuration store.
- [Microsoft.AppConfiguration/configurationStores/keyValues:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)skapa ett nyckelvärde i App Configuration arkivet.

> [!TIP]
> Resursens `keyValues` namn är en kombination av nyckel och etikett. Nyckeln och etiketten är sammanfogade med `$` avgränsaren. Etiketten är valfri. I exemplet ovan skapar resursen `keyValues` med namnet `myKey` ett nyckelvärde utan etikett.
>
> Med procentkodning, även kallat URL-kodning, kan nycklar eller etiketter innehålla tecken som inte tillåts i RESURSnamn för ARM-mallar. `%` är inte heller ett tillåtet tecken, `~` så används i dess ställe. Följ dessa steg om du vill koda ett namn korrekt:
>
> 1. Tillämpa URL-kodning
> 2. Ersätt `~` med `~7E`
> 3. Ersätt `%` med `~`
>
> Om du till exempel vill skapa ett nyckel/värde-par med nyckelnamn `AppName:DbEndpoint` och `Test` etikettnamn ska resursnamnet vara `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> App Configuration tillåter åtkomst till nyckel/värde-data via [en privat länk](concept-private-endpoint.md) från ditt virtuella nätverk. När funktionen är aktiverad nekas som standard alla förfrågningar App Configuration data över det offentliga nätverket. Eftersom ARM-mallen körs utanför ditt virtuella nätverk tillåts inte dataåtkomst från en ARM-mall. Om du vill tillåta dataåtkomst från en ARM-mall när en privat länk används kan du aktivera offentlig nätverksåtkomst med hjälp av följande Azure CLI-kommando. Det är viktigt att tänka på säkerhetsriskerna med att aktivera offentlig nätverksåtkomst i det här scenariot.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett App Configuration med två nyckelvärden inuti.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Du kan också distribuera mallen med hjälp av följande PowerShell-cmdlet. Nyckelvärdena visas i powershell-konsolens utdata.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Azure Portal skriver du **App Configuration**. Välj **App Configuration** i listan.
1. Välj den nyligen skapade App Configuration resursen.
1. Under **Åtgärder klickar** du på **Konfigurationsutforskaren.**
1. Kontrollera att det finns två nyckelvärden.

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs tar du bort den, App Configuration arkivet och alla relaterade resurser. Om du planerar att använda App Configuration butik i framtiden kan du hoppa över att ta bort den. Om du inte ska fortsätta att använda det här arkivet tar du bort alla resurser som har skapats i den här snabbstarten genom att köra följande cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till funktionsflaggan Key Vault referens till en App Configuration store finns i arm-mallexempel nedan.

- [101-app-configuration-store-ff](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-app-configuration-store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)
