---
title: Skydda extern åtkomst med grupper i Azure Active Directory och Microsoft 365
description: Azure Active Directory och Microsoft 365 grupper kan användas för att öka säkerheten när externa användare kommer åt dina resurser.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e28714e2557027a3f8f5504f7052973a77720b6
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744263"
---
# <a name="securing-external-access-with-groups"></a>Skydda extern åtkomst med grupper 

Grupper är en viktig del av en strategi för åtkomst kontroll. Azure Active Directory (Azure AD) säkerhets grupper och Microsoft 365s grupper (M365) kan användas som grund för att skydda åtkomsten till resurser.

Grupper är det bästa alternativet att använda som grund för följande metoder för åtkomst kontroll:

* Principer för villkorlig åtkomst

* Åtkomst paket för rättighets hantering 

* Åtkomst till M365-resurser, Microsoft Teams och SharePoint-webbplatser

Grupper har följande roller:

* Ägare – grupp ägare hanterar grupp inställningarna och dess medlemskap.

* Medlemmar – medlemmar som ärver behörigheter och åtkomst som har tilldelats till gruppen.

* Gäster – gäster är medlemmar utanför organisationen. 

## <a name="determine-your-group-strategy"></a>Ta reda på din grupp strategi

När du utvecklar din grupp strategi för att skydda extern åtkomst till dina resurser bör du ta hänsyn till [önskad säkerhets position](1-secure-access-posture.md) för att fastställa följande alternativ.

* **Vem ska kunna skapa grupper?** Vill du bara att administratörer ska kunna skapa grupper, eller vill du att anställda och eller externa användare också ska kunna skapa dessa grupper.

   * *Som standard kan alla klient medlemmar skapa Azure AD-säkerhetsgrupper*. 

      * Du kan [begränsa åtkomsten till portalen för icke-administratörer](../develop/howto-restrict-your-app-to-a-set-of-users.md) och inaktivera möjligheten att skapa grupper i [PowerShell.](../users-groups-roles/groups-troubleshooting.md) 

      * Du kan också [Konfigurera grupp hantering via självbetjäning i Azure Active Directory](../users-groups-roles/groups-self-service-management.md). 

   * *Som standard kan alla användare skapa M365 grupper och grupper är öppna för alla (interna och externa) användare i din klient organisation att ansluta till*. 

      * [Du kan begränsa skapandet av Microsoft 365 grupper](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) till medlemmarna i en viss säkerhets grupp. Använd Windows PowerShell för att konfigurera den här inställningen. 

* **Vem ska kunna bjuda in personer till grupper?** Kan alla grupp medlemmar kunna lägga till andra medlemmar, eller så kan endast grupp ägare lägga till medlemmar?

* **Vem kan bjudas in till grupper?** Externa användare kan som standard läggas till i grupper. 

### <a name="assign-users-to-groups"></a>Tilldela användare grupper

Användare kan tilldelas grupper både manuellt baserat på användarattribut i användar objekt, eller på andra kriterier. Användare kan bara tilldelas grupper dynamiskt baserat på deras attribut.

Du kan till exempel tilldela användare till grupper baserat på deras:

* speciell befattning eller avdelning

* partner organisation som de tillhör (manuellt eller genom anslutna organisationer)

* användar typ (medlem eller gäst)

* deltagande i ett särskilt projekt (manuellt)

* location

Dynamiska grupper kan innehålla antingen användare eller enheter, men inte båda. Du lägger till frågor baserat på användarattribut för att tilldela användare till den dynamiska gruppen. Exemplet nedan visar frågor som lägger till användare till gruppen om de är medlemmar (inte gäster) och på ekonomi avdelningen.

![Skärm bild som visar hur du konfigurerar dynamiska medlemskaps regler.](media/secure-external-access/4-dynamic-membership-rules.png)

