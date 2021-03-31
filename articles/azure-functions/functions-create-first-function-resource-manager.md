---
title: Skapa din första funktion med Azure Resource Manager mallar
description: Skapa och distribuera till Azure en enkel HTTP-utlöst funktion utan server med hjälp av en Azure Resource Manager mall (ARM-mall).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422832"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Snabb start: skapa och Distribuera Azure Functions resurser från en ARM-mall

I den här artikeln använder du en Azure Resource Manager mall (ARM-mall) för att skapa en funktion som svarar på HTTP-begäranden. 

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

### <a name="azure-account"></a>Azure-konto 

Innan du börjar måste du ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Skapa ett lokalt Functions-projekt

Den här artikeln kräver ett kod projekt för lokala Functions som körs på de Azure-resurser som du skapar. Om du inte först skapar ett projekt för publicering kan du inte slutföra distributions avsnittet i den här artikeln. 

Välj någon av följande flikar, Följ länken och slutför avsnittet för att skapa en Function-app på det språk som du väljer:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Skapa ditt lokala Functions-projekt på ditt valda språk i Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Skapa ditt lokala Functions-projekt i Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Skapa ditt lokala Functions-projekt på ditt valda språk från kommando raden:

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

+ [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto, vilket krävs av functions.
+ [**Microsoft. Web/Server grupper**](/azure/templates/microsoft.web/serverfarms): skapa en server lös förbruknings värd plan för Function-appen.
+ [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): skapa en Function-app.
+ [**Microsoft. Insights/komponenter**](/azure/templates/microsoft.insights/components): skapa en Application Insights-instans för övervakning.

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

Sedan validerar du de värd resurser för funktionen som du skapade genom att publicera projektet till Azure och anropa HTTP-slutpunkten för funktionen.

### <a name="publish-the-function-project-to-azure"></a>Publicera funktions projektet till Azure

Använd följande steg för att publicera projektet till de nya Azure-resurserna:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

I utdata kopierar du URL: en för HTTP-utlösaren. Du kan använda detta för att testa att din funktion körs i Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I **Välj ett publicerings mål** väljer du **Azure Functions förbruknings plan** med **Välj befintlig** och väljer **Skapa profil**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Välj ett befintligt publicerings mål":::

1. Välj din **prenumeration**, expandera resurs grupp, välj din Function-app och välj **OK**.

1. När publiceringen är klar kopierar du **webbplatsens URL**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Kopiera webbplats-URL: en från publicerings sammanfattningen":::

1. Lägg till sökvägen `/api/<FUNCTION_NAME>?name=Functions` , där `<FUNCTION_NAME>` är namnet på din funktion. Den URL som anropar funktionen HTTP-utlösare har följande format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Du använder den här URL: en för att testa funktionen HTTP-utlösare som körs i Azure.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Om du vill publicera din lokala kod i en Function-app i Azure använder du `publish` kommandot:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

I det här exemplet ersätter du `<FUNCTION_APP_NAME>` med namnet på din Function-app. Du kan behöva logga in igen med hjälp av `az login` . 

I utdata kopierar du URL: en för HTTP-utlösaren. Du kan använda detta för att testa att din funktion körs i Azure.

---

### <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Klistra in URL: en som du kopierade för HTTP-begäran i webbläsarens Adress fält, se till att `name` frågesträngen som `?name=Functions` har lagts till i slutet av URL: en och kör sedan begäran. 

Du bör se ett svar som:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg och lägger till en Azure Storage utgående bindning för kö, bör du se till att alla resurser är på plats när du bygger på det du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Ersätt `<RESOURCE_GROUP_NAME>` med namnet på din resurs grupp.

## <a name="next-steps"></a>Nästa steg

Nu när du har publicerat din första funktion kan du läsa mer genom att lägga till en utgående bindning till din funktion.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md)

---
