---
title: Snabbstart – Skapa och distribuera logikapparbetsflöde med hjälp av Azure Resource Manager mallar
description: Skapa och distribuera en logikapp med hjälp av Azure Resource Manager mallar
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 04/01/2021
ms.openlocfilehash: a177cf48c479f2e2afb0d6d23b1f4695ed57cfe4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875619"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Snabbstart: Skapa och distribuera ett logikapparbetsflöde med hjälp av en ARM-mall

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) är en molntjänst som hjälper dig att skapa och köra automatiserade arbetsflöden som integrerar data, appar, molnbaserade tjänster och lokala system genom att välja bland hundratals [anslutningsappar.](/connectors/connector-reference/connector-reference-logicapps-connectors) Den här snabbstarten fokuserar på processen för att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa en grundläggande logikapp som kontrollerar status för Azure enligt ett schema per timme. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

I den här snabbstarten [**används**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) mallen Skapa en logikapp, som du hittar i [Azure-snabbstartsgalleriet](https://azure.microsoft.com/resources/templates) mallar, men som är för lång för att visas här. I stället kan du granska snabbstartsmallens ["azuredeploy.jspå filen"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) i mallgalleriet.

Snabbstartsmallen skapar ett logikapparbetsflöde som använder upprepningsutlösaren, som är inställd på att köras varje timme, och en inbyggd [ *HTTP-åtgärd*](../connectors/built-in.md), som anropar en URL som returnerar status för Azure. En inbyggd åtgärd är inbyggd i Azure Logic Apps plattformen.

Den här mallen skapar följande Azure-resurs:

* [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows), som skapar arbetsflödet för en logikapp.

Om du vill hitta fler snabbstartsmallar Azure Logic Apps kan du läsa [Microsoft.Logic-mallarna](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) i galleriet.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Distribuera mallen

Följ det alternativ som du vill använda för att distribuera snabbstartsmallen:

| Alternativ | Beskrivning |
|--------|-------------|
| [Azure-portalen](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Om din Azure-miljö uppfyller kraven och du är van att använda ARM-mallar hjälper de här stegen dig att logga in direkt på Azure och öppna snabbstartsmallen i Azure Portal. Mer information finns i Distribuera [resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Azures kommandoradsgränssnitt (Azure CLI) är en uppsättning kommandon för att skapa och hantera Azure-resurser. Om du vill köra dessa kommandon behöver du Azure CLI version 2.6 eller senare. Om du vill kontrollera CLI-versionen skriver du `az --version` . Mer information finns i de här ämnena: <p><p>- [Vad är Azure CLI?](/cli/azure/what-is-azure-cli) <br>- [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Azure PowerShell tillhandahåller en uppsättning cmdletar som använder Azure Resource Manager-modellen för att hantera dina Azure-resurser. Mer information finns i de här ämnena: <p><p>- [Azure PowerShell översikt](/powershell/azure/azurerm/overview) <br>- [Introduktion till Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az) <br>- [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure Resource Management REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Azure tillhandahåller Representational State Transfer API:er (REST), som är tjänstslutpunkter som stöder HTTP-åtgärder (metoder) som du använder för att skapa, hämta, uppdatera eller ta bort åtkomst till tjänstresurser. Mer information finns i [Kom igång med Azure REST API](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj följande bild för att logga in med ditt Azure-konto och öppna snabbstartsmallen i Azure Portal:

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. I portalen går du till sidan **Skapa en logikapp med hjälp av en** mall och anger eller väljer följande värden:

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-subscription-name*> | Namnet på Azure-prenumerationen som ska användas |
   | **Resursgrupp** | <*Azure-resource-group-name*> | Namnet på en ny eller befintlig Azure-resursgrupp. I det här exemplet används `Check-Azure-Status-RG`. |
   | **Region** | <*Azure-region*> | Azure-datacenterregionen för att använda din logikapp. I det här exemplet används `West US`. |
   | **Logikappens namn** | <*logic-app-name*> | Namnet som ska användas för logikappen. I det här exemplet används `Check-Azure-Status-LA`. |
   | **Test-URI** | <*test-URI*> | Tjänstens URI att anropa baserat på ett specifikt schema. I det här `https://status.azure.com/en-us/status/` exemplet används , som är statussidan för Azure. |
   | **Plats** |  <*Azure-region-for-all-resources*> | Azure-regionen som ska användas för alla resurser, om den skiljer sig från standardvärdet. I det här exemplet används `[resourceGroup().location]` standardvärdet, , som är resursgruppens plats. |
   ||||

   Så här ser sidan ut med de värden som används i det här exemplet:

   ![Ange information för snabbstartsmallen](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. När du är klar väljer du **Granska + skapa.**

1. Fortsätt med stegen i [Granska distribuerade resurser.](#review-deployed-resources)

#### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

Mer information finns i de här ämnena:

* [Azure CLI: az deployment group](/cli/azure/deployment/group)
* [Distribuera resurser med ARM-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

Mer information finns i de här ämnena:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[REST-API](#tab/rest-api)

1. Om du inte vill använda en befintlig Azure-resursgrupp skapar du en ny resursgrupp genom att följa den här syntaxen för den begäran som du skickar till resurshanterings-REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Värde | Beskrivning |
   |-------|-------------|
   | `subscriptionId`| GUID för den Azure-prenumeration som du vill använda |
   | `resourceGroupName` | Namnet på den Azure-resursgrupp som ska skapas. I det här exemplet används `Check-Azure-Status-RG`. |
   |||

   Exempel:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Mer information finns i de här ämnena:

   * [Referens för Azure REST API – Så här anropar du Azure REST API:er](/rest/api/azure/)
   * [Resource Management REST API: Resursgrupper – Skapa eller uppdatera](/rest/api/resources/resourcegroups/createorupdate).

1. Om du vill distribuera snabbstartsmallen till resursgruppen följer du den här syntaxen för den begäran som du skickar till resource management-REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Värde | Beskrivning |
   |-------|-------------|
   | `subscriptionId`| GUID för den Azure-prenumeration som du vill använda |
   | `resourceGroupName` | Namnet på den Azure-resursgrupp som ska användas. I det här exemplet används `Check-Azure-Status-RG`. |
   | `deploymentName` | Namnet som ska användas för distributionen. I det här exemplet används `Check-Azure-Status-LA`. |
   |||

   Exempel:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Mer information finns i [Resource Management REST API: Deployments - Create or Update](/rest/api/resources/deployments/createorupdate).

1. Om du vill ange de värden som ska användas för distributionen, till exempel Azure-regionen och länkar till snabbstartsmallen och [parameterfilen](../azure-resource-manager/templates/template-parameters.md), som innehåller värdena för snabbstartsmallen som ska användas vid distributionen, följer du den här syntaxen för begärandetexten som du skickar till resurshanterings-REST API:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | `location`| <*Azure-region*> | Den Azure-region som ska användas för distribution. I det här exemplet används `West US`. |
   | `templateLink` : `uri` | <*quickstart-template-URL*> | URL-platsen för snabbstartsmallen som ska användas för distribution: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*quickstart-template-parameter-file-URL*> | URL-platsen för snabbstartsmallens parameterfil som ska användas för distribution: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Mer information om parameterfilen Resource Manager finns i följande avsnitt: <p><p>- [Skapa Resource Manager parameterfil](../azure-resource-manager/templates/parameter-files.md) <br>- [Självstudie: Använda parameterfiler för att distribuera ARM-mallen](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*distributionsläge*> | Kör antingen en inkrementell uppdatering eller en fullständig uppdatering. I det här `Incremental` exemplet används , som är standardvärdet. Mer information finns i [Azure Resource Manager distributionslägen](../azure-resource-manager/templates/deployment-modes.md). |
   |||

   Exempel:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

Mer information finns i de här ämnena:

* [Resurshanterings-REST API](/rest/api/resources/)
* [Distribuera resurser med ARM-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Om du vill visa logikappen kan du använda Azure Portal, köra ett skript som du skapar med Azure CLI eller Azure PowerShell eller använda logikappens REST API.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Portal anger du logikappens namn, som finns i `Check-Azure-Status-LA` det här exemplet. Välj din logikapp i resultatlistan.

1. I Azure Portal du och väljer din logikapp, som finns i `Check-Azure-Status-RG` det här exemplet.

1. När Logikappdesignern öppnas granskar du logikappen som skapats av snabbstartsmallen.

1. Om du vill testa logikappen går du till designerverktygsfältet och väljer **Kör**.

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Mer information finns i [Azure CLI: az logic workflow show](/cli/azure/logic/workflow#az_logic_workflow_show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Mer information finns i [Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Värde | Beskrivning |
|-------|-------------|
| `subscriptionId`| GUID för Azure-prenumerationen där du distribuerade snabbstartsmallen. |
| `resourceGroupName` | Namnet på den Azure-resursgrupp där du distribuerade snabbstartsmallen. I det här exemplet används `Check-Azure-Status-RG`. |
| `workflowName` | Namnet på logikappen som du distribuerade. I det här exemplet används `Check-Azure-Status-LA`. |
|||

Exempel:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Mer information finns i [Logic Apps REST API: Arbetsflöden – Hämta](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande snabbstarter och självstudier kanske du vill behålla dessa resurser. När du inte längre behöver logikappen tar du bort resursgruppen med hjälp av antingen Azure Portal, Azure CLI, Azure PowerShell eller Resource Management REST API.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I listan Azure Portal du och väljer den resursgrupp som du vill ta bort, vilket är `Check-Azure-Status-RG` i det här exemplet.

1. På resursgruppmenyn väljer du **Översikt om** du inte redan har valt den. På översiktssidan väljer du Ta **bort resursgrupp.**

1. Bekräfta genom att ange namnet på resursgruppen.

Mer information finns i Ta [bort resursgrupp.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Mer information finns i [Azure CLI: az group delete](/cli/azure/group#az_group_delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Mer information finns i [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Värde | Beskrivning |
|-------|-------------|
| `subscriptionId`| GUID för Azure-prenumerationen där du distribuerade snabbstartsmallen. |
| `resourceGroupName` | Namnet på den Azure-resursgrupp där du distribuerade snabbstartsmallen. I det här exemplet används `Check-Azure-Status-RG`. |
|||

Exempel:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Mer information finns i [Resource Management REST API: Resource Groups - Delete](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
