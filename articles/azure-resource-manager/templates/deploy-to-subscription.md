---
title: Distribuera resurser till prenumerationen
description: Beskriver hur du skapar en resursgrupp i en Azure Resource Manager mall. Den visar också hur du distribuerar resurser i Azure-prenumerationsomfånget.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 3598fe290fd993cbbc662ba9d3a3c5ba8c207bc0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781927"
---
# <a name="subscription-deployments-with-arm-templates"></a>Prenumerationsdistributioner med ARM-mallar

För att förenkla hanteringen av resurser kan du använda en Azure Resource Manager (ARM-mall) för att distribuera resurser på nivån för din Azure-prenumeration. Du kan till exempel distribuera [principer](../../governance/policy/overview.md) och rollbaserad åtkomstkontroll i [Azure (Azure RBAC)](../../role-based-access-control/overview.md) till din prenumeration, vilket tillämpar dem i din prenumeration. Du kan också skapa resursgrupper i prenumerationen och distribuera resurser till resursgrupper i prenumerationen.

> [!NOTE]
> Du kan distribuera till 800 olika resursgrupper i en distribution på prenumerationsnivå.

Om du vill distribuera mallar på prenumerationsnivå använder du Azure CLI, PowerShell, REST API eller portalen.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurstyper kan inte distribueras till prenumerationsnivån. I det här avsnittet visas vilka resurstyper som stöds.

För Azure Blueprints använder du:

* [Artefakter](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Ritningar](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versioner (skisser)](/azure/templates/microsoft.blueprint/blueprints/versions)

För Azure-principer använder du:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [åtgärder](/azure/templates/microsoft.policyinsights/remediations)

För rollbaserad åtkomstkontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

För kapslade mallar som distribueras till resursgrupper använder du:

* [Distributioner](/azure/templates/microsoft.resources/deployments)

Om du vill skapa nya resursgrupper använder du:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Använd följande för att hantera din prenumeration:

* [Advisor-konfigurationer](/azure/templates/microsoft.advisor/configurations)
* [Budgetar](/azure/templates/microsoft.consumption/budgets)
* [Ändringsanalys profil](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Taggar](/azure/templates/microsoft.resources/tags)

