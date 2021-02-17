---
title: 'Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell-Azure RBAC'
description: Lär dig hur du beviljar en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell och rollbaserad åtkomst kontroll i Azure (Azure RBAC) i den här självstudien.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 45993d617028dec13c7a8b57587c7204322965cf
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555191"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-azure-powershell"></a>Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) är hur du hanterar åtkomst till Azure-resurser. I den här självstudien kommer du at ge en användare åtkomst för att visa allt i en prenumeration och hantera allt innehåll i en resursgrupp med hjälp av Azure PowerShell.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Beviljar åtkomst till en användare med olika omfång
> * Visar åtkomst
> * Ta bort åtkomst

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- Behörighet att skapa användare i Azure Active Directory (eller en befintlig grupp)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Rolltilldelningar

I Azure RBAC för att bevilja åtkomst skapar du en roll tilldelning. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Här följer två rolltilldelningar som du utför i den här självstudiekursen:

| Säkerhetsobjekt | Rolldefinition | Omfång |
| --- | --- | --- |
| User<br>(RBAC-kursanvändare) | [Läsare](built-in-roles.md#reader) | Prenumeration |
| User<br>(RBAC-kursanvändare)| [Deltagare](built-in-roles.md#contributor) | Resursgrupp<br>(rbac-tutorial-resource-user) |

   ![Rolltilldelningar för en användare](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Skapa en användare

Om du vill tilldela en roll behöver du en användare, grupp eller tjänstens huvudnamn. Om du inte redan har en användare skapar du en.

1. Skapa ett lösenord som uppfyller kraven på lösenordskomplexitet i Azure Cloud Shell.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Skapa en ny användare för din domän med kommandot [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du kan använda en resursgrupp för att visa hur du tilldelar en roll med resursgruppomfång.

1. Hämta en lista över regionplatser med hjälp av kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Välj en plats nära dig och tilldela en variabel.

   ```azurepowershell
   $location = "westus"
   ```

1. Skapa en ny resursgrupp med hjälp av kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill bevilja åtkomst för användaren använder du kommandot [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att tilldela en roll. Du måste ange säkerhetsobjekt, rolldefinition och omfång.

1. Hämta ID för din prenumeration med hjälp av kommandot [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Spara prenumerationsomfattningen i en variabel.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Tilldela rollen [Läsare](built-in-roles.md#reader) till användaren med prenumerationsomfång.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Tilldela rollen [Deltagare](built-in-roles.md#contributor) till användaren med resursgruppomfång.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Visar åtkomst

1. För att bekräfta åtkomsten till prenumerationen använder du kommandot [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Du kan se att rollen Läsare har tilldelats till användaren RBAC-kursanvändare med prenumerationsomfång.

1. För att bekräfta åtkomsten till resursgruppen använder du kommandot [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) för att visa en lista över rolltilldelningarna.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Du kan se att rollerna Läsare och Deltagare har tilldelats till RBAC-kursanvändare. Deltagarrollen är definitionsområdet rbac-tutorial-resource-group och rollen Läsare ärvs i prenumerationomfånget.

## <a name="optional-list-access-using-the-azure-portal"></a>(Valfritt) Visa åtkomst med hjälp av Azure Portal

1. Om du vill se rolltilldelningen i Azure-portalen kan du visa **åtkomstkontroll (IAM)**-bladet för prenumerationen.

    ![Rolltilldelningar för en användare med prenumerationsomfång](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Visa bladet **Åtkomstkontroll (IAM)** för resursgruppen.

    ![Rolltilldelningar för en användare med resursgruppomfång](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Ta bort åtkomst

Ta bort åtkomst för användare, grupper och program med [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) för att ta bort en rolltilldelning.

1. Använd följande kommando för att ta bort rolltilldelningen Deltagare från användarens resursgruppomfång.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Använd följande kommando för att ta bort rolltilldelningen Läsare från användarens prenumerationsomfång.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen och användaren.

1. Om du vill ta bort resursgruppen använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. När du uppmanas att bekräfta skriver du **Y**. Det tar några sekunder att ta bort.

1. Ta bort en användare med hjälp av kommandot [Ta bort AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Tilldela Azure-roller med hjälp av Azure PowerShell](role-assignments-powershell.md)