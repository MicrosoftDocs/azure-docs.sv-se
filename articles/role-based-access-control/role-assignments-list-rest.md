---
title: Visa en lista med Azures roll tilldelningar med REST API – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b717d84c8fedf4a87c0b81752b14914ece185250
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369216"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Visa en lista med Azures roll tilldelningar med hjälp av REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av REST API.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="list-role-assignments"></a>Visa lista över rolltilldelningar

I Azure RBAC, för att visa åtkomst, visar du roll tilldelningarna. Om du vill lista roll tilldelningar använder du en av REST-API: erna för [roll tilldelningar](/rest/api/authorization/roleassignments/list) . Du kan förfina resultaten genom att ange ett omfång och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa roll tilldelningarna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är Microsoft. Web en resurs leverantör som refererar till en App Service instans. På samma sätt kan du använda andra resurs leverantörer och ange omfånget. Mer information finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md) och [Azure Resource Provider-åtgärder](resource-provider-operations.md)som stöds.  
     
1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera roll tilldelnings listan.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | `$filter=atScope()` | Visar endast roll tilldelningar för det angivna omfånget, inte inklusive roll tilldelningarna i underscope. |
    > | `$filter=assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för en viss användare eller tjänstens huvud namn.<br/>Om användaren är medlem i en grupp som har en roll tilldelning visas även den roll tilldelningen. Det här filtret är transitivt för grupper, vilket innebär att om användaren är medlem i en grupp och gruppen är medlem i en annan grupp som har en roll tilldelning visas även roll tilldelningen.<br/>Filtret accepterar bara ett objekt-ID för en användare eller ett tjänst objekt. Det går inte att skicka ett objekt-ID för en grupp. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för den angivna användaren eller tjänstens huvud namn och vid det angivna omfånget. |
    > | `$filter=principalId+eq+'{objectId}'` | Visar en lista över roll tilldelningar för en viss användare, grupp eller tjänstens huvud namn. |

Följande begäran visar en lista över alla roll tilldelningar för den angivna användaren vid prenumerations omfånget:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Följande visar ett exempel på utdata:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
