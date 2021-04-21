---
title: Lista azure-rolldefinitioner – Azure RBAC
description: Lär dig hur du visar en lista över inbyggda och anpassade Roller i Azure med hjälp Azure Portal, Azure PowerShell, Azure CLI eller REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: b285755d24cdbf1f8ef06eb850fc218a00734f16
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771739"
---
# <a name="list-azure-role-definitions"></a>Lista azure-rolldefinitioner

En rolldefinition är en samling behörigheter som kan utföras, till exempel läsa, skriva och ta bort. Det kallas vanligtvis bara för en roll. [Rollbaserad åtkomstkontroll i Azure (Azure RBAC)](overview.md) har över 120 [inbyggda](built-in-roles.md) roller eller så kan du skapa egna anpassade roller. Den här artikeln beskriver hur du listar de inbyggda och anpassade roller som du kan använda för att bevilja åtkomst till Azure-resurser.

En lista över administratörsroller för Azure Active Directory finns i [Behörigheter för administratörsroll i Azure Active Directory](../active-directory/roles/permissions-reference.md).

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Visa en lista över alla roller

Följ de här stegen för att lista alla roller i Azure Portal.

Om du är intresserad av att visa en uppdaterad rollupplevelse kan du titta på fliken **Roller (förhandsversion),** som för närvarande är en offentlig förhandsversion. Fliken **Roller (förhandsversion)** visar samma lista över roller som **fliken Roller** med några ytterligare funktioner. Du kan använda någon av rollfliken för att arbeta med dina roller, men om du skapar eller tar bort anpassade roller kan du behöva uppdatera sidan manuellt för att se de senaste ändringarna.

#### <a name="roles"></a>[Roller](#tab/roles/)

1. I den Azure Portal klickar du **på Alla tjänster** och väljer ett omfång. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper** eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på **fliken** Roller om du vill se en lista över alla inbyggda och anpassade roller.

   Du kan se antalet användare och grupper som har tilldelats till varje roll i det aktuella omfånget.

   ![Lista över roller](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Roller (förhandsversion)](#tab/roles-preview/)

1. I den Azure Portal klickar du **på Alla tjänster** och väljer ett omfång. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper** eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på **fliken Roller (förhandsversion)** om du vill se en lista över alla inbyggda och anpassade roller.

   ![Lista över roller med hjälp av förhandsversion](./media/role-definitions-list/roles-list.png)

1. Om du vill se behörigheterna för en viss roll går du **till kolumnen Information** och klickar på **länken** Visa.

    Ett behörighetsfönster visas.

1. Klicka på **fliken Behörigheter** för att visa och söka efter behörigheter för den valda rollen.

   ![Rollbehörigheter med förhandsversion](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Visa en lista över alla roller

Om du vill lista alla roller Azure PowerShell använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Visa en rolldefinition

Om du vill visa information om en viss roll använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Visa en rolldefinition i JSON-format

Om du vill visa en roll i JSON-format använder [du Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Lista behörigheter för en rolldefinition

Om du vill visa en lista över behörigheter för en viss roll [använder du Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Visa en lista över alla roller

Om du vill visa en lista över alla roller i Azure CLI använder [du az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list
```

I följande exempel visas namn och beskrivning för alla tillgängliga rolldefinitioner:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

I följande exempel visas alla inbyggda roller.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Visa en rolldefinition

Om du vill visa information om en roll använder [du az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name {roleName}
```

I följande exempel visas definitionen *av rollen* Deltagare:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Lista behörigheter för en rolldefinition

I följande exempel visas bara *åtgärderna och* *inteÅtgärder för* rollen Deltagare. 

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

I följande exempel visas bara åtgärderna för rollen *Virtuell datordeltagare.*

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>REST-API

### <a name="list-role-definitions"></a>Visa lista över rolldefinitioner

Om du vill visa en lista över [rolldefinitioner använder du rolldefinitioner –](/rest/api/authorization/roledefinitions/list) lista REST API. Om du vill förfina resultatet anger du ett omfång och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. I URI:en *ersätter du {scope}* med det omfång som du vill visa rolldefinitionerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är microsoft.web en resursprovider som refererar till en App Service instans. På samma sätt kan du använda andra resursproviders och ange omfånget. Mer information finns i [Azure-resursproviders och resurstyper samt](../azure-resource-manager/management/resource-providers-and-types.md) åtgärder som [stöds av Azure-resursprovidern.](resource-provider-operations.md)  
     
1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera listan över rolldefinitioner.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Description |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Visar rolldefinitioner för det angivna omfånget och eventuella underomfång. |
    > | `$filter=type+eq+'{type}'` | Visar en lista över rolldefinitioner av den angivna typen. Typ av roll kan vara `CustomRole` eller `BuiltInRole` . |

I följande begäran visas anpassade rolldefinitioner i prenumerationsomfånget:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Följande visar ett exempel på utdata:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Visa en rolldefinition

Om du vill visa information om en viss roll använder du [Rolldefinitioner – Hämta](/rest/api/authorization/roledefinitions/get) eller [Rolldefinitioner – Hämta efter ID](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    För en rolldefinition på katalognivå kan du använda den här begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI:en *ersätter du {scope}* med det omfång som du vill visa rolldefinitionen för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |
     
1. Ersätt *{roleDefinitionId} med* rolldefinitionsidentifieraren.

I följande begäran visas definitionen [av rollen](built-in-roles.md#reader) Läsare:

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Följande visar ett exempel på utdata:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Nästa steg

- [Inbyggda roller i Azure](built-in-roles.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Lista Azure-rolltilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
