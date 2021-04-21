---
title: Skapa din första funktion med hjälp Azure Resource Manager mallar
description: Skapa och distribuera till Azure en enkel HTTP-utlöst serverlös funktion med hjälp av en Azure Resource Manager mall (ARM-mall).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0badc233597c13228e9826ed062cc15828c65cf2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833390"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Snabbstart: Skapa och distribuera Azure Functions från en ARM-mall

I den här artikeln använder du en Azure Resource Manager mall (ARM-mall) för att skapa en funktion som svarar på HTTP-begäranden. 

När du slutför den här snabbstarten medför det en liten kostnad på några cent eller mindre för ditt Azure-konto. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

### <a name="azure-account"></a>Azure-konto 

Innan du börjar måste du ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/)

### <a name="create-a-local-functions-project"></a>Skapa ett lokalt funktionsprojekt

Den här artikeln kräver att ett kodprojekt för lokala funktioner körs på de Azure-resurser som du skapar. Om du inte först skapar ett projekt att publicera kan du inte slutföra distributionsavsnittet i den här artikeln. 

Välj någon av följande flikar, följ länken och slutför avsnittet för att skapa en funktionsapp på det språk du önskar:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Skapa ditt lokala funktionsprojekt på ditt valda språk i Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Skapa ditt lokala funktionsprojekt i Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Skapa ditt lokala funktionsprojekt på det språk du valt från kommandoraden:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

När du har skapat projektet lokalt skapar du de resurser som krävs för att köra den nya funktionen i Azure. 

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Följande fyra Azure-resurser skapas med den här mallen:

+ [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)skapa ett Azure Storage-konto som krävs av Functions.
+ [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)skapa en serverlös förbrukningsvärdplan för funktionsappen.
+ [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)skapa en funktionsapp.
+ [**microsoft.insights/components:**](/azure/templates/microsoft.insights/components)skapa en Application Insights instans för övervakning.

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Verifiera distributionen

Därefter validerar du funktionsappens värdresurser som du skapade genom att publicera projektet till Azure och anropa HTTP-slutpunkten för funktionen.

### <a name="publish-the-function-project-to-azure"></a>Publicera funktionsprojektet i Azure

Använd följande steg för att publicera projektet till de nya Azure-resurserna:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

I utdata kopierar du URL:en för HTTP-utlösaren. Du använder det här för att testa funktionen som körs i Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I **Välj ett publiceringsmål** väljer du **Azure Functions förbrukningsplan med** Välj **befintlig** och väljer **Skapa profil.**

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Välj ett befintligt publiceringsmål":::

1. Välj din **prenumeration,** expandera resursgruppen, välj din funktionsapp och välj **OK.**

1. När publiceringen är klar kopierar du **webbplats-URL:en**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Kopiera webbplats-URL:en från publiceringssammanfattningen":::

1. Lägg till `/api/<FUNCTION_NAME>?name=Functions` sökvägen , `<FUNCTION_NAME>` där är namnet på din funktion. Den URL som anropar http-utlösarfunktionen har följande format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Du använder den här URL:en för att testa http-utlösarfunktionen som körs i Azure.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Om du vill publicera din lokala kod till en funktionsapp i Azure använder du `publish` kommandot :

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

I det här exemplet `<FUNCTION_APP_NAME>` ersätter du med namnet på din funktionsapp. Du kan behöva logga in igen med hjälp av `az login` . 

I utdata kopierar du URL:en för HTTP-utlösaren. Du använder det här för att testa funktionen som körs i Azure.

---

### <a name="invoke-the-function-on-azure"></a>Anropa funktionen i Azure

Klistra in den URL som du kopierade för HTTP-begäran i webbläsarens adressfält, kontrollera att frågesträngen har lagts till i slutet av url:en och kör sedan `name` `?name=Functions` begäran. 

Du bör se ett svar som:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg och lägger till en Azure Storage-köutdatabindning behåller du alla dina resurser på plats eftersom du bygger vidare på det du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika ytterligare kostnader.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Ersätt `<RESOURCE_GROUP_NAME>` med namnet på resursgruppen.

## <a name="next-steps"></a>Nästa steg

Nu när du har publicerat din första funktion kan du lära dig mer genom att lägga till en utdatabindning i funktionen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md)

---
