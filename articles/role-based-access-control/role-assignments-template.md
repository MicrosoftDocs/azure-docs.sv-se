---
title: Tilldela Azure-roller med Azure Resource Manager mallar – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvudnamn eller hanterade identiteter med hjälp av Azure Resource Manager-mallar och rollbaserad åtkomstkontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771883"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Tilldela Azure-roller med hjälp Azure Resource Manager mallar

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Förutom att använda Azure PowerShell Azure CLI kan du tilldela roller med hjälp av [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md). Mallar kan vara användbara om du behöver distribuera resurser konsekvent och upprepade gånger. I den här artikeln beskrivs hur du tilldelar roller med hjälp av mallar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Hämta objekt-ID:er

Om du vill tilldela en roll måste du ange ID:t för den användare, grupp eller det program som du vill tilldela rollen till. ID:t har formatet: `11111111-1111-1111-1111-111111111111` . Du kan hämta ID:t med hjälp av Azure Portal, Azure PowerShell eller Azure CLI.

### <a name="user"></a>Användare

Om du vill hämta ID:t för en användare kan du använda [kommandona Get-AzADUser](/powershell/module/az.resources/get-azaduser) [eller az ad user show.](/cli/azure/ad/user#az_ad_user_show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Om du vill hämta ID:t för en grupp kan du använda [kommandona Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) [eller az ad group show.](/cli/azure/ad/group#az_ad_group_show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Hanterade identiteter

Om du vill hämta ID:t för en hanterad identitet kan du använda [Get-AzAdServiceprincipal eller](/powershell/module/az.resources/get-azadserviceprincipal) [az ad sp-kommandon.](/cli/azure/ad/sp)

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Program

Om du vill hämta ID:t för ett huvudnamn för tjänsten (identitet som används av ett program) kan du använda [kommandona Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) [eller az ad sp list.](/cli/azure/ad/sp#az_ad_sp_list) För tjänstens huvudnamn använder du objekt-ID:t **och inte** program-ID:t.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Tilldela en Azure-roll

För att bevilja åtkomst i Azure RBAC tilldelar du en roll.

### <a name="resource-group-scope-without-parameters"></a>Omfång för resursgrupp (utan parametrar)

Följande mall visar ett grundläggande sätt att tilldela en roll. Vissa värden anges i mallen. Följande mall visar:

-  Så här tilldelar [du rollen](built-in-roles.md#reader) Läsare till en användare, grupp eller ett program i ett resursgruppsomfång

Om du vill använda mallen måste du göra följande:

- Skapa en ny JSON-fil och kopiera mallen
- Ersätt `<your-principal-id>` med ID:t för en användare, grupp, hanterad identitet eller ett program som rollen ska tilldelas till

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Här är [exempelkommandona New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) för att starta distributionen i en resursgrupp med namnet ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Nedan visas ett exempel på rolltilldelningen Läsare till en användare för en resursgrupp när mallen har distribuerats.

![Rolltilldelning i resursgruppsomfång](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Resursgrupp eller prenumerationsomfång

Den tidigare mallen är inte särskilt flexibel. Följande mall använder parametrar och kan användas i olika omfång. Följande mall visar:

- Så här tilldelar du en roll till en användare, grupp eller ett program i antingen en resursgrupp eller ett prenumerationsomfång
- Så här anger du rollerna Ägare, Deltagare och Läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID för en användare, grupp, hanterad identitet eller ett program som rollen ska tilldelas till
- Ett unikt ID som ska användas för rolltilldelningen, eller så kan du använda standard-ID:t

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Den här mallen är inte idempotent såvida inte `roleNameGuid` samma värde anges som en parameter för varje distribution av mallen. Om nej anges genereras som standard ett nytt GUID för `roleNameGuid` varje distribution och efterföljande distributioner misslyckas med ett `Conflict: RoleAssignmentExists` fel.

Rolltilldelningens omfattning bestäms utifrån distributionsnivån. Här är exempel [på kommandona New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) för att starta distributionen i ett resursgruppomfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Här är exempel [på kommandona New-AzDeployment](/powershell/module/az.resources/new-azdeployment) och [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) för hur du startar distributionen i ett prenumerationsomfång och anger platsen.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Resursomfång

Om du behöver tilldela en roll på resursnivå anger du namnet på resursens namn för `scope` rolltilldelningen.

Följande mall visar:

- Så här skapar du ett nytt lagringskonto
- Så här tilldelar du en roll till en användare, grupp eller ett program i lagringskontoomfånget
- Ange rollerna Ägare, Deltagare och Läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID för en användare, grupp, hanterad identitet eller ett program som rollen ska tilldelas till

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Du distribuerar den tidigare mallen med hjälp av resursgruppskommandona. Här är exempel [på kommandona New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) för att starta distributionen i ett resursomfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Nedan visas ett exempel på rolltilldelningen Deltagare till en användare för ett lagringskonto när mallen har distribuerats.

![Rolltilldelning i resursomfång](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nytt huvudnamn för tjänsten

Om du skapar ett nytt huvudnamn för tjänsten och omedelbart försöker tilldela en roll till tjänstens huvudnamn kan rolltilldelningen i vissa fall misslyckas. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvudnamn i samma Azure Resource Manager mall, kan rolltilldelningen misslyckas. Orsaken till det här felet är troligen en replikeringsfördröjning. Tjänstens huvudnamn skapas i en region. Rolltilldelningen kan dock ske i en annan region som inte har replikerat tjänstens huvudnamn ännu.

För att lösa det här scenariot bör du ange `principalType` egenskapen som när du skapar `ServicePrincipal` rolltilldelningen. Du måste också ange `apiVersion` för rolltilldelningen till `2018-09-01-preview` eller senare.

Följande mall visar:

- Så här skapar du ett nytt huvudnamn för tjänsten för hanterad identitet
- Så här anger du `principalType`
- Så här tilldelar du rollen Deltagare till tjänstens huvudnamn i ett resursgruppomfång

Om du vill använda mallen måste du ange följande indata:

- Basnamnet på den hanterade identiteten, eller så kan du använda standardsträngen

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Här är [exempelkommandona New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) för att starta distributionen i ett resursgruppomfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Nedan visas ett exempel på rolltilldelningen Deltagare till ett nytt huvudnamn för tjänsten för hanterad identitet när mallen har distribuerats.

![Rolltilldelning för ett nytt huvudnamn för tjänsten för hanterad identitet](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa och distribuera ARM-mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Förstå strukturen och syntaxen för ARM-mallar](../azure-resource-manager/templates/template-syntax.md)
- [Skapa resursgrupper och resurser på prenumerationsnivå](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
