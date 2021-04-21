---
title: Utöka åtkomst för att hantera alla Azure-prenumerationer och hanteringsgrupper
description: Beskriver hur du höjer åtkomsten för en global administratör för att hantera alla prenumerationer och hanteringsgrupper i Azure Active Directory med hjälp av Azure Portal eller REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778482"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Utöka åtkomst för att hantera alla Azure-prenumerationer och hanteringsgrupper

Som global administratör i Azure Active Directory (Azure AD) kanske du inte har åtkomst till alla prenumerationer och hanteringsgrupper i din katalog. I den här artikeln beskrivs hur du kan utöka din åtkomst till alla prenumerationer och hanteringsgrupper.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Varför skulle du behöva höja din behörighet?

Om du är global administratör kan det finnas tillfällen när du vill utföra följande åtgärder:

- Återfå åtkomst till en Azure-prenumeration eller hanteringsgrupp när en användare har förlorat åtkomst
- tilldela en annan användare eller sig själv en Azure-prenumeration eller -hanteringsgrupp
- Se alla Azure-prenumerationer eller hanteringsgrupper i en organisation
- Tillåt att en Automation-app (till exempel en fakturerings- eller granskningsapp) får åtkomst till alla Azure-prenumerationer eller hanteringsgrupper

## <a name="how-does-elevated-access-work"></a>Hur fungerar förhöjd åtkomst?

