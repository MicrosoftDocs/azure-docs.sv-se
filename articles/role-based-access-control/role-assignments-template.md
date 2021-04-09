---
title: Tilldela Azure-roller med hjälp av Azure Resource Manager mallar – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure Resource Manager mallar och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: 65b4ec369085e44cdffb0550e9eeaef0196cd35a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556016"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Tilldela Azure-roller med hjälp av Azure Resource Manager mallar

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Förutom att använda Azure PowerShell eller Azure CLI kan du tilldela roller med [Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md). Mallar kan vara användbara om du behöver distribuera resurser konsekvent och upprepade gånger. I den här artikeln beskrivs hur du tilldelar roller med hjälp av mallar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Hämta objekt-ID: n

Om du vill tilldela en roll måste du ange ID: t för användaren, gruppen eller programmet som du vill tilldela rollen till. ID: t har formatet: `11111111-1111-1111-1111-111111111111` . Du kan hämta ID: t med hjälp av Azure Portal, Azure PowerShell eller Azure CLI.

### <a name="user"></a>User

Om du vill hämta ID för en användare kan du använda kommandona [Get-AzADUser](/powershell/module/az.resources/get-azaduser) eller [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Om du vill hämta ID för en grupp kan du använda kommandona [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) eller [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show) .

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Hanterade identiteter

Om du vill hämta ID: t för en hanterad identitet kan du använda [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) eller [AZ AD SP](/cli/azure/ad/sp) -kommandon.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Program

Om du vill hämta ID: t för ett huvud namn för tjänsten (identitet som används av ett program) kan du använda kommandona [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) eller [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) . För ett huvud namn för tjänsten använder du objekt-ID: t och **inte** program-ID: t.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Tilldela en Azure-roll

I Azure RBAC, för att bevilja åtkomst, tilldelar du en roll.

### <a name="resource-group-scope-without-parameters"></a>Resurs grupps omfång (utan parametrar)

Följande mall visar ett enkelt sätt att tilldela en roll. Vissa värden anges i mallen. Följande mall visar:

-  Tilldela rollen [läsare](built-in-roles.md#reader) till en användare, grupp eller ett program i ett resurs grupps omfång

Om du vill använda mallen måste du göra följande:

- Skapa en ny JSON-fil och kopiera mallen
- Ersätt `<your-principal-id>` med ID: t för en användare, grupp, hanterad identitet eller program för att tilldela rollen till

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

Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ-distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create) kommandon för hur du startar distributionen i en resurs grupp med namnet ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Följande visar ett exempel på roll tilldelningen för en användare för en resurs grupp när du har distribuerat mallen.

![Roll tilldelning i resurs gruppens omfång](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Resurs grupp eller prenumerations omfång

Den tidigare mallen är inte mycket flexibel. Följande mall använder parametrar och kan användas i olika omfång. Följande mall visar:

- Så här tilldelar du en roll till en användare, grupp eller ett program i antingen en resurs grupp eller ett prenumerations omfång
- Ange rollen ägare, deltagare och läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID: t för en användare, grupp, hanterad identitet eller program för att tilldela rollen till
- Ett unikt ID som ska användas för roll tilldelningen eller så kan du använda standard-ID: t

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
> Den här mallen är inte idempotenta om inte samma `roleNameGuid` värde anges som en parameter för varje distribution av mallen. Om inget anges `roleNameGuid` , genereras ett nytt GUID som standard vid varje distribution och efterföljande distributioner kommer att Miss förväntas med ett `Conflict: RoleAssignmentExists` fel.

Roll tilldelningens omfattning bestäms från distributions nivån. Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ-distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create) kommandon för att starta distributionen i ett resurs grupps omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Här är exempel på kommandona [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) och [AZ Deployment sub Create](/cli/azure/deployment/sub#az_deployment_sub_create) för hur du startar distributionen i ett prenumerations omfång och anger platsen.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Resursomfång

Om du behöver tilldela en roll på nivån för en resurs anger du `scope` egenskapen för roll tilldelningen till namnet på resursen.

Följande mall visar:

- Så här skapar du ett nytt lagringskonto
- Så här tilldelar du en roll till en användare, grupp eller ett program i lagrings konto omfånget
- Ange rollen ägare, deltagare och läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID: t för en användare, grupp, hanterad identitet eller program för att tilldela rollen till

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

Om du vill distribuera den tidigare mallen använder du resurs grupps kommandon. Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ-distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create) kommandon för hur du startar distributionen i ett resurs omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Följande visar ett exempel på roll tilldelningen deltagare till en användare för ett lagrings konto när du har distribuerat mallen.

![Roll tilldelning i resurs omfång](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nytt huvud namn för tjänsten

Om du skapar ett nytt huvud namn för tjänsten och sedan omedelbart försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen inte utföras i vissa fall. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvud namn i samma Azure Resource Manager mall kan roll tilldelningen Miss Miss förväntat. Orsaken till det här felet är förmodligen en fördröjning i replikeringen. Tjänstens huvud namn skapas i en region. roll tilldelningen kan dock inträffa i en annan region som ännu inte har replikerat tjänstens huvud namn.

För att åtgärda det här scenariot ska du ställa in `principalType` egenskapen till `ServicePrincipal` när roll tilldelningen skapas. Du måste också ange `apiVersion` roll tilldelningen till `2018-09-01-preview` eller senare.

Följande mall visar:

- Så här skapar du en ny hanterad identitet för tjänstens huvud namn
- Så här anger du `principalType`
- Tilldela deltagar rollen till tjänstens huvud namn i ett resurs grupps omfång

Om du vill använda mallen måste du ange följande indata:

- Det grundläggande namnet på den hanterade identiteten eller så kan du använda standard strängen

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

Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ-distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create) kommandon för att starta distributionen i ett resurs grupps omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Följande visar ett exempel på roll tilldelningen deltagare till ett nytt hanterat identitets tjänst huvud objekt när du har distribuerat mallen.

![Roll tilldelning för en ny hanterad identitets tjänstens huvud namn](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Nästa steg

- [Snabb start: skapa och distribuera ARM-mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Förstå strukturen och syntaxen för ARM-mallar](../azure-resource-manager/templates/template-syntax.md)
- [Skapa resurs grupper och resurser på prenumerations nivå](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
