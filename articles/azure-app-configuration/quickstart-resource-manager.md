---
title: Skapa ett Azure App konfigurations lager med hjälp av Azure Resource Manager-mall (ARM-mall)
titleSuffix: Azure App Configuration
description: Lär dig hur du skapar ett Azure App konfigurations lager med hjälp av Azure Resource Manager-mall (ARM-mall).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: c35b6d27a5e6d7ffd2a9f76ea201af4a138a77df
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219119"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Snabb start: skapa en Azure App konfigurations lagring med en ARM-mall

I den här snabb starten beskrivs hur du:

- Distribuera ett konfigurations lager för appar med hjälp av en Azure Resource Manager-mall (ARM-mall).
- Skapa nyckel värden i ett program konfigurations lager med ARM-mall.
- Läs nyckel värden i ett konfigurations Arkiv för appar från ARM-mallen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Det skapar ett nytt konfigurations lager för appar med två nyckel värden i. Den använder sedan `reference` funktionen för att mata ut värdena för de två nyckel värde resurserna. Genom att läsa nyckelns värde på det här sättet kan det användas på andra platser i mallen.

Snabb starten använder- `copy` elementet för att skapa flera instanser av nyckel värdes resurs. Mer information om `copy` -elementet finns [i resurs upprepning i arm-mallar](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Den här mallen kräver version av resurs leverantör för program konfiguration `2020-07-01-preview` eller senare. Den här versionen använder `reference` funktionen för att läsa nyckel värden. `listKeyValue`Funktionen som användes för att läsa nyckel värden i den tidigare versionen är inte tillgänglig från och med version `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores): skapa ett konfigurations lager för appar.
- [Microsoft. AppConfiguration/configurationStores/nyckel värden](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues): skapa ett nyckel värde i appens konfigurations arkiv.

> [!TIP]
> `keyValues`Resurs namnet är en kombination av nyckel och etikett. Nyckeln och etiketten är kopplade till `$` avgränsaren. Etiketten är valfri. I exemplet ovan `keyValues` skapar resursen med namn `myKey` ett nyckel värde utan en etikett.
>
> Procent kodning, även kallat URL-kodning, tillåter att nycklar eller etiketter innehåller tecken som inte tillåts i ARM-mallens resurs namn. `%` är inte ett tillåtet värde antingen, så `~` används i sitt ställe. Om du vill koda ett namn korrekt följer du dessa steg:
>
> 1. Använd URL-kodning
> 2. Ersätt `~` med `~7E`
> 3. Ersätt `%` med `~`
>
> Om du till exempel vill skapa ett nyckel/värde-par med nyckel namn `AppName:DbEndpoint` och etikett namn `Test` bör resurs namnet vara `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> Med konfiguration av appar kan du använda nyckel värdes data via en [privat länk](concept-private-endpoint.md) från det virtuella nätverket. När funktionen är aktive rad nekas som standard alla begär Anden för konfigurations data för din app via det offentliga nätverket. Eftersom ARM-mallen körs utanför det virtuella nätverket tillåts inte data åtkomst från en ARM-mall. Om du vill tillåta data åtkomst från en ARM-mall när en privat länk används, kan du aktivera offentlig nätverks åtkomst med hjälp av följande Azure CLI-kommando. Det är viktigt att tänka på säkerhets konsekvenserna av att aktivera offentlig nätverks åtkomst i det här scenariot.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett konfigurations lager för appar med två nyckel värden i.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Du kan också distribuera mallen med hjälp av följande PowerShell-cmdlet. Nyckel värden visas i utdata från PowerShell-konsolen.

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
1. Skriv **app Configuration** i rutan Azure Portal Sök. Välj **app-konfiguration** i listan.
1. Välj den nyligen skapade konfigurations resursen för appen.
1. Under **åtgärder** klickar du på **konfigurations Utforskaren**.
1. Kontrol lera att det finns två nyckel värden.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, konfigurations arkivet för appen och alla relaterade resurser. Om du planerar att använda appens konfigurations Arkiv i framtiden kan du hoppa över borttagningen. Om du inte kommer att fortsätta att använda den här butiken tar du bort alla resurser som skapats i den här snabb starten genom att köra följande cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du lägger till funktions flagga och Key Vault referens till ett konfigurations Arkiv för appar, kontrollerar du följande ARM-mallar

- [101-app-Configuration-Store-FF](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-app-Configuration-Store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)