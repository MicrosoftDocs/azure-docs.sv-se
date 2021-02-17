---
title: Ta bort roll tilldelningar i Azure – Azure RBAC
description: Lär dig hur du tar bort åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561511"
---
# <a name="remove-azure-role-assignments"></a>Ta bort roll tilldelningar i Azure

[Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../articles/role-based-access-control/overview.md) är det auktoriserings system som du använder för att hantera åtkomst till Azure-resurser. Om du vill ta bort åtkomst från en Azure-resurs tar du bort en roll tilldelning. Den här artikeln beskriver hur du tar bort roll tilldelningar med hjälp av Azure Portal, Azure PowerShell, Azure CLI och REST API.

## <a name="prerequisites"></a>Förutsättningar

Om du vill ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) eller [ägare](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Azure Portal

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill ta bort åtkomsten.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Roll tilldelningen har marker ATS för borttagning](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-remove/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Om du ser ett meddelande om att ärvda roll tilldelningar inte kan tas bort försöker du ta bort en roll tilldelning i ett underordnat omfång. Du bör öppna åtkomst kontroll (IAM) i omfånget där rollen har tilldelats och försöka igen. Ett snabbt sätt att öppna åtkomst kontroll (IAM) i rätt omfång är att titta i kolumnen **omfattning** och klicka på länken bredvid **(ärvd)**.

   ![Ta bort roll tilldelnings meddelande för ärvda roll tilldelningar](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

I Azure PowerShell tar du bort en roll tilldelning genom att använda [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

I följande exempel tas roll tilldelningen för [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) bort från *patlong \@ contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Tar bort rollen [läsare](built-in-roles.md#reader) från *Ann Mack team* Group med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Tar bort rollen för [fakturerings läsare](built-in-roles.md#billing-reader) från *Alain \@ example.com* -användaren i hanterings gruppens omfång.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Om du får fel meddelandet "den tillhandahållna informationen inte mappas till en roll tilldelning", se till att du även anger `-Scope` `-ResourceGroupName` parametrarna eller. Mer information finns i [Felsöka Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Azure CLI

I Azure CLI tar du bort en roll tilldelning genom att använda [AZ roll tilldelning ta bort](/cli/azure/role/assignment#az_role_assignment_delete).

I följande exempel tas roll tilldelningen för [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) bort från *patlong \@ contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Tar bort rollen [läsare](built-in-roles.md#reader) från *Ann Mack team* Group med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Tar bort rollen för [fakturerings läsare](built-in-roles.md#billing-reader) från *Alain \@ example.com* -användaren i hanterings gruppens omfång.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST-API

I REST API tar du bort en roll tilldelning genom att använda [roll tilldelningar – ta bort](/rest/api/authorization/roleassignments/delete).

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

## <a name="arm-template"></a>ARM-mall

Det finns inget sätt att ta bort en roll tilldelning med hjälp av en Azure Resource Manager-mall (ARM-mall). Om du vill ta bort en roll tilldelning måste du använda andra verktyg som Azure Portal, Azure PowerShell, Azure CLI eller REST API.

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azures roll tilldelningar med hjälp av Azure Portal](role-assignments-list-portal.md)
- [Visa en lista med Azure Role-tilldelningar med Azure PowerShell](role-assignments-list-powershell.md)
- [Felsöka Azure RBAC](troubleshooting.md)
