---
title: Skapa resurs leverantör
description: Beskriver hur du skapar en resurs leverantör och distribuerar dess anpassade resurs typer.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 23ae69f49d37a210dd9fa1bfeedeca6c1b461e20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888869"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Snabb start: skapa en anpassad Provider och distribuera anpassade resurser

I den här snabb starten skapar du en egen resurs leverantör och distribuerar anpassade resurs typer för den resurs leverantören. Mer information om anpassade providers finns i [Översikt över Azure Custom providers Preview](overview.md).

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- För att slutföra stegen i den här snabb starten måste du anropa `REST` åtgärder. Det finns [olika sätt att skicka rest-begäranden](/rest/api/azure/).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI-exempel används `az rest` för `REST` förfrågningar. Mer information finns i [AZ rest](/cli/azure/reference-index#az-rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell-kommandona körs lokalt med PowerShell 7 eller senare och Azure PowerShell modulerna. Mer information finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).
- Om du inte redan har ett verktyg för `REST` åtgärder installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett kommando rads verktyg med öppen källkod som gör det enklare att anropa Azure Resource Manager API.
- När **ARMClient** har installerats kan du Visa användnings information från en PowerShell-kommandotolk genom att skriva: `armclient.exe` . Eller så går du till [ARMClient-wikin](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Distribuera anpassad Provider

Om du vill konfigurera den anpassade providern distribuerar du en [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) till din Azure-prenumeration.

När du har distribuerat mallen har prenumerationen följande resurser:

- Funktionsapp med åtgärder för resurserna och åtgärderna.
- Lagrings konto för att lagra användare som skapats via den anpassade providern.
- Anpassad provider som definierar anpassade resurs typer och åtgärder. Den använder Function-programmets slut punkt för att skicka begär Anden.
- Anpassad resurs från den anpassade providern.

Om du vill distribuera den anpassade providern använder du Azure CLI, PowerShell eller Azure Portal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

I det här exemplet blir du ombedd att ange en resurs grupp, plats och leverantörs funktions program namn. Namnen lagras i variabler som används i andra kommandon. [AZ-gruppen Skapa](/cli/azure/group#az-group-create) och [AZ distributions grupp skapa](/cli/azure/deployment/group#az-deployment-group-create) -kommandon distribuerar resurserna.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I det här exemplet blir du ombedd att ange en resurs grupp, plats och leverantörs funktions program namn. Namnen lagras i variabler som används i andra kommandon. Kommandona [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) och [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) distribuerar resurserna.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Du kan också distribuera lösningen från Azure Portal. Klicka på knappen **distribuera till Azure** för att öppna mallen i Azure Portal.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Visa anpassad Provider och resurs

Den anpassade providern är en dold resurs typ i portalen. För att bekräfta att resurs leverantören har distribuerats navigerar du till resurs gruppen. Välj alternativet för att **Visa dolda typer**.

![Visa dolda resurs typer](./media/create-custom-provider/show-hidden.png)

Om du vill se den anpassade resurs typ som du har distribuerat använder du `GET` åtgärden på resurs typen.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Du får svaret:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Du får svaret:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Anrops åtgärd

Den anpassade providern har också en åtgärd med namnet `ping` . Den kod som bearbetar begäran implementeras i Function-appen. `ping`Åtgärden svarar med en hälsning.

Om du vill skicka en `ping` begäran använder du `POST` åtgärden på den anpassade providern.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Du får svaret:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Du får svaret:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Skapa en resurs typ

Om du vill skapa en anpassad resurs typ kan du distribuera resursen i en mall. Den här metoden visas i mallen som du distribuerade i den här snabb starten. Du kan också skicka en `PUT` begäran om resurs typen.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Du får svaret:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Du får svaret:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Anpassade Resource Provider-kommandon

Använd de [anpassade providers-](/cli/azure/ext/custom-providers/custom-providers/resource-provider) kommandona för att arbeta med din anpassade resurs leverantör.

### <a name="list-custom-resource-providers"></a>Lista med anpassade resurs leverantörer

Använd `list` kommandot för att visa alla anpassade resurs leverantörer i en prenumeration. Standardvärdet visar den aktuella prenumerationens anpassade resurs leverantörer, eller så kan du ange `--subscription` parametern. Om du vill visa en resurs grupp använder du `--resource-group` parametern.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Visa egenskaperna

Använd `show` kommandot för att visa egenskaperna för den anpassade resurs leverantören. Utdataformatet liknar `list` utdata.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Skapa en ny resurs

Använd `create` kommandot för att skapa eller uppdatera en anpassad resurs leverantör. I det här exemplet uppdateras `actions` och `resourceTypes` .

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Uppdatera providerns Taggar

`update`Kommandot uppdaterar endast taggar för en anpassad resurs leverantör. I Azure Portal visar den anpassade resurs leverantörens App Service taggen.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Ta bort en anpassad resurs leverantör

`delete`Kommandot efterfrågar och tar bara bort den anpassade resurs leverantören. Lagrings kontot, app service och App Service-planen tas inte bort. När providern har tagits bort returneras en kommando tolk.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Nästa steg

En introduktion till anpassade leverantörer finns i följande artikel:

> [!div class="nextstepaction"]
> [Översikt över Azure Custom providers Preview](overview.md)
