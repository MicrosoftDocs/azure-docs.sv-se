---
title: Skapa eller uppdatera anpassade Azure-roller med hjälp av REST API – Azure RBAC
description: Lär dig att visa, skapa, uppdatera eller ta bort Azure-anpassade roller med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ab64765fc4e329c8edbf7a4db813113d20d476b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557517"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Skapa eller uppdatera anpassade Azure-roller med hjälp av REST API

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. Den här artikeln beskriver hur du visar, skapar, uppdaterar eller tar bort anpassade roller med hjälp av REST API.

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista med alla anpassade roller i en katalog använder du REST API [roll definitioner-lista](/rest/api/authorization/roledefinitions/list) .

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersätt *{filter}* med roll typen.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrera baserat på typen av CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Visa en lista med anpassade roller i ett omfång

Om du vill visa en lista med anpassade roller i ett omfång använder du [roll definitionerna-list](/rest/api/authorization/roledefinitions/list) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{filter}* med roll typen.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtrera baserat på typen av CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Lista en anpassad roll definition efter namn

Om du vill hämta information om en anpassad roll med dess visnings namn använder du [roll definitionerna-get](/rest/api/authorization/roledefinitions/get) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{filter}* med rollens visnings namn.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Använd URL-kodad form med det exakta visnings namnet för rollen. Till exempel, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Lista en anpassad roll definition efter ID

Om du vill hämta information om en anpassad roll efter dess unika identifierare använder du [roll definitionerna-get](/rest/api/authorization/roledefinitions/get) REST API.

1. Använd [roll definitionerna-List](/rest/api/authorization/roledefinitions/list) REST API för att hämta GUID-identifieraren för rollen.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med roll DEFINITIONens GUID-identifierare.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om du vill skapa en anpassad roll använder du [roll definitionerna-skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa det här API: et måste du vara inloggad med en användare som har tilldelats en roll som har `Microsoft.Authorization/roleDefinitions/write` behörighet för alla `assignableScopes` . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Granska listan över [resurs leverantörs åtgärder](resource-provider-operations.md) som är tillgängliga för att skapa behörigheter för din anpassade roll.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för den anpassade roll identifieraren. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. I URI: n ersätter du *{scope}* med den första `assignableScopes` av den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. I begär ande texten ersätter du *{roleDefinitionId}* med GUID-identifieraren.

1. Om `assignableScopes` är en prenumeration eller resurs grupp ersätter du instanserna *{subscriptionId}* eller *{resourceGroup}* med dina identifierare.

1. Om `assignableScopes` är en hanterings grupp ersätter du *{ID}* -instansen med hanterings gruppens identifierare. Att lägga till en hanterings grupp i `assignableScopes` är för närvarande en för hands version.

1. I `actions` egenskapen lägger du till de åtgärder som rollen kan utföra.

1. I `notActions` egenskapen lägger du till de åtgärder som undantas från tillåten `actions` .

1. I `roleName` egenskaperna och `description` anger du ett unikt roll namn och en beskrivning. Mer information om egenskaperna finns i Azure- [anpassade roller](custom-roles.md).

    Följande visar ett exempel på en begär ande text:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera en anpassad roll använder du [roll definitionerna-skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa det här API: et måste du vara inloggad med en användare som har tilldelats en roll som har `Microsoft.Authorization/roleDefinitions/write` behörighet för alla `assignableScopes` . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Använd [roll definitionerna – lista](/rest/api/authorization/roledefinitions/list) eller [roll definitioner – Hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta information om den anpassade rollen. Mer information finns i avsnittet om [anpassade roller](#list-custom-roles) i föregående lista.

1. Börja med följande begäran:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med den första `assignableScopes` av den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. Utifrån informationen om den anpassade rollen skapar du en begär ande text med följande format:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Uppdatera begär ande texten med de ändringar som du vill göra till den anpassade rollen.

    Nedan visas ett exempel på en begär ande text med en ny åtgärd för diagnostiska inställningar som har lagts till:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Om du vill ta bort en anpassad roll använder du [roll definitionerna-ta bort](/rest/api/authorization/roledefinitions/delete) REST API. För att anropa det här API: et måste du vara inloggad med en användare som har tilldelats en roll som har `Microsoft.Authorization/roleDefinitions/delete` behörighet för alla `assignableScopes` . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Använd [roll definitionerna – lista](/rest/api/authorization/roledefinitions/list) eller [roll definitioner – Hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta GUID-identifieraren för den anpassade rollen. Mer information finns i avsnittet om [anpassade roller](#list-custom-roles) i föregående lista.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill ta bort den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller i Azure](custom-roles.md)
- [Tilldela Azure-roller med hjälp av REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
