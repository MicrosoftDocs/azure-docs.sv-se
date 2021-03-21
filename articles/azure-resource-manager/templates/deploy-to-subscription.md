---
title: Distribuera resurser till prenumerationen
description: Beskriver hur du skapar en resurs grupp i en Azure Resource Manager-mall. Det visar också hur du distribuerar resurser i Azures prenumerations omfång.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: b5c99e5dc21c2b93f1c9da3977302a2dd311277f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491511"
---
# <a name="subscription-deployments-with-arm-templates"></a>Prenumerations distribution med ARM-mallar

För att förenkla hanteringen av resurser kan du använda en Azure Resource Manager mall (ARM-mall) för att distribuera resurser på nivån i din Azure-prenumeration. Du kan t. ex. distribuera [principer](../../governance/policy/overview.md) och [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) till din prenumeration, som tillämpar dem i din prenumeration. Du kan också skapa resurs grupper i prenumerationen och distribuera resurser till resurs grupper i prenumerationen.

> [!NOTE]
> Du kan distribuera till 800 olika resurs grupper i en distribution på prenumerations nivå.

Om du vill distribuera mallar på prenumerations nivån använder du Azure CLI, PowerShell, REST API eller portalen.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurs typer kan inte distribueras till prenumerations nivån. I det här avsnittet listas vilka resurs typer som stöds.

För Azure-ritningar använder du:

* [artefakter](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [modeller](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versioner (ritningar)](/azure/templates/microsoft.blueprint/blueprints/versions)

Använd följande för Azure-principer:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [reparationer](/azure/templates/microsoft.policyinsights/remediations)

För rollbaserad åtkomst kontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

För kapslade mallar som distribuerar till resurs grupper använder du:

* [distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att skapa nya resurs grupper:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Använd följande för att hantera din prenumeration:

* [Advisor-konfigurationer](/azure/templates/microsoft.advisor/configurations)
* [budget](/azure/templates/microsoft.consumption/budgets)
* [Ändra analys profil](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [taggen](/azure/templates/microsoft.resources/tags)

Andra typer som stöds är:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Schemat som används för distributioner på prenumerations nivå skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    ...
}
```

Schemat för en parameter fil är detsamma för alla distributions omfång. För parameter-filer använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Distributions kommandon

Använd distributions kommandon på prenumerations nivå för att distribuera till en prenumeration.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create). I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För kommandot PowerShell-distribution använder du [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) eller dess alias `New-AzSubscriptionDeployment` . I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Mer detaljerad information om distributions kommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på prenumerations nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras. [Hanterings grupp](deploy-to-management-group.md) och [klient](deploy-to-tenant.md) distributioner kräver också en plats. För [resurs grupps](deploy-to-resource-group.md) distributioner används resurs gruppens plats för att lagra distributions data.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter _azuredeploy.jspå_ skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en prenumerations distribution med namnet **deployment1** i **centralen** kan du inte senare skapa en annan distribution med namnet **deployment1** men en plats med **väst**. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en prenumeration kan du distribuera resurser för att:

* mål prenumerationen från åtgärden
* alla prenumerationer i klient organisationen
* resurs grupper inom prenumerationen eller andra prenumerationer
* Prenumerationens klient organisation

En [tilläggs resurs](scope-extension-resources.md) kan begränsas till ett mål som skiljer sig från distributions målet.

Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

I det här avsnittet visas hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-subscription"></a>Omfång till mål prenumeration

Om du vill distribuera resurser till mål prenumerationen lägger du till resurserna i avsnittet resurser i mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Exempel på hur du distribuerar till prenumerationen finns i [skapa resurs grupper](#create-resource-groups) och [tilldela princip definition](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Omfång till annan prenumeration

Om du vill distribuera resurser till en annan prenumeration än prenumerationen från åtgärden lägger du till en kapslad distribution. Ange `subscriptionId` egenskapen till ID: t för den prenumeration som du vill distribuera till. Ange `location` egenskapen för den kapslade distributionen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Omfång till resurs grupp

Om du vill distribuera resurser till en resurs grupp i prenumerationen lägger du till en kapslad distribution och inkluderar `resourceGroup` egenskapen. I följande exempel riktar den kapslade distributionen till en resurs grupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Ett exempel på hur du distribuerar till en resurs grupp finns i [skapa resurs grupper och resurser](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Scope till klient organisation

Ange till för att skapa resurser hos klienten `scope` `/` . Användaren som distribuerar mallen måste ha den [åtkomst som krävs för att distribuera på klienten](deploy-to-tenant.md#required-access).

Om du vill använda en kapslad distribution anger du `scope` och `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Eller så kan du ange omfånget till `/` för vissa resurs typer, t. ex. hanterings grupper.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Mer information finns i [hanterings grupp](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Resursgrupper

### <a name="create-resource-groups"></a>Skapa resurs grupper

Om du vill skapa en resurs grupp i en ARM-mall definierar du en resurs för [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resurs gruppen.

Följande mall skapar en tom resurs grupp.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Använd [Kopiera-elementet](copy-resources.md) med resurs grupper för att skapa fler än en resurs grupp.

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
      "apiVersion": "2020-06-01",
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

Information om resurs upprepning finns [i resurs upprepning i arm-mallar](./copy-resources.md)och [självstudie: skapa flera resurs instanser med ARM-mallar](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Skapa resurs grupp och resurser

Använd en kapslad mall för att skapa resurs gruppen och distribuera resurser till den. Den kapslade mallen definierar de resurser som ska distribueras till resurs gruppen. Ange att den kapslade mallen är beroende av resurs gruppen för att kontrol lera att resurs gruppen finns innan du distribuerar resurserna. Du kan distribuera till upp till 800 resurs grupper.

I följande exempel skapas en resurs grupp och ett lagrings konto distribueras till resurs gruppen.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
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

### <a name="assign-policy-definition"></a>Tilldela princip definition

I följande exempel tilldelas en befintlig princip definition till prenumerationen. Om princip definitionen tar parametrar, anger du dem som ett objekt. Om princip definitionen inte tar parametrar använder du det tomma standard objektet.

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

Använd följande om du vill distribuera den här mallen med PowerShell:

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

### <a name="create-and-assign-policy-definitions"></a>Skapa och tilldela princip definitioner

Du kan [definiera](../../governance/policy/concepts/definition-structure.md) och tilldela en princip definition i samma mall.

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

Använd följande CLI-kommando om du vill skapa en princip definition i din prenumeration och tilldela den till prenumerationen:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Skapa skiss definition

Du kan [skapa](../../governance/blueprints/tutorials/create-from-sample.md) en skiss definition från en mall.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Använd följande CLI-kommando om du vill skapa en skiss definition i din prenumeration:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Åtkomstkontroll

Information om hur du tilldelar roller finns i [lägga till Azure-roll tilldelningar med hjälp av Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).

I följande exempel skapas en resurs grupp, ett lås till den och tilldelas en roll till ett huvud konto.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du hittar exempel på mallar på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Du kan också distribuera mallar på [hanterings grupp nivå](deploy-to-management-group.md) och [klient nivå](deploy-to-tenant.md).
