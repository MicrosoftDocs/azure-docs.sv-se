---
title: Hantera åtkomst till appar med Azure AD
description: Beskriver hur Azure Active Directory organisationer kan ange de appar som varje användare har åtkomst till.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: iangithinji
ms.openlocfilehash: 6a1ae7dd1da2c7666c007194bf22bd980e41dc22
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376383"
---
# <a name="managing-access-to-apps"></a>Hantera åtkomst till appar

Kontinuerlig åtkomsthantering, utvärdering av användning och rapportering fortsätter att vara en utmaning när en app har integrerats i organisationens identitetssystem. I många fall måste IT-administratörer eller supporten ha en aktiv roll i hanteringen av åtkomst till dina appar. Ibland utförs tilldelningen av ett allmänt eller avdelnings-IT-team. Ofta är tilldelningsbeslutet avsett att delegeras till affärsbeslutsfattaren, vilket kräver deras godkännande innan IT gör tilldelningen.  Andra organisationer investerar i integrering med ett befintligt automatiserat identitets- och åtkomsthanteringssystem, till exempel Role-Based Access Control (RBAC) eller Attribute-Based Access Control (ABAC). Både integrerings- och regelutvecklingen tenderar att vara specialiserad och dyr. Övervakning eller rapportering om någon av hanteringssätten är en egen separat, kostsam och komplex investering.

## <a name="how-does-azure-active-directory-help"></a>Hur hjälper Azure Active Directory?

Azure AD stöder omfattande åtkomsthantering för konfigurerade program, så att organisationer enkelt kan uppnå rätt åtkomstprinciper, från automatisk, attributbaserad tilldelning (ABAC- eller RBAC-scenarier) till delegering och inklusive administratörshantering. Med Azure AD kan du enkelt uppnå komplexa principer genom att kombinera flera hanteringsmodeller för ett enda program och till och med återanvända hanteringsregler för olika program med samma målgrupper.

Med Azure AD är användnings- och tilldelningsrapportering helt integrerad, vilket gör det möjligt för administratörer att enkelt rapportera om tilldelningstillstånd, tilldelningsfel och till och med användning.

### <a name="assigning-users-and-groups-to-an-app"></a>Tilldela användare och grupper till en app

Azure AD:s programtilldelning fokuserar på två primära tilldelningslägen:

* **Enskild tilldelning** En IT-administratör med behörigheter som global katalogadministratör kan välja enskilda användarkonton och ge dem åtkomst till programmet.

* **Gruppbaserad tilldelning (kräver Azure AD Premium P1 eller P2)** En IT-administratör med behörigheter som global katalogadministratör kan tilldela en grupp till programmet. Specifika användares åtkomst bestäms av om de är medlemmar i gruppen när de försöker komma åt programmet. Med andra ord kan en administratör effektivt skapa en tilldelningsregel som anger "alla aktuella medlemmar i den tilldelade gruppen har åtkomst till programmet". Med det här tilldelningsalternativet kan administratörer dra nytta av något av alternativen för grupphantering i Azure AD, inklusive attributbaserade dynamiska [grupper,](../fundamentals/active-directory-groups-create-azure-portal.md)externa systemgrupper (till exempel lokal Active Directory eller Workday) eller administratörs-hanterade eller självbetjäningshanteringsgrupper. En enda grupp kan enkelt tilldelas till flera appar, och se till att program med tilldelningstillhörighet kan dela tilldelningsregler, vilket minskar den övergripande hanteringskomplexiteten. Observera att kapslade gruppmedlemskap inte stöds för gruppbaserad tilldelning till program för närvarande.

Med dessa två tilldelningslägen kan administratörer uppnå valfri önskvärd tilldelningshanteringsmetod.

### <a name="requiring-user-assignment-for-an-app"></a>Kräva användartilldelning för en app

Med vissa typer av program kan du välja att [kräva att användare tilldelas till programmet](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). På så sätt kan du förhindra att alla loggar in förutom de användare som du uttryckligen tilldelar till programmet. Följande typer av program stöder det här alternativet:

* Program som konfigurerats för federerad enkel inloggning (SSO) med SAML-baserad autentisering
* Programproxy program som använder Azure Active Directory förautentisering
* Program som bygger på Azure AD-programplattformen som använder OAuth 2.0/OpenID Connect Authentication när en användare eller administratör har samtyckt till programmet. Vissa företagsprogram ger ytterligare kontroll över vem som får logga in.

När användartilldelning inte krävs kan otilldelade användare inte se appen på sin Mina appar, men de kan fortfarande logga in på själva programmet (även  kallat SP-initierad  inloggning) eller använda URL:en för användaråtkomst på programmets egenskapssida (även kallat IDP-initierad inloggning). 

För vissa program är alternativet att kräva användartilldelning inte tillgängligt i programmets egenskaper. I dessa fall kan du använda PowerShell för att ange egenskapen appRoleAssignmentRequired för tjänstens huvudnamn.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Fastställa användarupplevelsen för åtkomst till appar

Azure AD erbjuder [flera anpassningsbara sätt att distribuera program](end-user-experiences.md) till slutanvändare i din organisation:

