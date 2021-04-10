---
title: Begränsa åtkomst behörighet till gäst användare – Azure Active Directory | Microsoft Docs
description: Begränsa åtkomst behörigheter för gäst användare med hjälp av Azure Portal, PowerShell eller Microsoft Graph i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222529"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Begränsa åtkomst behörigheter för gäst (för hands version) i Azure Active Directory

Med Azure Active Directory (Azure AD) kan du begränsa vad externa gäst användare kan se i sin organisation i Azure AD. Gäst användare ställs in på en begränsad behörighets nivå som standard i Azure AD, medan standardvärdet för medlems användare är en fullständig uppsättning standard användar behörigheter. Det här är en förhands granskning av en ny behörighets nivå för gäst användare i Azure AD-organisationens externa samarbets inställningar för ännu mer begränsad åtkomst, så dina gäst åtkomst alternativ är nu:

Behörighetsnivå         | Åtkomstnivå
----------------         | ------------
Samma som medlems användare     | Gäster har samma åtkomst till Azure AD-resurser som medlems användare
Begränsad åtkomst (standard) | Gäster kan se medlemskap i alla icke-dolda grupper
**Begränsad åtkomst (ny)**  | **Gäster kan inte se medlemskap i några grupper**

När gäst åtkomst är begränsad kan gästerna bara se sin egen användar profil. Behörighet att visa andra användare tillåts inte även om gästen söker efter användarens huvud namn eller objectId. Begränsad åtkomst begränsar även gäst användare från att se medlemskapet i de grupper de befinner sig i. Mer information om de övergripande standard användar behörigheterna, inklusive gäst användar behörigheter, finns i [Vad är standard användar behörigheter i Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Du måste vara i rollen global administratör för att konfigurera de externa samarbets inställningarna. Det finns inga ytterligare licensierings krav för att begränsa gäst åtkomsten.

## <a name="update-in-the-azure-portal"></a>Uppdatera i Azure Portal

Vi har gjort ändringar i befintliga Azure Portal-kontroller för gäst användar behörigheter.

1. Logga in på [administrations Center för Azure AD](https://aad.portal.azure.com) med globala administratörs behörigheter.
1. På sidan **Azure Active Directory** översikt för din organisation väljer du **användar inställningar**.
1. Under **externa användare** väljer du **Hantera inställningar för externt samarbete**.
1. På sidan **Inställningar för externt samarbete** väljer du **åtkomst till gäst användare är begränsad till egenskaper och medlemskap för de egna katalog objekt** alternativen.

    ![Sidan för externa samarbets inställningar i Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Välj **Spara**. Det kan ta upp till 15 minuter innan ändringarna börjar gälla för gäst användare.

## <a name="update-with-the-microsoft-graph-api"></a>Uppdatera med Microsoft Graph API

Vi har lagt till ett nytt Microsoft Graph-API för att konfigurera gäst behörigheter i din Azure AD-organisation. Följande API-anrop kan göras för att tilldela alla behörighets nivåer. Värdet för guestUserRoleId som används här är för att illustrera den mest begränsade gäst användar inställningen. Mer information om hur du använder Microsoft Graph för att ange gäst behörigheter finns i [resurs typen authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Konfigurerar för första gången

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Svaret ska vara framgångs rik 204.

### <a name="updating-the-existing-value"></a>Uppdaterar det befintliga värdet

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Svaret ska vara framgångs rik 204.

### <a name="view-the-current-value"></a>Visa det aktuella värdet

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Exempelsvar:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Uppdatera med PowerShell-cmdletar

Med den här funktionen har vi lagt till möjligheten att konfigurera begränsade behörigheter via PowerShell V2-cmdletar. Hämta och ange PowerShell-cmdletar har publicerats i version 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Hämta kommando: Get-AzureADMSAuthorizationPolicy

Exempel:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set-kommando: Set-AzureADMSAuthorizationPolicy

Exempel:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Du måste ange authorizationPolicy som-ID när du begär det.

## <a name="supported-microsoft-365-services"></a>Microsoft 365 tjänster som stöds

### <a name="supported-services"></a>Tjänster som stöds

Med stöd för vi betyder det att upplevelsen är som förväntat. Mer specifikt är det samma som den aktuella gäst upplevelsen.

- Teams
- Outlook (OWA)
- SharePoint
- Planner i Teams
- Webb program för Planner

### <a name="services-currently-not-supported"></a>Tjänster stöds inte för närvarande

Tjänsten utan aktuellt stöd kan ha kompatibilitetsproblem med den nya inställningen för gäst begränsning.

- Formulär
- Planner-mobilapp
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Fråga | Svar
-------- | ------
Var gäller dessa behörigheter? | Dessa behörigheter för katalog nivå tillämpas i Azure AD-tjänster och portaler, inklusive Microsoft Graph, PowerShell V2, Azure Portal och mina apps-portalen. Microsoft 365 tjänster som utnyttjar Microsoft 365 grupper för samarbets scenarier påverkas också, särskilt Outlook, Microsoft Teams och SharePoint.
Hur påverkar begränsade behörigheter vilka grupper gäster kan se? | Gäster kan inte räkna upp listan över grupper eller användare oavsett standard behörighet eller begränsad gäst behörighet. Gäster kan se grupper som de är medlemmar i i både Azure Portal och mina apps-portalen beroende på behörigheter:<li>**Standard behörigheter**: för att hitta de grupper som de är medlemmar av i Azure Portal måste gästen söka efter sitt objekt-ID i listan **alla användare** och sedan välja **grupper**. Här kan de se en lista över grupper som de är medlemmar i, inklusive alla grupp uppgifter, inklusive namn, e-post och så vidare. I portalen Mina appar kan de se en lista över grupper som de äger och grupper som de är medlemmar i.</li><li>**Begränsade gäst behörigheter**: i Azure Portal kan de fortfarande hitta listan över grupper som de är medlemmar i genom att söka efter deras objekt-ID i listan alla användare och sedan välja grupper. De kan bara se mycket begränsad information om gruppen, särskilt objekt-ID. I design är kolumnerna namn och e-post tomma och grupp typen känns inte igen. I portalen Mina appar kan de inte komma åt listan över grupper som de äger eller grupper som de är medlemmar i.</li><br>Mer detaljerad information om de katalog behörigheter som kommer från Graph API finns i [standard användar behörigheter](../fundamentals/users-default-permissions.md#member-and-guest-users).
Vilka delar av portalen för Mina appar påverkar den här funktionen? | Grupp funktionen i portalen Mina appar kommer att respektera dessa nya behörigheter. Detta omfattar alla sökvägar för att Visa grupp listan och grupp medlemskap i Mina appar. Inga ändringar har gjorts i grupp panelens tillgänglighet. Grupp panelens tillgänglighet styrs fortfarande av den befintliga grupp inställningen i Azure Portal.
Åsidosätter dessa behörigheter SharePoint-eller Microsoft Teams gäst inställningar? | Nej. De befintliga inställningarna styr fortfarande upplevelsen och åtkomsten i dessa program. Om du till exempel ser problem i SharePoint, dubbelklickar du på inställningarna för extern delning.
Vilka är kända kompatibilitetsproblem i Planner och Yammer? | <li>Om behörigheterna har angetts till "begränsad" kommer gäster som är inloggade i Planner-mobilappen inte att kunna komma åt sina planer eller aktiviteter.<li>Om behörigheterna har angetts till "begränsad" kan inte gäster som är inloggade i Yammer lämna gruppen.
Kommer mina befintliga gäst behörigheter att ändras i min klient? | Inga ändringar har gjorts i de aktuella inställningarna. Vi bibehåller bakåtkompatibilitet med dina befintliga inställningar. Du bestämmer när du vill göra ändringar.
Kommer de här behörigheterna att anges som standard? | Nej. De befintliga standard behörigheterna förblir oförändrade. Du kan också ange att behörigheterna ska vara mer restriktiva.
Finns det några licens krav för den här funktionen? | Nej, det finns inga nya licensierings krav för den här funktionen.

## <a name="next-steps"></a>Nästa steg

- Mer information om befintliga gäst behörigheter i Azure AD finns i [Vad är standard användar behörigheter i Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Om du vill se Microsoft Graph API-metoder för att begränsa gäst åtkomst, se [resurs typen authorizationPolicy](/graph/api/resources/authorizationpolicy)
- Om du vill återkalla all åtkomst till en användare läser du [återkalla användar åtkomst i Azure AD](users-revoke-access.md)