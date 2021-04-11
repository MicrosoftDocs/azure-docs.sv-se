---
title: Tilldela Azure-roller med hjälp av REST API – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av REST API och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 9a61f54530f25ac33c6ef097698198a11cf1275e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581439"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Tilldela Azure-roller med hjälp av REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] I den här artikeln beskrivs hur du tilldelar roller med hjälp av REST API.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Tilldela en Azure-roll

Om du vill tilldela en roll använder du [roll tilldelningarna-skapa](/rest/api/authorization/roleassignments/create) REST API och anger säkerhets objekt, roll definition och omfattning. Du måste ha åtkomst till åtgärden för att anropa detta API `Microsoft.Authorization/roleAssignments/write` . Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

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

### <a name="new-service-principal"></a>Nytt huvud namn för tjänsten

Om du skapar ett nytt huvud namn för tjänsten och sedan omedelbart försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen inte utföras i vissa fall. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen Miss Miss sen. Orsaken till det här felet är förmodligen en fördröjning i replikeringen. Tjänstens huvud namn skapas i en region. roll tilldelningen kan dock inträffa i en annan region som ännu inte har replikerat tjänstens huvud namn.

För att åtgärda det här scenariot ska du ställa in `principalType` egenskapen till `ServicePrincipal` när roll tilldelningen skapas. Du måste också ange `apiVersion` roll tilldelningen till `2018-09-01-preview` eller senare.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azures roll tilldelningar med hjälp av REST API](role-assignments-list-rest.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa eller uppdatera anpassade Azure-roller med hjälp av REST API](custom-roles-rest.md)
