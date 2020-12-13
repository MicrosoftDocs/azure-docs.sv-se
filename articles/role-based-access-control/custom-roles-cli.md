---
title: Skapa eller uppdatera anpassade Azure-roller med Azure CLI – Azure RBAC
description: Lär dig att visa, skapa, uppdatera eller ta bort Azure-anpassade roller med Azure CLI och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.openlocfilehash: 31dabcf77f0db76047919fa76d00f1c5ed3c96d6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369148"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Skapa eller uppdatera anpassade Azure-roller med Azure CLI

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. Den här artikeln beskriver hur du visar, skapar, uppdaterar eller tar bort anpassade roller med Azure CLI.

En stegvis själv studie kurs om hur du skapar en anpassad roll finns i [Självstudier: skapa en anpassad Azure-roll med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista med anpassade roller som är tillgängliga för tilldelning använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). I följande exempel visas alla anpassade roller i den aktuella prenumerationen.

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

## <a name="list-a-custom-role-definition"></a>Lista en anpassad roll definition

Om du vill visa en anpassad roll definition använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). Det här är samma kommando som du använder för en inbyggd roll.

```azurecli
az role definition list --name {roleName}
```

I följande exempel visas roll definitionen för den *virtuella datorns operatör* :

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

I följande exempel visas bara åtgärder för rollen *virtuell dator operatör* :

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

Använd [AZ roll definition Create](/cli/azure/role/definition#az-role-definition-create)för att skapa en anpassad roll. Roll definitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition {roleDefinition}
```

I följande exempel skapas en anpassad roll med namnet *Virtual Machine-operator*. Den här anpassade rollen tilldelar åtkomst till alla Läs åtgärder för *Microsoft. Compute*, *Microsoft. Storage* och *Microsoft. Network* Resource providers och tilldelar åtkomst till Start, omstart och övervakning av virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. I det här exemplet används en JSON-fil som indata.

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

Om du vill uppdatera en anpassad roll ska du först använda [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list) för att hämta roll definitionen. Sedan gör du önskade ändringar i roll definitionen. Använd slutligen [AZ roll definitions uppdatering](/cli/azure/role/definition#az-role-definition-update) för att spara den uppdaterade roll definitionen.

```azurecli
az role definition update --role-definition {roleDefinition}
```

I följande exempel läggs *Microsoft. Insights/diagnosticSettings/* operation till `Actions` och lägger till en hanterings grupp till `AssignableScopes` för den anpassade rollen för den *virtuella datorn* . Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.

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

Om du vill ta bort en anpassad roll använder du [AZ roll definition Delete](/cli/azure/role/definition#az-role-definition-delete). Om du vill ange vilken roll som ska tas bort använder du roll namnet eller roll-ID: t. Använd [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list)för att fastställa roll-ID: t.

```azurecli
az role definition delete --name {roleNameOrId}
```

I följande exempel tar vi bort den anpassade rollen för den *virtuella datorn* .

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Azure Resource Provider-åtgärder](resource-provider-operations.md)