Mer information om dynamiska grupper finns [i Skapa eller uppdatera en dynamisk grupp i Azure Active Directory.](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Använd inte grupper för flera användnings sätt

När du använder grupper av säkerhets-eller resurs åtkomst, är det viktigt att de har en enda funktion. Om en grupp används för att bevilja åtkomst till resurser, bör den inte användas i något annat syfte. Om en grupp används för allmänna syfte, till exempel för att definiera plats-eller grupp medlemskap, bör den också användas för att skydda åtkomsten. 

Vi rekommenderar en namngivnings konvention för säkerhets grupper som gör syftet tydligt. Exempel:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Typer av grupper

Både Azure AD-säkerhetsgrupper och Microsoft 365 grupper kan skapas från Azure AD-portalen eller M365 administrations Portal. Båda typerna kan användas som grund för att skydda extern åtkomst:

|Överväganden | Azure AD-säkerhetsgrupper (manuellt och dynamiskt)| Microsoft 365 grupper |
| - | - | - |
| Vad kan gruppen innehålla?| Användare<br>Grupper<br>Tjänstprinciper<br>Enheter| Endast användare |
| Var skapas gruppen?| Azure AD-Portal<br>M365 portal (om du vill att e-post ska vara aktive rad)<br>PowerShell<br>Microsoft Graph<br>Slut användar Portal| M365-portalen<br>Azure AD-Portal<br>PowerShell<br>Microsoft Graph<br>I Microsoft 365 program |
| Vem skapar som standard?| Administratörer <br>Slutanvändare| Administratörer<br>Slutanvändare |
| Vem kan läggas till som standard?| Interna användare (medlemmar)| Klient medlemmar och gäster från vilken organisation som helst |
| Vad ger den åtkomst till?| Endast resurser som den är tilldelad till.| Alla grupprelaterade resurser:<br>(Grupp post låda, webbplats, team, chattar och andra inkluderade M365-resurser)<br>Andra resurser till vilken grupp läggs till |
| Kan användas med| Villkorlig åtkomst<br>Hantering av rättigheter<br>Grupp licensiering| Villkorlig åtkomst<br>Hantering av rättigheter<br>Känslighetsetiketter |



Använd Microsoft 365 grupper för att skapa och hantera en uppsättning Microsoft 365 resurser, till exempel ett team och dess associerade webbplatser och innehåll. De är ett bra alternativ för en projekt-baserad ansträngning. 

 

## <a name="azure-ad-security-groups"></a>Azure AD-säkerhetsgrupper 

[Azure AD-säkerhetsgrupper](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) kan innehålla användare eller enheter och kan användas för att hantera åtkomst till 

* Azure-resurser som Microsoft 365 appar, anpassade appar och SaaS-appar (Software as a Service) som ServiceNow för Dropbox.

* Azure-data och-prenumerationer.

* Azure-tjänster.

Azure AD-säkerhetsgrupper kan också användas för att:

* tilldela licenser för tjänster som M365, Dynamics 365 och Enterprise Mobility och Security. Mer information finns i [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

* tilldela förhöjd behörighet. Mer information finns i [använda moln grupper för att hantera roll tilldelningar (för hands version](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)). 

Om du vill skapa en grupp [i Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) navigerar du till Azure Active Directory och sedan till grupper. Du kan också skapa Azure AD-säkerhetsgrupper med hjälp av [PowerShell-cmdletar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets). 

> [!NOTE]
> En säkerhets grupp kan användas för tilldelning av upp till 1500 program, men inte mer. 

![Skärm bild som visar hur du skapar en säkerhets grupp.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Om du vill skapa en e-postaktiverad säkerhets grupp går du till [Microsoft 365 administrations Center](https://admin.microsoft.com/)**. Du kan inte skapa den i Azure AD-portalen. 
<br>Du måste aktivera en säkerhets grupp för e-post vid tidpunkten för skapandet. Du kan inte aktivera det senare.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Hybrid organisationer och Azure AD-säkerhetsgrupper

Hybrid organisationer har både en lokal infrastruktur och en moln infrastruktur i Azure AD. Många hybrid organisationer som använder Active Directory skapa sina säkerhets grupper lokalt och synkronisera dem till molnet. Med den här metoden kan endast användare i den lokala miljön läggas till i säkerhets grupperna.

**Skydda din lokala infrastruktur mot kompromisser, eftersom intrång lokalt kan användas för att få åtkomst till din Microsoft 365-klient**. Vägledning finns i [skydda Microsoft 365 från lokala attacker](https://aka.ms/protectm365) .

## <a name="microsoft-365-groups"></a>Microsoft 365 grupper

[Microsoft 365 grupper](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) är den grundläggande medlemskaps tjänsten som driver all åtkomst över M365. De kan skapas från [Azure Portal](https://portal.azure.com/)eller [M365-portalen](https://admin.microsoft.com/). När en M365-grupp skapas beviljar du åtkomst till en grupp med resurser som används för att samar beta. Se [Översikt över Microsoft 365 grupper för administratörer](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) för en fullständig lista över dessa resurser.

M365-grupper har följande olika delarna för sina roller

* **Ägare** – grupp ägare kan lägga till eller ta bort medlemmar och ha unika behörigheter som möjligheten att ta bort konversationer från den delade Inkorgen eller ändra grupp inställningarna. Grupp ägare kan byta namn på gruppen, uppdatera beskrivningen eller bilden med mera.

* **Medlemmar** – medlemmar kan komma åt allt i gruppen, men kan inte ändra grupp inställningar. Som standard kan grupp medlemmar bjuda in gäster att delta i gruppen, men du kan [kontrol lera den inställningen](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide).

* **Gäster** – grupp gäster är medlemmar som ligger utanför din organisation. Gäster är som standard begränsade till funktioner i team.

 

### <a name="m365-group-settings"></a>Inställningar för M365-grupp

Du väljer e-postalias, sekretess och om du vill aktivera gruppen för team vid tidpunkten för konfiguration. 

![Skärm bild av inställningar för att redigera Microsoft 365 grupper](media/secure-external-access/4-edit-group-settings.png)

Efter installationen lägger du till medlemmar och konfigurerar inställningar för e-postanvändning osv.

### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Fastställ önskad säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använd grupper för säkerhet](4-secure-access-groups.md) (du är här.)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)