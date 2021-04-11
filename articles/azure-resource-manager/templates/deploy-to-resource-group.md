---
title: Distribuera resurser till resurs grupper
description: Beskriver hur du distribuerar resurser i en Azure Resource Manager mall. Det visar hur du riktar in mer än en resurs grupp.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 1d636be9ffab5a4398e3e12867e601ce6df382bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889798"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Resurs grupps distributioner med ARM-mallar

Den här artikeln beskriver hur du omfångerar distributionen till en resurs grupp. Du använder en Azure Resource Manager-mall (ARM-mall) för distributionen. Artikeln visar också hur du kan utöka omfattningen bortom resurs gruppen i distributions åtgärden.

## <a name="supported-resources"></a>Resurser som stöds

De flesta resurser kan distribueras till en resurs grupp. En lista över tillgängliga resurser finns i [referens för ARM-mallar](/azure/templates).

## <a name="schema"></a>Schema

För mallar använder du följande schema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

För parameter-filer använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Distributions kommandon

Använd resurs grupps distributions kommandon för att distribuera till en resurs grupp.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [AZ distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create)för Azure CLI. I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För kommandot PowerShell-distribution använder du [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Mer detaljerad information om distributions kommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en resurs grupp kan du distribuera resurser för att:

* mål resurs gruppen från åtgärden
* andra resurs grupper i samma prenumeration eller andra prenumerationer
* alla prenumerationer i klient organisationen
* innehavaren för resurs gruppen

En [tilläggs resurs](scope-extension-resources.md) kan begränsas till ett mål som skiljer sig från distributions målet.

Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

I det här avsnittet visas hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-resource-group"></a>Omfång till mål resurs grupp

Om du vill distribuera resurser till mål resursen lägger du till resurserna i avsnittet resurser i mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

En exempel-mall finns i [distribuera till mål resurs grupp](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Omfånget till resurs gruppen i samma prenumeration

Om du vill distribuera resurser till en annan resurs grupp i samma prenumeration lägger du till en kapslad distribution och inkluderar `resourceGroup` egenskapen. Om du inte anger prenumerations-ID eller resurs grupp används prenumerationen och resurs gruppen från den överordnade mallen. Alla resurs grupper måste finnas innan du kör distributionen.

I följande exempel riktar den kapslade distributionen till en resurs grupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

En exempel-mall finns i [distribuera till flera resurs grupper](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Omfånget till resurs gruppen i en annan prenumeration

Om du vill distribuera resurser till en resurs grupp i en annan prenumeration lägger du till en kapslad distribution och inkluderar- `subscriptionId` och- `resourceGroup` egenskaperna. I följande exempel riktar den kapslade distributionen till en resurs grupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

En exempel-mall finns i [distribuera till flera resurs grupper](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Omfång till prenumeration

Om du vill distribuera resurser till en prenumeration lägger du till en kapslad distribution och inkluderar `subscriptionId` egenskapen. Prenumerationen kan vara prenumerationen för mål resurs gruppen eller någon annan prenumeration i klienten. Ange också `location` egenskapen för den kapslade distributionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

En exempel-mall finns i [skapa resurs grupp](#create-resource-group).

### <a name="scope-to-tenant"></a>Scope till klient organisation

Ange till för att skapa resurser hos klienten `scope` `/` . Användaren som distribuerar mallen måste ha den [åtkomst som krävs för att distribuera på klienten](deploy-to-tenant.md#required-access).

Om du vill använda en kapslad distribution anger du `scope` och `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Eller så kan du ange omfånget till `/` för vissa resurs typer, t. ex. hanterings grupper.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Mer information finns i [hanterings grupp](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Distribuera till mål resurs grupp

För att distribuera resurser i mål resurs gruppen definierar du resurserna i `resources` avsnittet i mallen. Följande mall skapar ett lagrings konto i resurs gruppen som anges i distributions åtgärden.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Distribuera till flera resurs grupper

Du kan distribuera till fler än en resurs grupp i en enda ARM-mall. Använd en [kapslad eller länkad mall](linked-templates.md)om du vill rikta en resurs grupp från en annan resurs grupp än den för den överordnade mallen. I resurs typen distribution anger du värden för det prenumerations-ID och den resurs grupp som du vill att den kapslade mallen ska distribuera till. Resurs grupperna kan finnas i olika prenumerationer.

> [!NOTE]
> Du kan distribuera till **800 resurs grupper** i en enda distribution. Den här begränsningen innebär vanligt vis att du kan distribuera till en resurs grupp som angetts för den överordnade mallen och upp till 799 resurs grupper i kapslade eller länkade distributioner. Men om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till 800 resurs grupper i kapslade eller länkade distributioner.

I följande exempel distribueras två lagrings konton. Det första lagrings kontot distribueras till den resurs grupp som anges i distributions åtgärden. Det andra lagrings kontot distribueras till resurs gruppen som anges i `secondResourceGroup` parametrarna och `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Om du anger `resourceGroup` namnet på en resurs grupp som inte finns, Miss lyckas distributionen.

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration** använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Om du vill distribuera två lagrings konton till **två prenumerationer** använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration** använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Om du vill distribuera två lagrings konton till **två prenumerationer** använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Skapa resursgrupp

Från en resurs grupps distribution kan du växla till en prenumerations nivå och skapa en resurs grupp. Följande mall distribuerar ett lagrings konto till mål resurs gruppen och skapar en ny resurs grupp i den angivna prenumerationen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