Andra typer som stöds är:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Schemat som du använder för distributioner på prenumerationsnivå skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    ...
}
```

Schemat för en parameterfil är detsamma för alla distributionsomfång. För parameterfiler använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Distributionskommandon

Om du vill distribuera till en prenumeration använder du distributionskommandona på prenumerationsnivå.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create). I följande exempel distribueras en mall för att skapa en resursgrupp:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell-distributionskommandot använder [du New-AzDeployment](/powershell/module/az.resources/new-azdeployment) eller dess alias `New-AzSubscriptionDeployment` . I följande exempel distribueras en mall för att skapa en resursgrupp:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Mer detaljerad information om distributionskommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributionsknapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Distributionsplats och namn

För distributioner på prenumerationsnivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras. [Distributioner av](deploy-to-management-group.md) [hanteringsgrupp](deploy-to-tenant.md) och klientorganisation kräver också en plats. För [resursgruppsdistributioner](deploy-to-resource-group.md) används platsen för resursgruppen för att lagra distributionsdata.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en _mallazuredeploy.jspå_ skapas standarddistributionsnamnet **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en prenumerationsdistribution med namnet **deployment1** i **usa,** kan du inte senare skapa en annan distribution med namnet **deployment1** utan platsen **westus**. Om du får felkoden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributionsomfång

När du distribuerar till en prenumeration kan du distribuera resurser till:

* målprenumerationen från åtgärden
* valfri prenumeration i klientorganisationen
* resursgrupper i prenumerationen eller andra prenumerationer
* klienten för prenumerationen

En [tilläggsresurs](scope-extension-resources.md) kan vara begränsad till ett mål som skiljer sig från distributionsmålet.

Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Det här avsnittet visar hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-subscription"></a>Omfång för målprenumeration

Om du vill distribuera resurser till målprenumerationen lägger du till resurserna i resursavsnittet i mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Exempel på distribution till prenumerationen finns i Skapa [resursgrupper och](#create-resource-groups) Tilldela [principdefinition.](#assign-policy-definition)

### <a name="scope-to-other-subscription"></a>Omfång till annan prenumeration

Om du vill distribuera resurser till en prenumeration som skiljer sig från prenumerationen från åtgärden lägger du till en kapslad distribution. Ange egenskapen `subscriptionId` till ID:t för den prenumeration som du vill distribuera till. Ange egenskapen `location` för den kapslade distributionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Omfång till resursgrupp

Om du vill distribuera resurser till en resursgrupp i prenumerationen lägger du till en kapslad distribution och inkluderar `resourceGroup` egenskapen . I följande exempel är den kapslade distributionen inriktad på en resursgrupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Ett exempel på hur du distribuerar till en resursgrupp finns [i Skapa resursgrupp och resurser](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Omfång till klientorganisation

Om du vill skapa resurser i klientorganisationen anger du `scope` till `/` . Den användare som distribuerar mallen måste ha nödvändig [åtkomst för att distribuera på klientorganisationen](deploy-to-tenant.md#required-access).

Om du vill använda en kapslad distribution anger du `scope` och `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Eller så kan du ange omfånget till `/` för vissa resurstyper, till exempel hanteringsgrupper.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Mer information finns i [Hanteringsgrupp.](deploy-to-management-group.md#management-group)

## <a name="resource-groups"></a>Resursgrupper

### <a name="create-resource-groups"></a>Skapa resursgrupper

Om du vill skapa en resursgrupp i en ARM-mall definierar du en [Microsoft.Resources/resourceGroups-resurs](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resursgruppen.

Följande mall skapar en tom resursgrupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Använd [kopieringselementet](copy-resources.md) med resursgrupper för att skapa fler än en resursgrupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Information om resurs iteration finns i Resurs [iteration](./copy-resources.md)i ARM-mallar och [Självstudie: Skapa flera resursinstanser med ARM-mallar.](./template-tutorial-create-multiple-instances.md)

### <a name="create-resource-group-and-resources"></a>Skapa resursgrupp och resurser

Om du vill skapa resursgruppen och distribuera resurser till den använder du en kapslad mall. Den kapslade mallen definierar de resurser som ska distribueras till resursgruppen. Ange den kapslade mallen som beroende av resursgruppen för att se till att resursgruppen finns innan du distribuerar resurserna. Du kan distribuera till upp till 800 resursgrupper.

I följande exempel skapas en resursgrupp och ett lagringskonto distribueras till resursgruppen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Tilldela principdefinition

I följande exempel tilldelas en befintlig principdefinition till prenumerationen. Om principdefinitionen tar parametrar anger du dem som ett -objekt. Om principdefinitionen inte tar parametrar använder du det tomma standardobjektet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Om du vill distribuera den här mallen med Azure CLI använder du:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Skapa och tilldela principdefinitioner

Du kan [definiera](../../governance/policy/concepts/definition-structure.md) och tilldela en principdefinition i samma mall.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Om du vill skapa principdefinitionen i din prenumeration och tilldela den till prenumerationen använder du följande CLI-kommando:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Skapa skissdefinition

Du kan [skapa en](../../governance/blueprints/tutorials/create-from-sample.md) skissdefinition från en mall.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Använd följande CLI-kommando för att skapa skissdefinitionen i din prenumeration:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Åtkomstkontroll

Mer information om hur du tilldelar roller finns i [Lägga till Azure-rolltilldelningar med hjälp Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).

I följande exempel skapas en resursgrupp, ett lås tillämpas på den och en roll tilldelas till ett huvudnamn.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du distribuerar arbetsyteinställningar för Azure Security Center finns [ ideployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Exempelmallar finns på [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)
* Du kan också distribuera mallar på [hanteringsgruppsnivå och](deploy-to-management-group.md) [klientorganisationsnivå.](deploy-to-tenant.md)