* Azure AD-Mina appar
* Microsoft 365 programstartprogram
* Direkt inloggning till federerade appar (service-pr)
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Du kan avgöra om användare som är tilldelade till en företagsapp kan se den Mina appar Microsoft 365 programstartaren.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exempel: Komplex programtilldelning med Azure AD
Överväg ett program som Salesforce. I många organisationer används Salesforce främst av marknadsförings- och säljteamen. Ofta har medlemmar i marknadsföringsteamet hög privilegierad åtkomst till Salesforce, medan medlemmar i säljteamet har begränsad åtkomst. I många fall har en bred population av informationsarbetare begränsad åtkomst till programmet. Undantag till dessa regler är komplicera. Det är ofta marknadsförings- eller säljledarteamens arbete att bevilja en användare åtkomst eller ändra sina roller oberoende av dessa allmänna regler.

Med Azure AD kan program som Salesforce förkonfigureras för enkel inloggning (SSO) och automatisk etablering. När programmet har konfigurerats kan en administratör vidta en enda åtgärd för att skapa och tilldela lämpliga grupper. I det här exemplet kan en administratör köra följande tilldelningar:

* [Dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md) kan definieras för att automatiskt representera alla medlemmar i marknadsförings- och säljteamen med hjälp av attribut som avdelning eller roll:
  
  * Alla medlemmar i marknadsföringsgrupper tilldelas rollen "marknadsföring" i Salesforce
  * Alla medlemmar i säljteamsgrupper skulle tilldelas till rollen "försäljning" i Salesforce. En ytterligare förfining kan använda flera grupper som representerar regionala säljteam som tilldelats olika Salesforce-roller.

* För att aktivera undantagsmekanismen kan en självbetjäningsgrupp skapas för varje roll. Till exempel kan gruppen "Salesforce marketing exception" skapas som en självbetjäningsgrupp. Gruppen kan tilldelas till Salesforce-marknadsföringsrollen och marknadsföringsledarteamet kan bli ägare. Medlemmar i marknadsföringsledarteamet kan lägga till eller ta bort användare, ange en anslutningsprincip eller till och med godkänna eller neka enskilda användares förfrågningar om att ansluta. Den här mekanismen stöds via en lämplig informationsarbetare som inte kräver specialiserad utbildning för ägare eller medlemmar.

I det här fallet skulle alla tilldelade användare automatiskt etableras till Salesforce, eftersom de läggs till i olika grupper som deras rolltilldelning skulle uppdateras i Salesforce. Användare skulle kunna identifiera och komma åt Salesforce via Mina appar, Office-webbklienter eller till och med genom att gå till organisationens Salesforce-inloggningssida. Administratörer skulle enkelt kunna visa användnings- och tilldelningsstatus med hjälp av Azure AD-rapportering.

Administratörer kan använda [villkorsstyrd åtkomst i Azure AD](../conditional-access/concept-conditional-access-users-groups.md) för att ange åtkomstprinciper för specifika roller. Dessa principer kan omfatta huruvida åtkomst tillåts utanför företagsmiljön och även multifaktorautentisering eller enhetskrav för att uppnå åtkomst i olika fall.

## <a name="access-to-microsoft-applications"></a>Åtkomst till Microsoft-program

Microsoft-program (som Exchange, SharePoint, Yammer osv.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

Det finns tre huvudsakliga sätt som en användare kan få åtkomst till ett program som publicerats av Microsoft.

- För program i Microsoft 365 eller andra betalda paket beviljas  användare åtkomst via licenstilldelning antingen direkt till sitt användarkonto eller via en grupp som använder vår gruppbaserade licenstilldelningsfunktion.
- För program som Microsoft eller en tredje part publicerar fritt för vem som helst kan användare beviljas åtkomst via [användarmedgivande.](configure-user-consent.md) Det innebär att de loggar in på programmet med sitt Azure AD-arbets- eller skolkonto och ger det åtkomst till en begränsad uppsättning data på sitt konto.
- För program som Microsoft eller en tredje part publicerar fritt för vem som helst kan använda kan användare också beviljas åtkomst via [administratörsmedgivande.](manage-consent-requests.md) Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörskonto och beviljar åtkomst till alla i organisationen.

Vissa program kombinerar dessa metoder. Till exempel är vissa Microsoft-program en del av en Microsoft 365 prenumeration, men kräver fortfarande medgivande.

Användare kan komma Microsoft 365 program via sina Office 365-portaler. Du kan också visa eller dölja Microsoft 365 program i Mina appar med [office 365-synlighetsknappen](hide-application-from-user-portal.md) i katalogens **användarinställningar.** 

Precis som med företagsappar kan du tilldela användare till vissa Microsoft-program via Azure Portal eller, om portalalternativet inte är tillgängligt, med hjälp av PowerShell. [](assign-user-or-group-access-portal.md)

## <a name="next-steps"></a>Nästa steg
* [Skydda appar med villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Grupphantering med självbetjäning/SSAA](../enterprise-users/groups-self-service-management.md)