Azure AD- och Azure-resurser skyddas oberoende av varandra. Det innebär att Azure AD-rolltilldelningar inte beviljar åtkomst till Azure-resurser och Azure-rolltilldelningar beviljar inte åtkomst till Azure AD. Men om du är global [administratör i](../active-directory/roles/permissions-reference.md#global-administrator) Azure AD kan du tilldela dig själv åtkomst till alla Azure-prenumerationer och hanteringsgrupper i din katalog. Använd den här funktionen om du inte har åtkomst till Azure-prenumerationsresurser, till exempel virtuella datorer eller lagringskonton, och du vill använda din globala administratörsbehörighet för att få åtkomst till dessa resurser.

När du höjer din behörighet tilldelas du rollen [Administratör för](built-in-roles.md#user-access-administrator) användaråtkomst i Azure i rotomfånget ( `/` ).På så sätt kan du visa alla resurser och tilldela åtkomst i valfri prenumeration eller hanteringsgrupp i katalogen. Rolltilldelningar för administratör för användaråtkomst kan tas bort med Azure PowerShell, Azure CLI eller REST API.

Du bör ta bort den här utökade åtkomsten när du har gjort de ändringar du behöver göra i rotomfånget.

![Upphöj åtkomst](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomst för en global administratör

Följ de här stegen för att utöka åtkomsten för en global administratör med hjälp av Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrationscenter](https://aad.portal.azure.com) som global administratör.

    Om du använder Azure AD Privileged Identity Management aktiverar [du rolltilldelningen Global administratör.](../active-directory/privileged-identity-management/pim-how-to-activate-role.md)

1. Öppna **Azure Active Directory**.

1. Välj **Egenskaper** under **Hantera**.

   ![Välj Egenskaper för Azure Active Directory egenskaper – skärmbild](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Under **Åtkomsthantering för Azure-resurser** anger du växlingsknappen till **Ja.**

   ![Åtkomsthantering för Azure-resurser – skärmbild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du ställer in växlingsknappen **på Ja** tilldelas du rollen Administratör för användaråtkomst i Azure RBAC i rotomfånget (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Den här växlingsknappen är endast tillgänglig för användare som har tilldelats rollen Global administratör i Azure AD.

   När du ställer in växlingsknappen **på Nej** tas rollen Administratör för användaråtkomst i Azure RBAC bort från ditt användarkonto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanteringsgrupper som du har beviljats åtkomst till.

    > [!NOTE]
    > Om du använder [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)ändrar inte inaktiveringen av din rolltilldelning åtkomsthanteringen för **Azure-resurser** till **Nej.** För att behålla lägsta privilegierad åtkomst rekommenderar vi att du ställer in den här **växlingsknappen på Nej** innan du inaktiverar din rolltilldelning.
    
1. Spara **inställningen genom** att klicka på Spara.

   Den här inställningen är inte en global egenskap och gäller endast för den inloggade användaren. Du kan inte höja behörigheten för alla medlemmar i rollen Global administratör.

1. Logga ut och logga in igen för att uppdatera din åtkomst.

    Nu bör du ha åtkomst till alla prenumerationer och hanteringsgrupper i din katalog. När du visar fönstret Åtkomstkontroll (IAM) ser du att du har tilldelats rollen Administratör för användaråtkomst i rotomfånget.

   ![Prenumerationsrolltilldelningar med rotomfång – skärmbild](./media/elevate-access-global-admin/iam-root.png)

1. Gör de ändringar du behöver göra vid förhöjd åtkomst.

    Information om hur du tilldelar roller finns i [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md). Om du använder en Privileged Identity Management se Identifiera [Azure-resurser för att hantera eller](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) Tilldela [Azure-resursroller.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

1. Utför stegen i följande avsnitt för att ta bort din förhöjda åtkomst.

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort rolltilldelningen Administratör för `/` användaråtkomst i rotomfånget ( ).

1. Logga in som samma användare som användes för att höja åtkomsten.

1. I navigeringslistan klickar du **på Azure Active Directory** och sedan på **Egenskaper.**

1. Ställ in **Åtkomsthantering för Azure-resurser** på **Nej.** Eftersom det här är en inställning per användare måste du vara inloggad som samma användare som användes för att utöka åtkomsten.

    Om du försöker ta bort rolltilldelningen Administratör för användaråtkomst i fönstret Åtkomstkontroll (IAM) visas följande meddelande. Om du vill ta bort rolltilldelningen  måste du ange växlingsknappen till Nej eller använda Azure PowerShell, Azure CLI eller REST API.

    ![Ta bort rolltilldelningar med rotomfång](./media/elevate-access-global-admin/iam-root-remove.png)

1. Logga ut som global administratör.

    Om du använder en Privileged Identity Management inaktiverar du rolltilldelningen Global administratör.

    > [!NOTE]
    > Om du använder [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)ändrar inte inaktiveringen av rolltilldelningen åtkomsthanteringen för **Azure-resurser** till **Nej.** Om du vill behålla lägsta privilegierade åtkomst rekommenderar vi att du ställer in den här **växlingsknappen** på Nej innan du inaktiverar din rolltilldelning.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Visa en lista med rolltilldelning i rotomfånget (/)

Om du vill visa rolltilldelningen Administratör för användaråtkomst för en användare i rotomfånget ( ) använder du kommandot `/` [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort rolltilldelningen Administratör för användaråtkomst för dig själv eller `/` en annan användare i rotomfånget ( ).

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som användes för att höja behörigheten eller en annan global administratör med förhöjd åtkomst i rotomfånget.

1. Använd kommandot [Remove-AzRoleAssignment för](/powershell/module/az.resources/remove-azroleassignment) att ta bort rolltilldelningen Administratör för användaråtkomst.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomst för en global administratör

Använd följande grundläggande steg för att utöka åtkomsten för en global administratör med hjälp av Azure CLI.

1. Använd kommandot [az rest för](/cli/azure/reference-index#az_rest) att anropa `elevateAccess` slutpunkten, som ger dig rollen Administratör för användaråtkomst i rotomfånget ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Gör de ändringar du behöver göra vid förhöjd åtkomst.

    Information om hur du tilldelar roller finns i [Tilldela Azure-roller med hjälp av Azure CLI.](role-assignments-cli.md)

1. Utför stegen i ett senare avsnitt för att ta bort din förhöjda åtkomst.

### <a name="list-role-assignment-at-root-scope-"></a>Visa en lista med rolltilldelning i rotomfånget (/)

Om du vill visa rolltilldelningen Administratör för användaråtkomst för en användare i rotomfånget ( `/` ) använder du kommandot az role assignment [list.](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort rolltilldelningen Administratör för användaråtkomst för dig själv eller `/` en annan användare i rotomfånget ( ).

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som användes för att höja behörigheten eller en annan global administratör med förhöjd åtkomst i rotomfånget.

1. Använd kommandot [az role assignment delete för](/cli/azure/role/assignment#az_role_assignment_delete) att ta bort rolltilldelningen Administratör för användaråtkomst.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomst för en global administratör

Använd följande grundläggande steg för att utöka åtkomsten för en global administratör med hjälp av REST API.

1. Med HJÄLP av REST `elevateAccess` anropar du , vilket ger dig rollen Administratör för användaråtkomst i rotomfånget ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Gör de ändringar du behöver göra vid förhöjd åtkomst.

    Information om hur du tilldelar roller finns [i Tilldela Azure-roller med hjälp av REST API](role-assignments-rest.md).

1. Utför stegen i ett senare avsnitt för att ta bort din förhöjda åtkomst.

### <a name="list-role-assignments-at-root-scope-"></a>Visa en lista över rolltilldelningar i rotomfånget (/)

Du kan lista alla rolltilldelningar för en användare i rotomfånget ( `/` ).

- Anropa [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) där `{objectIdOfUser}` är objekt-ID:t för den användare vars rolltilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Visa en lista över tilldelningsnekanden i rotomfånget (/)

Du kan lista alla tilldelningsnekanden för en användare i rotomfånget ( `/` ).

- Anropa GET denyAssignments där `{objectIdOfUser}` är objekt-ID:t för den användare vars nekandetilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

När du anropar skapar du en rolltilldelning åt dig själv, så om du vill återkalla dessa behörigheter måste du ta bort rolltilldelningen Administratör för användaråtkomst för dig själv i `elevateAccess` rotomfånget ( `/` ).

1. Anropa [GET roleDefinitions där](/rest/api/authorization/roledefinitions/get) är lika med Administratör för `roleName` användaråtkomst för att fastställa namn-ID för rollen Administratör för användaråtkomst.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Spara ID:t från `name` parametern , i det här fallet `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` .

1. Du måste också lista rolltilldelningen för katalogadministratören i katalogomfånget. Visa en lista över alla tilldelningar i katalogomfånget `principalId` för den katalogadministratör som gjorde upphöjt åtkomstsamtal. Då visas alla tilldelningar i katalogen för objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >En katalogadministratör bör inte ha många tilldelningar. Om den tidigare frågan returnerar för många tilldelningar kan du också fråga efter alla tilldelningar bara på katalogomfångsnivå och sedan filtrera resultaten: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Föregående anrop returnerar en lista över rolltilldelningar. Hitta rolltilldelningen där omfånget är och slutar med rollnamnets ID som du hittade i steg 1 och matchar `"/"` `roleDefinitionId` `principalId` objectId för katalogadministratören. 
    
    Exempel på rolltilldelning:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Spara återigen ID:t från parametern , i det här fallet `name` 11111111-1111-1111-1111-111111111111.

1. Slutligen använder du rolltilldelnings-ID:t för att ta bort tilldelningen som lagts till av `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå de olika rollerna](rbac-and-directory-admin-roles.md)
- [Tilldela Azure-roller med hjälp av REST API](role-assignments-rest.md)
