---
title: Skapa en App Service med en Azure Resource Manager mall
description: Skapa din första app för Azure App Service på några sekunder med en Azure Resource Manager mall (ARM-mall), som är ett av många sätt att distribuera till App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769015"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Snabbstart: Skapa App Service med en ARM-mall

Kom igång med [Azure App Service](overview.md) genom att distribuera en app till molnet med hjälp av en <abbr title="En JSON-fil som deklarativt definierar en eller flera Azure-resurser och beroenden mellan de distribuerade resurserna. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger.">ARM-mall</abbr> och [Azure CLI](/cli/azure/get-started-with-azure-cli) i Cloud Shell. Eftersom du använder en kostnadsfri App Service du inga kostnader för att slutföra den här snabbstarten. <abbr title="I deklarativ syntax beskriver du din avsedda distribution utan att skriva sekvensen med programmeringskommandon för att skapa den.">Mallen använder deklarativ syntax.</abbr>

 Om din miljö uppfyller kraven och du är bekant med ARM-mallar [väljer](../azure-resource-manager/templates/overview.md)du **knappen Distribuera till** Azure. Mallen öppnas på Azure-portalen.

::: zone pivot="platform-windows"
[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Granska mallen

::: zone pivot="platform-windows"
Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Den distribuerar en App Service plan och en App Service-app i Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Vilka resurser och parametrar definieras i mallen?</summary>

Två Azure-resurser definieras i mallen:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)skapa en App Service plan.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)Skapa en App Service app.

I följande tabell beskrivs standardparametrarna och deras beskrivningar:

| Parametrar | Typ    | Standardvärde                | Beskrivning |
|------------|---------|------------------------------|-------------|
| webAppName | sträng  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Appnamn |
| location   | sträng  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Appregion |
| sku        | sträng  | "F1"                         | Instansstorlek (F1 = kostnadsfri nivå) |
| language   | sträng  | ".net"                       | Programmeringsspråkstack (.net, php, node, html) |
| helloWorld | boolean | Falskt                        | Sant = Distribuera "Hello World"-app |
| repoUrl    | sträng  | " "                          | Extern Git-lagringsplatsen (valfritt) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Den distribuerar en App Service plan och en App Service-app i Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Den här mallen innehåller Azure-resurser och parametrar som har definierats för din bekvämlighet.

<details>
<summary>Vilka resurser och parametrar definieras i mallen?</summary>

Två Azure-resurser definieras i mallen:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)skapa en App Service plan.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)skapa en App Service app.

I följande tabell beskrivs standardparametrarna och deras beskrivningar:

| Parametrar | Typ    | Standardvärde                | Beskrivning |
|------------|---------|------------------------------|-------------|
| webAppName | sträng  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Appnamn |
| location   | sträng  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Appregion |
| sku        | sträng  | "F1"                         | Instansstorlek (F1 = kostnadsfri nivå) |
| linuxFxVersion   | sträng  | "DOTNETCORE&#124;3.0        | "Programmeringsspråkstack &#124; Version" |
| repoUrl    | sträng  | " "                          | Extern Git-lagringsplatsen (valfritt) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Distribuera mallen

::: zone pivot="platform-windows"
Kör koden nedan för att distribuera en .NET Framework-app i Windows med Hjälp av Azure CLI. 

Ersätt <abbr title="Giltiga tecken är `a-z` , `0-9` och `-` .">`<app-name>`</abbr> med ett globalt unikt appnamn. Om du vill lära dig mer <abbr title="Du kan också använda Azure Portal, Azure PowerShell och REST API.">distributionsmetoder</abbr>, se [Distribuera mallar.](../azure-resource-manager/templates/deploy-powershell.md) Du hittar fler Azure App Service [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Kör koden nedan för att skapa en Python-app i Linux. 

Ersätt <abbr title="Giltiga tecken är `a-z` , `0-9` och `-` .">`<app-name>`</abbr> med ett globalt unikt appnamn.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Vad gör koden?</summary>
<p>Kommandona gör följande:</p>
<ul>
<li>Skapa en standard <abbr title="En logisk container för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>.</li>
<li>Skapa en standard <abbr title="Planen som anger plats, storlek och funktioner för webbservergruppen som är värd för din app.">App Service-plan</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Skapa en <abbr title="Representationen av webbappen, som innehåller din appkod, DNS-värdnamn, certifikat och relaterade resurser. "> App Service app</abbr></a> med det angivna namnet.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Hur gör jag för att distribuera en annan språkstack?</summary>
Om du vill distribuera en annan språkstack uppdaterar du <abbr title="Den här mallen är kompatibel med .NET Core-, .NET Framework-, PHP-, Node.js- och statiska HTML-appar. "> språkparameter</abbr> med lämpliga värden. För Java, se <a href="/azure/app-service/quickstart-java-uiex">Skapa Java-app</a>.

| Parametrar | Typ    | Standardvärde                | Beskrivning |
|------------|---------|------------------------------|-------------|
| language   | sträng  | ".net"                       | Programmeringsspråkstack (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Hur gör jag för att distribuera en annan språkstack?</summary>
Om du vill distribuera en annan språkstack uppdaterar `linuxFxVersion` du med lämpliga värden. Exemplen visas nedan. Om du vill visa aktuella versioner kör du följande kommando i Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Språk    | Exempel                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Verifiera distributionen

Bläddra till `http://<app_name>.azurewebsites.net/` och kontrollera att den har skapats.

<hr/>

## <a name="5-clean-up-resources"></a>5. Rensa resurser

Ta bort resursgruppen [när den inte längre behövs.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)

<hr/>

## <a name="next-steps"></a>Nästa steg

- [Distribuera från lokal Git](deploy-local-git.md)
- [ASP.NET Core med SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Python med Postgres](tutorial-python-postgresql-app.md)
- [PHP med MySQL](tutorial-php-mysql-app.md)
- [Ansluta till Azure SQL databas med Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mappa anpassad domän](app-service-web-tutorial-custom-domain-uiex.md)
