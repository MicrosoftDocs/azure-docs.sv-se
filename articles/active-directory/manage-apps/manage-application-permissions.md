---
title: Hantera användar- och administratörsbehörigheter – Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar och hanterar behörigheter för programmet i Azure AD. Du kan till exempel återkalla alla behörigheter som beviljats för ett program.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373987"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Vidta åtgärder för överprivilegierade eller misstänkta program i Azure Active Directory

Lär dig hur du granskar och hanterar programbehörigheter. Den här artikeln innehåller olika åtgärder som du kan vidta för att skydda ditt program enligt scenariot. Dessa åtgärder gäller för alla program som har lagts till i din Azure Active Directory-klientorganisation (Azure AD) via användar- eller administratörsmedgivande.

Mer information om medgivande till program finns i Azure Active Directory [ramverket för medgivande.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra följande åtgärder måste du logga in som global administratör, programadministratör eller molnprogramadministratör.

Om du vill begränsa åtkomsten till program måste du kräva användartilldelning och sedan tilldela användare eller grupper till programmet.  Mer information finns i [Metoder för att tilldela användare och grupper](./assign-user-or-group-access-portal.md).

Du kan komma åt Azure AD-portalen för att hämta kontextuella PowerShell-skript för att utföra åtgärderna.
 
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
2. Välj **Azure Active Directory**  >  **Företagsprogram**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Behörigheter**. I kommandofältet väljer du **Granska behörigheter.**

![Skärmbild av fönstret granska behörigheter.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Kontrollera åtkomsten till ett program

Vi rekommenderar att du begränsar åtkomsten till programmet genom att aktivera inställningen **Användartilldelning.**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
2. Välj **Azure Active Directory**  >  **Företagsprogram**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och ange sedan **Användarkrav som krävs** till **Ja.**
5. Välj **Användare och grupper** och ta sedan bort de oönskade användare som har tilldelats till programmet.
6. Tilldela användare eller grupper till programmet.

Du kan också ta bort alla användare som har tilldelats till programmet med hjälp av PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Återkalla alla behörigheter för ett program

När du använder PowerShell-skriptet återkallas alla behörigheter som beviljats för det här programmet.

> [!NOTE]
> Om du återkallar den aktuella behörigheten hindras inte användarna från att godkänna programmet på nytt. Om du vill blockera användare från medgivande läser du Konfigurera [hur användare godkänner program.](configure-user-consent.md)

Du kan också inaktivera programmet för att hålla användarna från att komma åt appen och för att hålla programmet från att komma åt dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
2. Välj **Azure Active Directory**  >  **Företagsprogram**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och ange sedan **Aktiverad för användare att logga in?** till **Nej.**

## <a name="investigate-a-suspicious-application"></a>Undersöka ett misstänkt program

Vi rekommenderar att du begränsar åtkomsten till programmet genom att aktivera inställningen **Användartilldelning.** Granska sedan de behörigheter som användare och administratörer har beviljat till programmet.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
3. Välj **Azure Active Directory**  >  **Företagsprogram**.
5. Välj det program som du vill begränsa åtkomsten till.
6. Välj **Egenskaper** och ange sedan **Användarkrav som krävs** till **Ja.**
7. Välj **Behörigheter** och granska de behörigheter som administratören och användaren har samtyckt till.

Om du vill kan du med hjälp av PowerShell:

- Ta bort alla tilldelade användare för att hindra dem från att logga in i programmet.
- Ogiltigförklara uppdateringstoken för användare som har åtkomst till programmet.
- Återkalla alla behörigheter för programmet.

Eller så kan du inaktivera programmet för att blockera användarnas åtkomst och stoppa programmets åtkomst till dina data.


## <a name="disable-a-malicious-application"></a>Inaktivera ett skadligt program 

Vi rekommenderar att du inaktiverar programmet för att blockera användarnas åtkomst och för att hindra programmet från att komma åt dina data. Om du tar bort programmet i stället kan användarna godkänna programmet på ett annat sätt och bevilja åtkomst till dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
2. Välj **Azure Active Directory**  >  **Enterprise-program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och kopiera sedan objekt-ID:t.

### <a name="powershell-commands"></a>PowerShell-kommandon


Hämta objekt-ID:t för tjänstens huvudnamn.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, programadministratör eller molnprogramadministratör.
2. Välj **Azure Active Directory**  >  **Företagsprogram**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper** och kopiera sedan objekt-ID:t.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Ta bort alla användare som har tilldelats till programmet.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Återkalla behörigheter som beviljats för programmet.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
Ogiltigförklara uppdateringstoken.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Nästa steg
- [Hantera medgivande till program och utvärdera begäran om medgivande](manage-consent-requests.md)
- [Konfigurera användarmedgivande](configure-user-consent.md)
- [Konfigurera arbetsflöde för administratörsmedgivande](configure-admin-consent-workflow.md)
