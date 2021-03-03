---
title: Visa en lista med Azures roll tilldelningar med REST API – Azure RBAC
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: 9780902a1c5f4a711e1abffa6b508c28efe269ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735888"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Visa en lista med Azures roll tilldelningar med hjälp av REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av REST API.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

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

- [Tilldela Azure-roller med hjälp av REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
