---
title: Hantera användar-och administratörs behörigheter – Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar och hanterar behörigheter för programmet i Azure AD. Återkalla till exempel alla behörigheter som beviljats till ett program.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95e13cedc0cdbaedc8c00b9d855057da7e631c19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510886"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Vidta åtgärder för överanvändning eller misstänkta program i Azure Active Directory

Lär dig hur du granskar och hanterar program behörigheter. Den här artikeln innehåller olika åtgärder som du kan vidta för att skydda ditt program enligt scenariot. Dessa åtgärder gäller för alla program som har lagts till i din Azure Active Directory (Azure AD)-klient via användare eller administrativt medgivande.

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Krav

Om du vill utföra följande åtgärder måste du logga in som global administratör, program administratör eller en moln program administratör.

Om du vill begränsa åtkomsten till program måste du kräva användar tilldelning och sedan tilldela användare eller grupper till programmet.  Mer information finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

Du kan komma åt Azure AD-portalen för att hämta sammanhangsbaserade PowerShell-skript för att utföra åtgärderna.
 
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **behörigheter**. I kommando fältet väljer du **gransknings behörigheter**.

![Skärm bild av fönstret gransknings behörigheter.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Kontrol lera åtkomst till ett program

Vi rekommenderar att du begränsar åtkomsten till programmet genom att aktivera inställningen för **användar tilldelning** .

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper**och ange sedan **användar krav som krävs** till **Ja**.
5. Välj **användare och grupper**och ta sedan bort de oönskade användare som har tilldelats programmet.
6. Tilldela användare eller grupper till programmet.

Du kan också ta bort alla användare som är tilldelade till programmet med hjälp av PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Återkalla alla behörigheter för ett program

Om du använder PowerShell-skriptet återkallar du alla behörigheter som har beviljats för programmet.

> [!NOTE]
> Om du återkallar den aktuella beviljade behörigheten kommer användarna inte att hindra användare från att godkänna programmet på nytt. Om du vill blockera användare från att godkänna, Läs [Konfigurera hur användare godkänner program](configure-user-consent.md).

Du kan också inaktivera programmet för att hindra användare från att komma åt appen och hindra programmet från att komma åt dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper**och Ställ in **aktiverat för användare för inloggning?** till **Nej**.

## <a name="investigate-a-suspicious-application"></a>Undersök ett misstänkt program

Vi rekommenderar att du begränsar åtkomsten till programmet genom att aktivera inställningen för **användar tilldelning** . Granska sedan de behörigheter som användare och administratörer har beviljat programmet.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
3. Välj **Azure Active Directory**  >  **företags program**.
5. Välj det program som du vill begränsa åtkomsten till.
6. Välj **Egenskaper**och ange sedan **användar krav som krävs** till **Ja**.
7. Välj **behörigheter**och granska administratörs-och användarens behörigheter.

Du kan också använda PowerShell för att:

- Ta bort alla tilldelade användare för att hindra dem från att logga in i programmet.
- Invalidera uppdateringstoken för användare som har åtkomst till programmet.
- Återkalla alla behörigheter för programmet.

Du kan också inaktivera programmet för att blockera användarnas åtkomst och stoppa programmets åtkomst till dina data.


## <a name="disable-a-malicious-application"></a>Inaktivera ett skadligt program 

Vi rekommenderar att du inaktiverar programmet för att blockera användarnas åtkomst och förhindrar att programmet kommer åt dina data. Om du tar bort programmet i stället kan användare godkänna programmet igen och bevilja åtkomst till dina data.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper**och kopiera sedan objekt-ID: t.

### <a name="powershell-commands"></a>PowerShell-kommandon


Hämta objekt-ID för tjänstens huvud namn.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Välj **Azure Active Directory**  >  **företags program**.
3. Välj det program som du vill begränsa åtkomsten till.
4. Välj **Egenskaper**och kopiera sedan objekt-ID: t.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Ta bort alla användare som är tilldelade till programmet.
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

Återkalla behörigheter som har beviljats för programmet.

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
Ogiltig uppdatering av tokens.
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
- [Hantera medgivande till program och utvärdera medgivande förfrågan](manage-consent-requests.md)
- [Konfigurera användarmedgivande](configure-user-consent.md)
- [Konfigurera arbets flöde för administratörs medgivande](configure-admin-consent-workflow.md)
