---
title: Lägga till eller ta bort roll tilldelningar i Azure med hjälp av REST API – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.openlocfilehash: e4f230663e0eeddcf874c24e5041653f241f481c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964277"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av REST API.

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I Azure RBAC för att bevilja åtkomst lägger du till en roll tilldelning. Om du vill lägga till en roll tilldelning använder du [roll tilldelningarna-skapa](/rest/api/authorization/roleassignments/create) REST API och anger säkerhets objekt, roll definition och omfattning. Du måste ha åtkomst till åtgärden för att anropa detta API `Microsoft.Authorization/roleAssignments/write` . Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Använd [roll definitionerna-List](/rest/api/authorization/roledefinitions/list) REST API eller se [inbyggda roller](built-in-roles.md) för att hämta identifieraren för den roll definition som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för roll tilldelnings-ID: t. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. I URI: n ersätter du *{scope}* med omfånget för roll tilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

    I föregående exempel är Microsoft. Web en resurs leverantör som refererar till en App Service instans. På samma sätt kan du använda andra resurs leverantörer och ange omfånget. Mer information finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md) och [Azure Resource Provider-åtgärder](resource-provider-operations.md)som stöds.  

1. Ersätt *{roleAssignmentId}* med GUID-identifieraren för roll tilldelningen.

1. I begär ande texten ersätter du *{scope}* med omfånget för roll tilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med roll Definitions-ID: n.

1. Ersätt *{principalId}* med objekt identifieraren för användaren, gruppen eller tjänstens huvud namn som ska tilldelas rollen.

Följande begäran och brödtext tilldelar rollen som [säkerhets kopierings läsare](built-in-roles.md#backup-reader) till en användare vid prenumerations omfång:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Följande visar ett exempel på utdata:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

För att ta bort åtkomst i Azure RBAC tar du bort en roll tilldelning. Om du vill ta bort en roll tilldelning använder du [roll tilldelningarna-ta bort](/rest/api/authorization/roleassignments/delete) REST API. Du måste ha åtkomst till åtgärden för att anropa detta API `Microsoft.Authorization/roleAssignments/delete` . Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Hämta roll tilldelnings identifieraren (GUID). Den här identifieraren returneras när du först skapar roll tilldelningen eller så kan du få den genom att lista roll tilldelningarna.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med omfånget för att ta bort roll tilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentId}* med GUID-identifieraren för roll tilldelningen.

Följande begäran tar bort den angivna roll tilldelningen i prenumerations omfattningen:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Följande visar ett exempel på utdata:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azures roll tilldelningar med hjälp av REST API](role-assignments-list-rest.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa eller uppdatera anpassade Azure-roller med hjälp av REST API](custom-roles-rest.md)
