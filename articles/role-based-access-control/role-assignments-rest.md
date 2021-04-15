---
title: Tilldela Azure-roller med hjälp REST API – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvudnamn eller hanterade identiteter med hjälp av REST API och rollbaserad åtkomstkontroll i Azure (Azure RBAC).
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
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363724"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Tilldela Azure-roller med hjälp av REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Den här artikeln beskriver hur du tilldelar roller med hjälp av REST API.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Tilldela en Azure-roll

Om du vill tilldela en roll använder du [rolltilldelningar –](/rest/api/authorization/roleassignments/create) REST API och anger säkerhetsobjekt, rolldefinition och omfång. Om du vill anropa det här API:et måste du ha åtkomst till `Microsoft.Authorization/roleAssignments/write` åtgärden. Av de inbyggda rollerna beviljas [endast ägare och](built-in-roles.md#owner) administratör [för](built-in-roles.md#user-access-administrator) användaråtkomst åtkomst till den här åtgärden.

1. Använd [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) REST API se [Inbyggda](built-in-roles.md) roller för att hämta identifieraren för rolldefinitionen som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för rolltilldelningsidentifieraren. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

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

1. I URI:en *ersätter du {scope}* med omfånget för rolltilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

    I föregående exempel är microsoft.web en resursprovider som refererar till en App Service instans. På samma sätt kan du använda andra resursproviders och ange omfånget. Mer information finns i [Azure-resursproviders och resurstyper samt](../azure-resource-manager/management/resource-providers-and-types.md) åtgärder som [stöds av Azure-resursprovidern.](resource-provider-operations.md)  

1. Ersätt *{roleAssignmentId} med* GUID-identifieraren för rolltilldelningen.

1. Ersätt *{scope} med omfånget för rolltilldelningen* i begärandetexten.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId} med* rolldefinitionsidentifieraren.

1. Ersätt *{principalId}* med objektidentifieraren för den användare, grupp eller tjänstens huvudnamn som ska tilldelas rollen.

Följande begäran och brödtext tilldelar rollen [Säkerhetskopieringsläsare](built-in-roles.md#backup-reader) till en användare med prenumerationsomfång:

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

### <a name="new-service-principal"></a>Nytt huvudnamn för tjänsten

Om du skapar ett nytt huvudnamn för tjänsten och omedelbart försöker tilldela en roll till tjänstens huvudnamn kan rolltilldelningen i vissa fall misslyckas. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvudnamn kan rolltilldelningen misslyckas. Orsaken till det här felet är troligen en replikeringsfördröjning. Tjänstens huvudnamn skapas i en region. Rolltilldelningen kan dock ske i en annan region som inte har replikerat tjänstens huvudnamn ännu.

Du kan lösa det här scenariot [genom att använda rolltilldelningar](/rest/api/authorization/roleassignments/create) – REST API och ange egenskapen `principalType` till `ServicePrincipal` . Du måste också ange `apiVersion` till `2018-09-01-preview` eller senare.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
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

- [Lista Azure-rolltilldelningar med hjälp av REST API](role-assignments-list-rest.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa eller uppdatera anpassade Roller i Azure med hjälp av REST API](custom-roles-rest.md)
