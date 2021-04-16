---
title: Begränsa åtkomstbehörigheter för gästanvändare – Azure Active Directory | Microsoft Docs
description: Begränsa åtkomstbehörigheter för gästanvändare med Azure Portal, PowerShell eller Microsoft Graph i Azure Active Directory
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
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501509"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Begränsa behörigheter för gäståtkomst (förhandsversion) i Azure Active Directory

Azure Active Directory (Azure AD) kan du begränsa vad externa gästanvändare kan se i organisationen i Azure AD. Gästanvändare anges till en begränsad behörighetsnivå som standard i Azure AD, medan standardvärdet för medlemsanvändare är den fullständiga uppsättningen standardanvändarbehörigheter. Det här är en förhandsversion av en ny behörighetsnivå för gästanvändare i Azure AD-organisationens inställningar för externt samarbete för ännu mer begränsad åtkomst, så dina gäståtkomstalternativ är nu:

Behörighetsnivå         | Åtkomstnivå | Värde
----------------         | ------------ | -----
Samma som medlemsanvändare     | Gäster har samma åtkomst till Azure AD-resurser som medlemsanvändare | a0b1b346-4d3e-4e8b-98f8-753987be4970
Begränsad åtkomst (standard) | Gäster kan se medlemskap i alla icke-dolda grupper | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Begränsad åtkomst (ny)**  | **Gäster kan inte se medlemskap i några grupper** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

