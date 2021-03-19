---
title: Visa en lista över Azure Deny-tilldelningar med hjälp av REST API – Azure RBAC
description: Lär dig hur du visar Azure Deny-tilldelningar för användare, grupper och program med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84791919"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Visa en lista över Azure Deny-tilldelningar med hjälp av REST API

[Azure Deny-tilldelningar](deny-assignments.md) blockerar användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger åtkomst till dem. Den här artikeln beskriver hur du visar en lista över nekade tilldelningar med hjälp av REST API.

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt. För information om hur neka-tilldelningar skapas, se [Azure Deny-tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Förutsättningar

För att få information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörighet, som ingår i de flesta [inbyggda Azure-roller](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista en enskild neka-tilldelning

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa neka-tilldelningar för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Prenumeration |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{Deny-Assignment-ID}* med det ID för tilldelning av neka som du vill hämta.

## <a name="list-multiple-deny-assignments"></a>Lista flera neka-tilldelningar

1. Börja med en av följande begär Anden:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Med valfria parametrar:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa neka-tilldelningar för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Prenumeration |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera listan neka tilldelning.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | (inget filter) | Visar alla neka-tilldelningar på, ovanför och under det angivna omfånget. |
    > | `$filter=atScope()` | Visar en lista över neka-tilldelningar för det angivna omfånget och ovan. Inkluderar inte neka-tilldelningar i underscope. |
    > | `$filter=assignedTo('{objectId}')` | Visar en lista över neka tilldelningar för den angivna användaren eller tjänstens huvud namn.<br/>Om användaren är medlem i en grupp som har en neka-tilldelning visas även den nekande tilldelningen. Det här filtret är transitivt för grupper, vilket innebär att om användaren är medlem i en grupp och gruppen är medlem i en annan grupp som har en neka-tilldelning visas även den nekande tilldelningen.<br/>Filtret accepterar bara ett objekt-ID för en användare eller ett tjänst objekt. Det går inte att skicka ett objekt-ID för en grupp. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Visar en lista över neka tilldelningar för den angivna användaren eller tjänstens huvud namn och vid det angivna omfånget. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Visar en lista med nekade tilldelningar med det angivna namnet. |
    > | `$filter=principalId+eq+'{objectId}'` | Visar en lista över neka tilldelningar för den angivna användaren, gruppen eller tjänstens huvud namn. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Visa lista över neka tilldelningar i rot omfånget (/)

1. Öka åtkomsten enligt beskrivningen i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](elevate-access-global-admin.md).

1. Använd följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera listan neka tilldelning. Ett filter måste anges.

    > [!div class="mx-tableFixed"]
    > | Filtrera | Beskrivning |
    > | --- | --- |
    > | `$filter=atScope()` | Visa lista över neka-tilldelningar för rot omfånget. Inkluderar inte neka-tilldelningar i underscope. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Visa lista över nekade tilldelningar med det angivna namnet. |

1. Ta bort utökad åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Deny-tilldelningar](deny-assignments.md)
- [Utöka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](elevate-access-global-admin.md)
- [Azure REST API-referens](/rest/api/azure/)
