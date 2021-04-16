---
title: Hantera användartilldelning för en app i Azure Active Directory
description: Lär dig hur du tilldelar och tar bort tilldelning av användare och grupper för en app med Azure Active Directory för identitetshantering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377980"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Hantera användartilldelning för en app i Azure Active Directory

Den här artikeln visar hur du tilldelar användare och grupper till företagsprogram i Azure Active Directory (Azure AD), antingen från Azure Portal eller med hjälp av PowerShell. När du tilldelar en användare till ett program visas programmet i användarens [Mina appar](https://myapps.microsoft.com/) för enkel åtkomst. Om programmet exponerar roller kan du även tilldela en viss roll till användaren.

För större kontroll kan vissa typer av företagsprogram konfigureras för att [kräva användartilldelning.](#configure-an-application-to-require-user-assignment) 

> [!IMPORTANT]
> När du tilldelar en grupp till ett program har endast användare i gruppen åtkomst. Tilldelningen överlappar inte till kapslade grupper.

> [!NOTE]
> Gruppbaserad tilldelning kräver en Azure Active Directory Premium P1- eller P2-version. Gruppbaserad tilldelning stöds endast för säkerhetsgrupper. Kapslade gruppmedlemskap Microsoft 365 grupper stöds inte för närvarande. Fler licensieringskrav för de funktioner som beskrivs i den här artikeln finns [Azure Active Directory sidan med priser.](https://azure.microsoft.com/pricing/details/active-directory) 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurera ett program för att kräva användartilldelning

Med följande typer av program kan du välja att kräva att användare tilldelas till programmet innan de kan komma åt det:

- Program som konfigurerats för federerad enkel inloggning (SSO) med SAML-baserad autentisering
- Programproxy program som använder Azure Active Directory förautentisering
- Program som bygger på Azure AD-programplattformen som använder OAuth 2.0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till programmet.

När användartilldelning krävs kan endast de användare som du uttryckligen tilldelar till programmet (antingen via direkt användartilldelning eller baserat på gruppmedlemskap) logga in. De kan komma åt appen på Mina appar eller via en direktlänk. 

När tilldelning inte *krävs,* antingen eftersom du  har angett det här alternativet till Nej eller eftersom programmet använder ett annat SSO-läge, kan alla användare komma åt programmet om de har en direktlänk till programmet eller **URL:en** för användaråtkomst på programmets **egenskapssida.** 

Den här inställningen påverkar inte om ett program visas på Mina appar. Program visas på användarnas Mina appar åtkomstpaneler när du har tilldelat en användare eller grupp till programmet. Bakgrundsinformation finns i [Hantera åtkomst till appar.](what-is-access-management.md)

Så här kräver du användartilldelning för ett program:
1. Logga in på [Azure Portal](https://portal.azure.com) med ett administratörskonto eller som ägare till programmet.
2. Välj **Azure Active Directory**. I den vänstra navigeringsmenyn väljer du **Företagsprogram**.
3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Du kan också använda filterkontrollerna för att välja programtyp, status eller synlighet och sedan **välja Tillämpa.**
4. I den vänstra navigeringsmenyn väljer du **Egenskaper.**
5. Kontrollera att **växlingsknappen Användartilldelning krävs?** är inställd på **Ja.**
   > [!NOTE]
   > Om **växlingsknappen Användartilldelning krävs?** inte är tillgänglig kan du använda PowerShell för att ange egenskapen appRoleAssignmentRequired för tjänstens huvudnamn.
6. Välj **knappen** Spara överst på skärmen.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Tilldela eller ta bort tilldelning av användare och grupper för en app med hjälp av Azure Portal
Information om hur du tilldelar eller tar bort tilldelning av en användare eller grupp med hjälp av Azure Portal finns i [Snabbstartsserie om programhantering.](add-application-portal-assign-users.md)

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Tilldela eller ta bort tilldelning av användare och grupper för en app med hjälp av Graph API
Du kan använda Graph API för att tilldela eller ta bort tilldelning av användare och grupper för en app. Mer information finns i [Approlltilldelningar.](/graph/api/resources/approleassignment)

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Tilldela användare och grupper till en app med hjälp av PowerShell
1. Öppna en upphöjd Windows PowerShell kommandotolk.
   > [!NOTE]
   > Du måste installera AzureAD-modulen (använd kommandot `Install-Module -Name AzureAD` ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory V2 PowerShell-modulen skriver du Y och trycker på RETUR.
2. Kör `Connect-AzureAD` och logga in med ett globalt administratörsanvändarkonto.
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
Mer information om hur du tilldelar en användare till en programroll finns i dokumentationen för [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Om du vill tilldela en grupp till en företagsapp måste du `Get-AzureADUser` ersätta med och med `Get-AzureADGroup` `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Mer information om hur du tilldelar en grupp till en programroll finns i dokumentationen för [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Exempel

I det här exemplet tilldelas användaren Britta Simon till [Microsoft Workplace Analytics-programmet](https://products.office.com/business/workplace-analytics) med hjälp av PowerShell.

1. I PowerShell tilldelar du motsvarande värden till variablerna $username, $app_name och $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. I det här exemplet vet vi inte vad som är det exakta namnet på den programroll som vi vill tilldela till Britta Simon. Kör följande kommandon för att hämta användaren ($user) och tjänstens huvudnamn ($sp) med hjälp av användarens UPN och visningsnamnen för tjänstens huvudnamn.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Kör kommandot för `$sp.AppRoles` att visa de roller som är tillgängliga för Workplace Analytics-programmet. I det här exemplet vill vi tilldela Britta Simon rollen Analytiker (begränsad åtkomst).
   ![Visar de roller som är tillgängliga för en användare som använder workplace analytics-rollen](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Tilldela rollnamnet till `$app_role_name` variabeln .

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Kör följande kommando för att tilldela användaren till approllen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Ta bort tilldelning av användare och grupper från en app med hjälp av PowerShell

1. Öppna en upphöjd Windows PowerShell kommandotolk.
   > [!NOTE]
   > Du måste installera AzureAD-modulen (använd kommandot `Install-Module -Name AzureAD` ). Om du uppmanas att installera en NuGet-modul eller den nya Azure Active Directory V2 PowerShell-modulen skriver du Y och trycker på RETUR.
2. Kör `Connect-AzureAD` och logga in med ett globalt administratörsanvändarkonto.
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

- [Läs mer om slutanvändaråtkomst till program](end-user-experiences.md)
- [Planera en Azure AD Mina appar distribution](my-apps-deployment-plan.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
 
## <a name="next-steps"></a>Nästa steg

- [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Ta bort en användar- eller grupptilldelning från en företagsapp]()
- [Inaktivera användarinloggningar för en företagsapp](disable-user-sign-in-portal.md)
- [Ändra namn eller logotyp för en företagsapp](./add-application-portal-configure.md)
