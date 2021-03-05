---
title: Snabb start – skapa och distribuera Logic app-arbetsflöde med Azure Resource Manager mallar
description: Skapa och distribuera en Logic app med hjälp av Azure Resource Manager mallar
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 06/30/2020
ms.openlocfilehash: 8cee79efd9be53ddb9d450f643c04f21ae5e7fca
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214005"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Snabb start: skapa och distribuera ett Logic app-arbetsflöde med en ARM-mall

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) är en moln tjänst som hjälper dig att skapa och köra automatiserade arbets flöden som integrerar data, appar, molnbaserade tjänster och lokala system genom att välja mellan [hundratals kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors). Den här snabb starten fokuserar på processen för att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa en grundläggande Logic-app som kontrollerar status för Azure på ett Tim schema. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

I den här snabb starten används en mall för att [**skapa en Logic-app**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) , som du hittar i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/resources/templates) , men är för lång för att visas här. I stället kan du granska snabb starts mallens ["azuredeploy.jspå fil"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) i galleriet mallar.

Snabb starts mal len skapar ett Logic app-arbetsflöde som använder upprepnings utlösaren, som är inställd på att köras varje timme och en [ *inbyggd*](../connectors/apis-list.md#connector-types)http-åtgärd, som anropar en URL som returnerar Azures status. En inbyggd åtgärd är inbyggd i Azure Logic Appss plattformen.

Den här mallen skapar följande Azure-resurs:

* [**Microsoft. Logic/arbets flöden**](/azure/templates/microsoft.logic/workflows), som skapar arbets flödet för en Logic app.

Om du vill ha fler snabb starts mallar för Azure Logic Apps granskar du [Microsoft. Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) -mallarna i galleriet.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Distribuera mallen

Följ det alternativ som du vill använda för att distribuera snabb starts mal len:

| Alternativ | Beskrivning |
|--------|-------------|
| [Azure-portalen](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Om din Azure-miljö uppfyller förutsättningarna och du är van att använda ARM-mallar, kan du använda de här stegen för att logga in direkt till Azure och öppna snabb starts mal len i Azure Portal. Mer information finns i [distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Azures kommando rads gränssnitt (Azure CLI) är en uppsättning kommandon för att skapa och hantera Azure-resurser. Om du vill köra de här kommandona behöver du Azure CLI version 2,6 eller senare. Om du vill kontrol lera CLI-versionen skriver du `az --version` . Mer information finns i de här ämnena: <p><p>- [Vad är Azure CLI](/cli/azure/what-is-azure-cli) <br>- [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Azure PowerShell tillhandahåller en uppsättning cmdletar som använder Azure Resource Manager-modellen för att hantera dina Azure-resurser. Mer information finns i de här ämnena: <p><p>- [Översikt över Azure PowerShell](/powershell/azure/azurerm/overview) <br>- [Introduktion till modulen Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) <br>- [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure Resource Management-REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Azure tillhandahåller en REST-API (Representational State Transfer) som är tjänst slut punkter som stöder HTTP-åtgärder (metoder) som du använder för att skapa, Hämta, uppdatera eller ta bort åtkomst till tjänst resurser. Mer information finns i [Kom igång med Azure REST API](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj följande bild för att logga in med ditt Azure-konto och öppna snabb starts mal len i Azure Portal:

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. I portalen, på sidan **skapa en logisk app med en mall** , anger eller väljer du följande värden:

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Namnet på den Azure-prenumeration som ska användas |
   | **Resursgrupp** | <*Azure-resurs-grupp-namn*> | Namnet på en ny eller befintlig Azure-resurs grupp. I det här exemplet används `Check-Azure-Status-RG`. |
   | **Region** | <*Azure-region*> | Azure Data Center-regionen att använda din Logic app. I det här exemplet används `West US`. |
   | **Namn på Logic app** | <*Logic – App-Name*> | Namnet som ska användas för din Logic app. I det här exemplet används `Check-Azure-Status-LA`. |
   | **Test-URI** | <*test-URI*> | URI för tjänsten som ska anropas baserat på ett angivet schema. Det här exemplet använder `https://status.azure.com/en-us/status/` , vilket är sidan Azure-status. |
   | **Plats** |  <*Azure-region – för-alla-resurser*> | Den Azure-region som ska användas för alla resurser, om den skiljer sig från standardvärdet. I det här exemplet används standardvärdet, `[resourceGroup().location]` som är resurs gruppens plats. |
   ||||

   Så här ser sidan ut med de värden som används i det här exemplet:

   ![Ange information för snabb starts mall](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. När du är klar väljer du **Granska + skapa**.

1. Fortsätt med stegen i [Granska distribuerade resurser](#review-deployed-resources).

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

* [Azure CLI: AZ distributions grupp](/cli/azure/deployment/group)
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

1. Om du inte vill använda en befintlig Azure-resurs grupp skapar du en ny resurs grupp genom att följa den här syntaxen för begäran som du skickar till resurs hanterings REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Värde | Beskrivning |
   |-------|-------------|
   | `subscriptionId`| GUID för den Azure-prenumeration som du vill använda |
   | `resourceGroupName` | Namnet på den Azure-resurs grupp som ska skapas. I det här exemplet används `Check-Azure-Status-RG`. |
   |||

   Exempel:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Mer information finns i de här ämnena:

   * [Referens för Azure-REST API – så här anropar du Azure REST-API: er](/rest/api/azure/)
   * [Resurs hanterings REST API: resurs grupper – skapa eller uppdatera](/rest/api/resources/resourcegroups/createorupdate).

1. Om du vill distribuera snabb starts mal len till resurs gruppen följer du den här syntaxen för begäran som du skickar till resurs hanterings REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Värde | Beskrivning |
   |-------|-------------|
   | `subscriptionId`| GUID för den Azure-prenumeration som du vill använda |
   | `resourceGroupName` | Namnet på den Azure-resurs grupp som ska användas. I det här exemplet används `Check-Azure-Status-RG`. |
   | `deploymentName` | Namnet som ska användas för din distribution. I det här exemplet används `Check-Azure-Status-LA`. |
   |||

   Exempel:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Mer information finns i [Resource Management REST API: distributioner-Create eller Update](/rest/api/resources/deployments/createorupdate).

1. Om du vill ange de värden som ska användas för distributionen, till exempel Azure-regionen och länkar till snabb starts mal len och [parameter filen](../azure-resource-manager/templates/template-parameters.md), som innehåller värdena för snabb starts mal len som ska användas vid distributionen, följer du den här syntaxen för begär ande texten som du skickar till resurs hanterings REST API:

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
   | `templateLink` : `uri` | <*snabb start – mall-URL*> | URL-platsen för snabb starts mal len som ska användas för distribution: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*snabb start – mall-parameter-File-URL*> | URL-platsen för snabb starts mal len parameter fil som ska användas för distribution: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Mer information om parameter filen för Resource Manager finns i följande avsnitt: <p><p>- [Skapa parameter fil för Resource Manager](../azure-resource-manager/templates/parameter-files.md) <br>- [Självstudie: använda parameter-filer för att distribuera ARM-mallen](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*distributions läge*> | Kör antingen en stegvis uppdatering eller fullständig uppdatering. Det här exemplet använder `Incremental` , vilket är standardvärdet. Mer information finns i [Azure Resource Manager distributions lägen](../azure-resource-manager/templates/deployment-modes.md). |
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

* [Resurs hanterings REST API](/rest/api/resources/)
* [Distribuera resurser med ARM-mallar och Resource Manager-REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Om du vill visa Logic-appen kan du använda Azure Portal, köra ett skript som du skapar med Azure CLI eller Azure PowerShell eller använda Logic app-REST API.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I sökrutan Azure Portal anger du namnet på din Logic Apps, som finns `Check-Azure-Status-LA` i det här exemplet. I listan resultat väljer du din Logic app.

1. I Azure Portal kan du söka efter och välja din Logic app, som är `Check-Azure-Status-RG` i det här exemplet.

1. När Logic App Designer öppnas granskar du den Logic-app som skapats av snabb starts mal len.

1. Om du vill testa Logic-appen går du till verktygsfältet i designern och väljer **Kör**.

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Mer information finns i [Azure CLI: AZ Logic Workflow show](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Mer information finns i [Azure PowerShell: get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Värde | Beskrivning |
|-------|-------------|
| `subscriptionId`| GUID för den Azure-prenumeration där du distribuerade snabb starts mal len. |
| `resourceGroupName` | Namnet på den Azure-resurs grupp där du distribuerade snabb starts mal len. I det här exemplet används `Check-Azure-Status-RG`. |
| `workflowName` | Namnet på den Logic-app som du har distribuerat. I det här exemplet används `Check-Azure-Status-LA`. |
|||

Exempel:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Mer information finns i [Logic Apps REST API: arbets flöden – Hämta](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande snabb starter och självstudier, kanske du vill behålla dessa resurser. När du inte längre behöver den logiska appen tar du bort resurs gruppen genom att använda antingen Azure Portal, Azure CLI, Azure PowerShell eller resurs hanterings REST API.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Portal letar du reda på och väljer den resurs grupp som du vill ta bort, vilket är `Check-Azure-Status-RG` i det här exemplet.

1. Välj **Översikt** på menyn resurs grupp om det inte redan är markerat. På sidan Översikt väljer du **ta bort resurs grupp**.

1. Bekräfta genom att ange namnet på resurs gruppen.

Mer information finns i [ta bort resurs grupp](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Mer information finns i [Azure CLI: AZ Group Delete](/cli/azure/group#az-group-delete).

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
| `subscriptionId`| GUID för den Azure-prenumeration där du distribuerade snabb starts mal len. |
| `resourceGroupName` | Namnet på den Azure-resurs grupp där du distribuerade snabb starts mal len. I det här exemplet används `Check-Azure-Status-RG`. |
|||

Exempel:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Mer information finns i [resurs hantering REST API: resurs grupper – ta bort](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
