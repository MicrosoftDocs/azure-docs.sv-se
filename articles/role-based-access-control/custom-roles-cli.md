---
title: Skapa eller uppdatera anpassade Azure-roller med Azure CLI – Azure RBAC
description: Lär dig hur du listar, skapar, uppdaterar eller tar bort anpassade Azure-roller med hjälp av Azure CLI och rollbaserad åtkomstkontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d3d05ba65e0d3918f1651c36cd17700ebf74de76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778345"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Skapa eller uppdatera anpassade Azure-roller med Hjälp av Azure CLI

> [!IMPORTANT]
> Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om [de inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa egna anpassade roller. I den här artikeln beskrivs hur du listar, skapar, uppdaterar eller tar bort anpassade roller med hjälp av Azure CLI.

En stegvis självstudiekurs om hur du skapar en anpassad roll finns i [Självstudie: Skapa en anpassad Azure-roll med Hjälp av Azure CLI.](tutorial-custom-role-cli.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista över anpassade roller som är tillgängliga för tilldelning använder [du az role definition list](/cli/azure/role/definition#az_role_definition_list). I följande exempel visas alla anpassade roller i den aktuella prenumerationen.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Visa en anpassad rolldefinition

Om du vill visa en anpassad rolldefinition använder [du az role definition list](/cli/azure/role/definition#az_role_definition_list). Det här är samma kommando som du använder för en inbyggd roll.

```azurecli
az role definition list --name {roleName}
```

I följande exempel visas definitionen *av rollen Virtuell datoroperatör:*

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

I följande exempel visas bara åtgärderna för rollen *Virtuell datoroperatör:*

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ]
]
```

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om du vill skapa en anpassad roll använder [du az role definition create](/cli/azure/role/definition#az_role_definition_create). Rolldefinitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition {roleDefinition}
```

I följande exempel skapas en anpassad roll med namnet *Virtual Machine Operator*. Den här anpassade rollen tilldelar åtkomst till alla läsåtgärder för Resursproviders *för Microsoft.Compute,* *Microsoft.Storage* och *Microsoft.Network* och tilldelar åtkomst för att starta, starta om och övervaka virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. I det här exemplet används en JSON-fil som indata.

vmoperator.jspå

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera en anpassad roll använder du först [az role definition list](/cli/azure/role/definition#az_role_definition_list) för att hämta rolldefinitionen. Sedan gör du önskade ändringar i rolldefinitionen. Använd slutligen [az role definition update för](/cli/azure/role/definition#az_role_definition_update) att spara den uppdaterade rolldefinitionen.

```azurecli
az role definition update --role-definition {roleDefinition}
```

I följande exempel läggs *åtgärden Microsoft.Insights/diagnosticSettings/* till och en hanteringsgrupp läggs till för den anpassade `Actions` rollen `AssignableScopes` *Virtuell* datoroperatör. Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion.

vmoperator.jspå

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Om du vill ta bort en anpassad roll använder du [az role definition delete](/cli/azure/role/definition#az_role_definition_delete). Om du vill ange vilken roll som ska tas bort använder du rollnamnet eller roll-ID:t. Använd az role definition list för att [fastställa roll-ID:t.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition delete --name {roleNameOrId}
```

I följande exempel tas den anpassade rollen *Virtuell datoroperatör* bort.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa en anpassad Azure-roll med Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Åtgärder för Azure-resursprovider](resource-provider-operations.md)