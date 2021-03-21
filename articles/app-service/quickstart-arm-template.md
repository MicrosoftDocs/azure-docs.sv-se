---
title: Skapa en App Service-app med en Azure Resource Manager-mall
description: Skapa din första app för att Azure App Service på några sekunder med hjälp av en Azure Resource Manager-mall (ARM-mall), som är en av många sätt att distribuera till App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-arm-template-uiex
ms.openlocfilehash: 8b048127c8a5581a34a62cfb67c19ba7aa50f2d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701594"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Snabb start: skapa App Service app med en ARM-mall

Kom igång med [Azure App Service](overview.md) genom att distribuera en app till molnet med hjälp av en Azure Resource Manager-mall (arm-mall) och [Azure CLI](/cli/azure/get-started-with-azure-cli) i Cloud Shell. Eftersom du använder en kostnads fri App Service nivå debiteras du inga kostnader för att slutföra den här snabb starten.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

Använd följande knapp för att distribuera i **Linux**:

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

Använd följande knapp för att distribuera i **Windows**:

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Granska mallen

::: zone pivot="platform-windows"
Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Den distribuerar en App Service plan och en App Service-app i Windows. Den är kompatibel med .NET Core, .NET Framework, PHP, Node.js och statiska HTML-appar. För Java, se [skapa Java-app](./quickstart-java.md).

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

* [**Microsoft. Web/Server grupper**](/azure/templates/microsoft.web/serverfarms): skapa en app service plan.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): skapa en app service-app.

Den här mallen innehåller flera parametrar som är fördefinierade för din bekvämlighet. Se tabellen nedan för parameter standarder och deras beskrivningar:

| Parametrar | Typ    | Standardvärde                | Beskrivning |
|------------|---------|------------------------------|-------------|
| webAppName | sträng  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Appnamn |
| location   | sträng  | "[[resourceGroup (). location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-region |
| sku        | sträng  | F1                         | Instans storlek (F1 = kostnads fri nivå) |
| language   | sträng  | Studio                       | Programmerings språks tack (.net, php, Node, HTML) |
| Projektet | boolean | Falskt                        | True = distribuera "Hello World"-appen |
| Repo    | sträng  | " "                          | Extern git-lagrings platsen (valfritt) |
::: zone-end
::: zone pivot="platform-linux"
Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Den distribuerar en App Service plan och en App Service-app i Linux. Den är kompatibel med alla programmeringsspråk som stöds på App Service.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

* [**Microsoft. Web/Server grupper**](/azure/templates/microsoft.web/serverfarms): skapa en app service plan.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): skapa en app service-app.

Den här mallen innehåller flera parametrar som är fördefinierade för din bekvämlighet. Se tabellen nedan för parameter standarder och deras beskrivningar:

| Parametrar | Typ    | Standardvärde                | Beskrivning |
|------------|---------|------------------------------|-------------|
| webAppName | sträng  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Appnamn |
| location   | sträng  | "[[resourceGroup (). location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-region |
| sku        | sträng  | F1                         | Instans storlek (F1 = kostnads fri nivå) |
| linuxFxVersion   | sträng  | "DOTNETCORE&#124;3,0        | "&#124; version av programmerings språks stack" |
| Repo    | sträng  | " "                          | Extern git-lagrings platsen (valfritt) |

---
::: zone-end

## <a name="deploy-the-template"></a>Distribuera mallen

Azure CLI används här för att distribuera mallen. Du kan också använda Azure Portal, Azure PowerShell och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

Följande kod skapar en resurs grupp, ett App Service plan och en webbapp. En standard resurs grupp, App Service plan och plats har angetts för dig. Ersätt `<app-name>` med ett globalt unikt namn på appen (giltiga tecken är `a-z` , `0-9` och `-` ).

::: zone pivot="platform-windows"
Kör koden nedan för att distribuera en .NET Framework-app i Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Om du vill distribuera en annan språks tack uppdaterar du `linuxFxVersion` med lämpliga värden. Exempel visas nedan. Om du vill visa aktuella versioner kör du följande kommando i Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Språk    | Exempel                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---
::: zone-end

> [!NOTE]
> Du hittar fler [Azure App Service mall-exempel här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Bläddra till `http://<app_name>.azurewebsites.net/` och kontrol lera att den har skapats.

## <a name="clean-up-resources"></a>Rensa resurser

[Ta bort resurs gruppen när den](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera från lokal Git](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python med Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP med MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Ansluta till Azure SQL Database med Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain.md)
