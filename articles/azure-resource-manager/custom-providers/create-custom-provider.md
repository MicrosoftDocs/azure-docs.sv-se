---
title: Skapa resursprovider
description: Beskriver hur du skapar en resursprovider och distribuerar dess anpassade resurstyper.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 721fe31f514cb948c93dc2a779a92ef3b2775ef9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779245"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Snabbstart: Skapa en anpassad provider och distribuera anpassade resurser

I den här snabbstarten skapar du en egen resursprovider och distribuerar anpassade resurstyper för den resursprovidern. Mer information om anpassade providers finns i [Översikt över förhandsversionen av Azure Custom Providers.](overview.md)

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- För att slutföra stegen i den här snabbstarten måste du anropa `REST` åtgärder. Det finns [olika sätt att skicka REST-begäranden.](/rest/api/azure/)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI-exempel använder `az rest` för `REST` begäranden. Mer information finns i [az rest](/cli/azure/reference-index#az_rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell-kommandona körs lokalt med PowerShell 7 eller senare och de Azure PowerShell modulerna. Mer information finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).
- Om du inte redan har ett verktyg för `REST` åtgärder installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett kommandoradsverktyg med öppen källkod som gör det enklare att anropa Azure Resource Manager API.
- När **ARMClient har installerats** kan du visa användningsinformation från en PowerShell-kommandotolk genom att skriva: `armclient.exe` . Eller gå till [ARMClient wiki.](https://github.com/projectkudu/ARMClient/wiki)

---

## <a name="deploy-custom-provider"></a>Distribuera anpassad provider

Om du vill konfigurera den anpassade providern distribuerar du en [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) till din Azure-prenumeration.

När du har distribuerat mallen har prenumerationen följande resurser:

- Funktionsapp med åtgärder för resurser och åtgärder.
- Lagringskonto för lagring av användare som skapas via den anpassade providern.
- Anpassad provider som definierar anpassade resurstyper och åtgärder. Den använder funktionsappens slutpunkt för att skicka begäranden.
- Anpassad resurs från den anpassade providern.

Om du vill distribuera den anpassade providern använder du Azure CLI, PowerShell eller Azure Portal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

I det här exemplet uppmanas du att ange en resursgrupp, plats och providerns funktionsappnamn. Namnen lagras i variabler som används i andra kommandon. Kommandona [az group create](/cli/azure/group#az_group_create) och az deployment group [create](/cli/azure/deployment/group#az_deployment_group_create) distribuerar resurserna.

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

I det här exemplet uppmanas du att ange en resursgrupp, plats och providerns funktionsappnamn. Namnen lagras i variabler som används i andra kommandon. Kommandona [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) [och New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) distribuerar resurserna.

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

Du kan också distribuera lösningen från Azure Portal. Välj knappen **Distribuera till Azure** för att öppna mallen i Azure Portal.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Visa anpassad provider och resurs

I portalen är den anpassade providern en dold resurstyp. Bekräfta att resursprovidern har distribuerats genom att gå till resursgruppen. Välj alternativet visa **dolda typer.**

![Visa dolda resurstyper](./media/create-custom-provider/show-hidden.png)

Om du vill se den anpassade resurstyp som du har distribuerat använder `GET` du åtgärden på din resurstyp.

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

## <a name="call-action"></a>Anropa åtgärd

Din anpassade provider har också en åtgärd som heter `ping` . Koden som bearbetar begäran implementeras i funktionsappen. Åtgärden `ping` svarar med en hälsning.

Om du vill `ping` skicka en begäran använder du åtgärden på din anpassade `POST` provider.

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

## <a name="create-a-resource-type"></a>Skapa en resurstyp

Om du vill skapa den anpassade resurstypen kan du distribuera resursen i en mall. Den här metoden visas i mallen som du distribuerade i den här snabbstarten. Du kan också skicka en `PUT` begäran om resurstypen.

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

## <a name="custom-resource-provider-commands"></a>Anpassade resursproviderkommandon

Använd kommandon [för anpassade providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) för att arbeta med din anpassade resursprovider.

### <a name="list-custom-resource-providers"></a>Lista anpassade resursproviders

Använd kommandot `list` för att visa alla anpassade resursproviders i en prenumeration. Standardvärdet visar den aktuella prenumerationens anpassade resursproviders, eller så kan du ange `--subscription` parametern . Om du vill visa en lista över en resursgrupp använder du `--resource-group` parametern .

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

Använd kommandot `show` för att visa egenskaperna för den anpassade resursprovidern. Utdataformatet liknar `list` utdata.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Skapa en ny resurs

Använd kommandot `create` för att skapa eller uppdatera en anpassad resursprovider. I det här exemplet uppdateras `actions` och `resourceTypes` .

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

### <a name="update-the-providers-tags"></a>Uppdatera providerns taggar

Kommandot `update` uppdaterar endast taggar för en anpassad resursprovider. I Azure Portal visar den anpassade resursproviderns apptjänst taggen .

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Ta bort en anpassad resursprovider

Kommandot `delete` frågar dig och tar bara bort den anpassade resursprovidern. Lagringskontot, App Service och App Service-planen tas inte bort. När providern har tagits bort returneras du till en kommandotolk.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Nästa steg

En introduktion till anpassade providers finns i följande artikel:

> [!div class="nextstepaction"]
> [Översikt över förhandsversionen av anpassade Azure-providers](overview.md)
