---
title: Distribuera resurser till hanteringsgrupp
description: Beskriver hur du distribuerar resurser i hanteringsgruppsomfånget i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781945"
---
# <a name="management-group-deployments-with-arm-templates"></a>Distributioner av hanteringsgrupp med ARM-mallar

När din organisation mognar kan du distribuera en Azure Resource Manager (ARM-mall) för att skapa resurser på hanteringsgruppsnivå. Du kan till exempel behöva [](../../governance/policy/overview.md) definiera och tilldela principer eller rollbaserad åtkomstkontroll i [Azure (Azure RBAC)](../../role-based-access-control/overview.md) för en hanteringsgrupp. Med mallar på hanteringsgruppsnivå kan du deklarativt tillämpa principer och tilldela roller på hanteringsgruppsnivå.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurstyper kan inte distribueras till hanteringsgruppsnivån. I det här avsnittet visas vilka resurstyper som stöds.

För Azure Blueprints använder du:

* [Artefakter](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Ritningar](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Versioner](/azure/templates/microsoft.blueprint/blueprints/versions)

För Azure Policy använder du:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [åtgärder](/azure/templates/microsoft.policyinsights/remediations)

För rollbaserad åtkomstkontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

För kapslade mallar som distribueras till prenumerationer eller resursgrupper använder du:

* [Distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att hantera dina resurser:

* [Taggar](/azure/templates/microsoft.resources/tags)

Hanteringsgrupper är resurser på klientorganisationsnivå. Du kan dock skapa hanteringsgrupper i en hanteringsgruppsdistribution genom att ange omfånget för den nya hanteringsgruppen till klienten. Se [Hanteringsgrupp.](#management-group)

## <a name="schema"></a>Schema

Schemat som du använder för distributioner av hanteringsgrupp skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

Om du vill distribuera till en hanteringsgrupp använder du distributionskommandona för hanteringsgruppen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För Azure PowerShell du [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
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

För distributioner på hanteringsgruppsnivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras. [Prenumerations-](deploy-to-subscription.md) [och](deploy-to-tenant.md) klientdistributioner kräver också en plats. För [resursgruppsdistributioner](deploy-to-resource-group.md) används platsen för resursgruppen för att lagra distributionsdata.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en _mallazuredeploy.jspå_ skapas standarddistributionsnamnet **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en hanteringsgruppsdistribution med namnet **deployment1** i **usa,** kan du inte senare skapa en annan distribution med namnet **deployment1** utan platsen **westus**. Om du får felkoden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributionsomfång

När du distribuerar till en hanteringsgrupp kan du distribuera resurser till:

* målhanteringsgruppen från åtgärden
* en annan hanteringsgrupp i klientorganisationen
* prenumerationer i hanteringsgruppen
* resursgrupper i hanteringsgruppen
* klientorganisationen för resursgruppen

En [tilläggsresurs](scope-extension-resources.md) kan vara begränsad till ett mål som skiljer sig från distributionsmålet.

Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Det här avsnittet visar hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-management-group"></a>Omfång till målhanteringsgrupp

Resurser som definierats i resursavsnittet i mallen tillämpas på hanteringsgruppen från distributionskommandot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Omfång till en annan hanteringsgrupp

Om du vill rikta in dig på en annan hanteringsgrupp lägger du till en kapslad distribution och anger `scope` egenskapen . Ange egenskapen `scope` till ett värde i formatet `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Omfång till prenumeration

Du kan också rikta prenumerationer inom en hanteringsgrupp. Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en prenumeration i hanteringsgruppen använder du en kapslad distribution och `subscriptionId` egenskapen .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Omfång till resursgrupp

Du kan också rikta in dig på resursgrupper i hanteringsgruppen. Den användare som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta in dig på en resursgrupp i hanteringsgruppen använder du en kapslad distribution. Ange egenskaperna `subscriptionId` `resourceGroup` och . Ange inte en plats för den kapslade distributionen eftersom den distribueras på resursgruppens plats.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Om du vill använda en distribution av hanteringsgrupp för att skapa en resursgrupp i en prenumeration och distribuera ett lagringskonto till den resursgruppen kan du läsa Distribuera till prenumeration [och resursgrupp.](#deploy-to-subscription-and-resource-group)

### <a name="scope-to-tenant"></a>Omfång för klientorganisation

Om du vill skapa resurser i klientorganisationen anger du `scope` till `/` . Den användare som distribuerar mallen måste ha nödvändig [åtkomst för att distribuera på klientorganisationen](deploy-to-tenant.md#required-access).

Om du vill använda en kapslad distribution anger du `scope` och `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Eller så kan du ange omfånget till `/` för vissa resurstyper, till exempel hanteringsgrupper. Att skapa en ny hanteringsgrupp beskrivs i nästa avsnitt.

## <a name="management-group"></a>Hanteringsgrupp

Om du vill skapa en hanteringsgrupp i en distribution av en hanteringsgrupp måste du ange `/` omfånget till för hanteringsgruppen.

I följande exempel skapas en ny hanteringsgrupp i rothanteringsgruppen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

I nästa exempel skapas en ny hanteringsgrupp i hanteringsgruppen som anges som överordnad. Observera att omfånget är inställt på `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Prenumerationer

Om du vill använda en ARM-mall för att skapa en ny Azure-prenumeration i en hanteringsgrupp kan du läsa:

* [Skapa Azure Enterprise-avtalsprenumerationer programmatiskt](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Skapa Azure-prenumerationer programmatiskt för en Microsoft-kundavtal](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Skapa Azure-prenumerationer programmatiskt för en Microsoft-partneravtal](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Information om hur du distribuerar en mall som flyttar en befintlig Azure-prenumeration till en ny hanteringsgrupp finns i [Flytta prenumerationer i ARM-mall](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Anpassade principdefinitioner som distribueras till hanteringsgruppen är tillägg för hanteringsgruppen. Om du vill hämta ID:t för en anpassad principdefinition använder [du funktionen extensionResourceId().](template-functions-resource.md#extensionresourceid) Inbyggda principdefinitioner är resurser på klientorganisationsnivå. Om du vill hämta ID:t för en inbyggd principdefinition använder du [funktionen tenantResourceId().](template-functions-resource.md#tenantresourceid)

I följande exempel visas hur du [definierar](../../governance/policy/concepts/definition-structure.md) en princip på hanteringsgruppsnivå och tilldelar den.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuera till prenumeration och resursgrupp

Från en distribution på hanteringsgruppsnivå kan du rikta in dig på en prenumeration i hanteringsgruppen. I följande exempel skapas en resursgrupp i en prenumeration och ett lagringskonto distribueras till den resursgruppen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar roller finns i [Lägga till Azure-rolltilldelningar med hjälp Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på hur du distribuerar arbetsyteinställningar för Azure Security Center finns [ ideployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du kan också distribuera mallar på [prenumerationsnivå och](deploy-to-subscription.md) [klientorganisationsnivå.](deploy-to-tenant.md)
