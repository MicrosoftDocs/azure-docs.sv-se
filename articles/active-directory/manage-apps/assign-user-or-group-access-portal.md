---
title: Hantera användartilldelning för en app i Azure Active Directory
description: Lär dig att tilldela och ta bort tilldelning av användare och grupper för en app som använder Azure Active Directory för identitets hantering.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 59c69e88fb64cbb00063534be9fd41b86dce0b62
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053388"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Hantera användartilldelning för en app i Azure Active Directory

Den här artikeln visar hur du tilldelar användare och grupper till företags program i Azure Active Directory (Azure AD), antingen i Azure Portal eller med hjälp av PowerShell. När du tilldelar en användare till ett program visas programmet i användarens [Mina appar](https://myapps.microsoft.com/) för enkel åtkomst. Om programmet exponerar roller kan du också tilldela användaren en specifik roll.

För bättre kontroll kan vissa typer av företags program konfigureras för att [kräva användar tilldelning](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> När du tilldelar en grupp till ett program får endast användare i gruppen åtkomst. Tilldelningen överlappar inte kapslade grupper.

> [!NOTE]
> Gruppbaserad tilldelning kräver Azure Active Directory Premium P1 eller P2 Edition. Gruppbaserad tilldelning stöds endast för säkerhets grupper. Kapslade grupp medlemskap och Microsoft 365 grupper stöds inte för närvarande. Mer licensierings krav för de funktioner som beskrivs i den här artikeln finns på [sidan Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurera ett program för att kräva användar tilldelning

Med följande typer av program kan du välja att kräva att användarna ska tilldelas programmet innan de kan komma åt det:

- Program som kon figurer ATS för federerad enkel inloggning (SSO) med SAML-baserad autentisering
- Program för programproxy som använder Azure Active Directory förautentisering
- Program som bygger på Azure AD-programplattformen som använder OAuth 2,0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet.

När användar tilldelning krävs kommer endast de användare som du uttryckligen tilldelar till programmet (antingen genom direkt användar tilldelning eller baserat på grupp medlemskap) att kunna logga in. De kan komma åt appen på sidan Mina appar eller genom att använda en direkt länk. 

Om tilldelning *inte krävs*, antingen på grund av att du har ställt in det här alternativet på **Nej** eller på att programmet använder ett annat SSO-läge, kan alla användare komma åt programmet om de har en direkt länk till programmet eller URL: en för **användar åtkomst** på programmets **egenskaps** sida. 

Den här inställningen påverkar inte om ett program visas i Mina appar. Program visas på användarnas åtkomst paneler för Mina appar när du har tilldelat en användare eller grupp till programmet. I bakgrunden, se [Hantera åtkomst till appar](what-is-access-management.md).

Så här kräver du användar tilldelning för ett program:
1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörs konto eller som ägare till programmet.
2. Välj **Azure Active Directory**. I den vänstra navigerings menyn väljer du **företags program**.
3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Eller Använd filter kontrollerna för att välja program typ, status eller synlighet och välj sedan **Använd**.
4. I den vänstra navigerings menyn väljer du **Egenskaper**.
5. Kontrol lera att **användar tilldelningen krävs?** växlingen är inställd på **Ja**.
   > [!NOTE]
   > Om **användar tilldelningen krävs?** växla är inte tillgänglig, du kan använda PowerShell för att ange egenskapen appRoleAssignmentRequired för tjänstens huvud namn.
6. Välj knappen **Spara** överst på skärmen.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Tilldela eller ta bort tilldelning av användare och grupper för en app med hjälp av Azure Portal
Information om hur du tilldelar eller tar bort en användare eller grupp med hjälp av Azure Portal finns i [snabb starts serien för program hantering](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Tilldela eller ta bort tilldelning av användare och grupper för en app med hjälp av Graph API
Du kan använda Graph API för att tilldela eller ta bort tilldelning av användare och grupper för en app. Mer information finns i [roll tilldelningar för appar](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Tilldela användare och grupper till en app med hjälp av PowerShell
1. Öppna en upphöjd kommando tolk i Windows PowerShell.
   > [!NOTE]
   > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD` ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory v2 PowerShell-modulen skriver du Y och trycker på RETUR.
2. Kör `Connect-AzureAD` och logga in med ett globalt administratörs användar konto.
3. Använd följande skript för att tilldela en användare och roll till ett program:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Mer information om hur du tilldelar en användare till en program roll finns i dokumentationen för [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Om du vill tilldela en grupp till en företags app måste du ersätta `Get-AzureADUser` med `Get-AzureADGroup` och ersätta `New-AzureADUserAppRoleAssignment` med `New-AzureADGroupAppRoleAssignment` .

Mer information om hur du tilldelar en grupp till en program roll finns i dokumentationen för [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Exempel

I det här exemplet tilldelas användaren Britta Simon till Microsoft-programmet för [företags analys](https://products.office.com/business/workplace-analytics) med hjälp av PowerShell.

1. I PowerShell tilldelar du motsvarande värden till variablerna $username $app _name och $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. I det här exemplet vet vi inte vad som är det exakta namnet på den program roll som vi vill tilldela till Britta Simon. Kör följande kommandon för att hämta användaren ($user) och tjänstens huvud namn ($sp) med hjälp av användarens UPN och visnings namnen för tjänstens huvud namn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Kör kommandot `$sp.AppRoles` för att visa de roller som är tillgängliga för programmet för arbets styrnings analys. I det här exemplet vill vi tilldela Britta Simon rollen analytiker (begränsad åtkomst).
   ![Visar de roller som är tillgängliga för en användare med rollen arbets plats analys](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Tilldela roll namnet till `$app_role_name` variabeln.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Kör följande kommando för att tilldela användaren till app-rollen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Ta bort tilldelning av användare och grupper från en app med hjälp av PowerShell

1. Öppna en upphöjd kommando tolk i Windows PowerShell.
   > [!NOTE]
   > Du måste installera AzureAD-modulen (Använd kommandot `Install-Module -Name AzureAD` ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory v2 PowerShell-modulen skriver du Y och trycker på RETUR.
2. Kör `Connect-AzureAD` och logga in med ett globalt administratörs användar konto.
3. Använd följande skript för att ta bort en användare och roll från ett program:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Relaterade artiklar

- [Läs mer om slut användar åtkomst till program](end-user-experiences.md)
- [Planera distributionen av Azure AD Mina appar](my-apps-deployment-plan.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
 
## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ta bort en användare eller grupp tilldelning från en företags app]()
- [Inaktivera användar inloggningar för en företags app](disable-user-sign-in-portal.md)
- [Ändra namn eller logo typ för en företags app](./add-application-portal-configure.md)