När gäståtkomsten är begränsad kan gäster endast visa sin egen användarprofil. Behörighet att visa andra användare tillåts inte även om gästen söker efter användarens huvudnamn eller objectId. Begränsad åtkomst begränsar också gästanvändare från att se medlemskap i grupper som de finns i. Mer information om de övergripande standardanvändarbehörigheterna, inklusive gästanvändarbehörigheter, finns i Vilka är [standardanvändarbehörigheterna i Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Du måste ha rollen Global administratör för att konfigurera inställningarna för externt samarbete. Det finns inga ytterligare licensieringskrav för att begränsa gäståtkomsten.

## <a name="update-in-the-azure-portal"></a>Uppdatera i Azure Portal

Vi har gjort ändringar i de befintliga Azure Portal för gästanvändarbehörigheter.

1. Logga in på [Azure AD-administrationscentret med](https://aad.portal.azure.com) Global administratör behörigheter.
1. På **Azure Active Directory** översiktssidan för din organisation väljer du **Användarinställningar.**
1. Under **Externa användare** väljer du Hantera inställningar för externt **samarbete.**
1. På sidan **Inställningar för externt** samarbete väljer du **Gästanvändaråtkomst är begränsad till egenskaper och medlemskap i deras egna katalogobjekt.**

    ![Inställningssida för externt samarbete i Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Välj **Spara**. Det kan ta upp till 15 minuter innan ändringarna verkställs för gästanvändare.

## <a name="update-with-the-microsoft-graph-api"></a>Uppdatera med MICROSOFT GRAPH-API:et

Vi har lagt till ett nytt Microsoft Graph API för att konfigurera gästbehörigheter i Azure AD-organisationen. Följande API-anrop kan göras för att tilldela valfri behörighetsnivå. Värdet för guestUserRoleId som används här är för att illustrera den mest begränsade gästanvändarinställningen. Mer information om hur du använder Microsoft Graph för att ange gästbehörigheter finns i [authorizationPolicy resource type](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Konfigurera för första gången

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Svaret bör vara Lyckades 204.

### <a name="updating-the-existing-value"></a>Uppdatera det befintliga värdet

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Svaret bör vara Lyckades 204.

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

## <a name="update-with-powershell-cmdlets"></a>Uppdatera med PowerShell-cmdlets

Med den här funktionen har vi lagt till möjligheten att konfigurera begränsade behörigheter via PowerShell v2-cmdlets. Hämta och ange PowerShell-cmdlets har publicerats i version 2.0.2.85.

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

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Ange kommando: Set-AzureADMSAuthorizationPolicy

Exempel:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Du måste ange authorizationPolicy som ID på begäran.

## <a name="supported-microsoft-365-services"></a>Stöd Microsoft 365 tjänster

### <a name="supported-services"></a>Tjänster som stöds

Med stöd menar vi att upplevelsen är som förväntat. mer specifikt att det är samma som den aktuella gästupplevelsen.

- Teams
- Outlook (OWA)
- SharePoint
- Planner i Teams
- Planner-webbapp

### <a name="services-currently-not-supported"></a>Tjänster som för närvarande inte stöds

Tjänsten utan aktuellt stöd kan ha kompatibilitetsproblem med den nya gästbegränsningsinställningen.

- Formulär
- Planner-mobilapp
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Fråga | Svar
-------- | ------
Var gäller dessa behörigheter? | Dessa behörigheter på katalognivå tillämpas i azure AD-tjänster och -portaler, inklusive Microsoft Graph, PowerShell v2, Azure Portal och Mina appar portalen. Microsoft 365 tjänster som utnyttjar Microsoft 365 för samarbetsscenarier påverkas också, särskilt Outlook, Microsoft Teams och SharePoint.
Hur påverkar begränsade behörigheter vilka grupper som gäster kan se? | Oavsett standard- eller begränsad gästbehörighet kan gäster inte räkna upp listan över grupper eller användare. Gäster kan se grupper som de är medlemmar i i både Azure Portal och Mina appar-portalen beroende på behörigheter:<li>**Standardbehörigheter:** För att hitta de grupper som de är medlemmar i i Azure Portal  måste gästen söka efter sitt objekt-ID i listan Alla användare och sedan **välja Grupper.** Här kan de se en lista över grupper som de är medlemmar i, inklusive all gruppinformation, inklusive namn, e-post och så vidare. I Mina appar portal kan de se en lista över grupper som de äger och grupper som de är medlemmar i.</li><li>**Begränsade gästbehörigheter:** I Azure Portal kan de fortfarande hitta listan över grupper som de är medlemmar i genom att söka efter objekt-ID:t i listan Alla användare och sedan välja Grupper. De kan bara se mycket begränsad information om gruppen, särskilt objekt-ID:t. Kolumnerna Namn och E-post är tomma och Grupptyp är okända. I Mina appar-portalen kan de inte komma åt listan över grupper som de äger eller grupper som de är medlemmar i.</li><br>Mer detaljerad jämförelse av de katalogbehörigheter som kommer från Graph API finns i [Standardanvändarbehörigheter.](../fundamentals/users-default-permissions.md#member-and-guest-users)
Vilka delar av Mina appar kommer den här funktionen att påverka? | Gruppfunktionerna i Mina appar portalen respekterar dessa nya behörigheter. Detta inkluderar alla sökvägar för att visa grupplistan och gruppmedlemskap i Mina appar. Inga ändringar har gjorts i grupppanelens tillgänglighet. Tillgängligheten för grupppanelen styrs fortfarande av den befintliga gruppinställningen i Azure Portal.
Åsidosätter dessa behörigheter SharePoint- eller Microsoft Teams-gästinställningar? | Nej. De befintliga inställningarna styr fortfarande upplevelsen och åtkomsten i dessa program. Om du till exempel ser problem i SharePoint kontrollerar du inställningarna för extern delning.
Vilka kända kompatibilitetsproblem finns i Planner och Yammer? | <li>Med behörigheten "begränsad" kan gäster som är inloggade i Planner-mobilappen inte komma åt sina planer eller uppgifter.<li>När behörigheterna är begränsade kan gäster som är inloggade på Yammer inte lämna gruppen.
Kommer mina befintliga gästbehörigheter att ändras i min klient? | Inga ändringar har gjorts i dina aktuella inställningar. Vi bibehåller bakåtkompatibilitet med dina befintliga inställningar. Du bestämmer när du vill göra ändringar.
Kommer dessa behörigheter att anges som standard? | Nej. De befintliga standardbehörigheterna förblir oförändrade. Du kan också ange att behörigheterna ska vara mer restriktiva.
Finns det några licenskrav för den här funktionen? | Nej, det finns inga nya licensieringskrav för den här funktionen.

## <a name="next-steps"></a>Nästa steg

- Mer information om befintliga gästbehörigheter i Azure AD finns i [Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Information om hur du Microsoft Graph API-metoder för att begränsa gäståtkomst finns i [authorizationPolicy resource type (auktoriseringPolicy-resurstyp)](/graph/api/resources/authorizationpolicy)
- Information om hur du återkallar all åtkomst för en användare finns [i Återkalla användaråtkomst i Azure AD](users-revoke-access.md)
