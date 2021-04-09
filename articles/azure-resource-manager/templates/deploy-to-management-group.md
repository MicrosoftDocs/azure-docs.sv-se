---
title: Distribuera resurser till hanterings grupp
description: Beskriver hur du distribuerar resurser i hanterings gruppens omfattning i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dc7418d9e93fb50590c5e2502b3a3ffb3847273f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043316"
---
# <a name="management-group-deployments-with-arm-templates"></a>Distributioner av hanterings grupper med ARM-mallar

När din organisation är vuxen kan du distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa resurser på hanterings grupps nivå. Du kan till exempel behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) för en hanterings grupp. Med mallar för hanterings grupps nivåer kan du tillämpa principer och tilldela roller på hanterings grupps nivån.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurs typer kan inte distribueras till hanterings grupps nivån. I det här avsnittet listas vilka resurs typer som stöds.

För Azure-ritningar använder du:

* [artefakter](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [modeller](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [5](/azure/templates/microsoft.blueprint/blueprints/versions)

För Azure Policy använder du:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [reparationer](/azure/templates/microsoft.policyinsights/remediations)

För rollbaserad åtkomst kontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

För kapslade mallar som distribuerar till prenumerationer eller resurs grupper använder du:

* [distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att hantera resurser:

* [taggen](/azure/templates/microsoft.resources/tags)

Hanterings grupper är resurser på klient organisations nivå. Du kan dock skapa hanterings grupper i en distribution av hanterings grupper genom att ange omfånget för den nya hanterings gruppen till klienten. Se [hanterings grupp](#management-group).

## <a name="schema"></a>Schema

Schemat som används för distributioner av hanterings grupper skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

Om du vill distribuera till en hanterings grupp använder du distributions kommandona för hanterings gruppen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För Azure CLI använder du [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För Azure PowerShell använder du [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
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

För distributioner på hanterings grupp nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras. [Prenumerations](deploy-to-subscription.md) -och [klient](deploy-to-tenant.md) distributioner kräver också en plats. För [resurs grupps](deploy-to-resource-group.md) distributioner används resurs gruppens plats för att lagra distributions data.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter _azuredeploy.jspå_ skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du till exempel skapar en distribution av en hanterings grupp med namnet **deployment1** i **centrala** kan du inte senare skapa en annan distribution med namnet **deployment1** men en plats med **väst**. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en hanterings grupp kan du distribuera resurser för att:

* mål hanterings gruppen från åtgärden
* en annan hanterings grupp i klienten
* prenumerationer i hanterings gruppen
* resurs grupper i hanterings gruppen
* innehavaren för resurs gruppen

En [tilläggs resurs](scope-extension-resources.md) kan begränsas till ett mål som skiljer sig från distributions målet.

Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

I det här avsnittet visas hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-management-group"></a>Omfång till mål hanterings grupp

De resurser som definieras i avsnittet resurser i mallen tillämpas på hanterings gruppen från distributions kommandot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Omfång till en annan hanterings grupp

Om du vill rikta en annan hanterings grupp lägger du till en kapslad distribution och anger `scope` egenskapen. Ange `scope` ett värde i formatet för egenskapen `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Omfång till prenumeration

Du kan också rikta prenumerationer i en hanterings grupp. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en prenumeration inom hanterings gruppen använder du en kapslad distribution och `subscriptionId` egenskapen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Omfång till resurs grupp

Du kan också rikta resurs grupper i hanterings gruppen. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en resurs grupp i hanterings gruppen använder du en kapslad distribution. Ange `subscriptionId` egenskaperna och `resourceGroup` . Ange ingen plats för den kapslade distributionen eftersom den har distribuerats i resurs gruppens plats.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Om du vill använda en distribution av hanterings grupper för att skapa en resurs grupp i en prenumeration och distribuera ett lagrings konto till den resurs gruppen, se [distribuera till prenumeration och resurs grupp](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Scope till klient organisation

Ange till för att skapa resurser hos klienten `scope` `/` . Användaren som distribuerar mallen måste ha den [åtkomst som krävs för att distribuera på klienten](deploy-to-tenant.md#required-access).

Om du vill använda en kapslad distribution anger du `scope` och `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Eller så kan du ange omfånget till `/` för vissa resurs typer, t. ex. hanterings grupper. Att skapa en ny hanterings grupp beskrivs i nästa avsnitt.

## <a name="management-group"></a>Hanteringsgrupp

Om du vill skapa en hanterings grupp i en hanterings grupps distribution måste du ange omfånget `/` för hanterings gruppen.

I följande exempel skapas en ny hanterings grupp i rot hanterings gruppen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

I nästa exempel skapas en ny hanterings grupp i hanterings gruppen som anges som överordnad. Observera att omfånget är inställt på `/` .

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

Om du vill använda en ARM-mall för att skapa en ny Azure-prenumeration i en hanterings grupp, se:

* [Skapa Azure Enterprise-avtal-prenumerationer program mässigt](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Skapa Azure-prenumerationer program mässigt för ett kund avtal från Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Skapa Azure-prenumerationer program mässigt för ett Microsoft partner avtal](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Om du vill distribuera en mall som flyttar en befintlig Azure-prenumeration till en ny hanterings grupp läser du [Flytta prenumerationer i arm-mallen](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Anpassade princip definitioner som distribueras till hanterings gruppen är tillägg för hanterings gruppen. Om du vill hämta ID för en anpassad princip definition använder du funktionen [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Inbyggda princip definitioner är klient nivå resurser. Om du vill hämta ID: t för en inbyggd princip definition använder du funktionen [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

I följande exempel visas hur du [definierar](../../governance/policy/concepts/definition-structure.md) en princip på hanterings grupps nivån och tilldelar den.

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

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuera till prenumeration och resurs grupp

Från en distribution på hanterings grupp nivå kan du rikta en prenumeration i hanterings gruppen. I följande exempel skapas en resurs grupp i en prenumeration och ett lagrings konto distribueras till den resurs gruppen.

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

* Information om hur du tilldelar roller finns i [lägga till Azure-roll tilldelningar med hjälp av Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du kan också distribuera mallar på [prenumerations nivå](deploy-to-subscription.md) och [klient nivå](deploy-to-tenant.md).
