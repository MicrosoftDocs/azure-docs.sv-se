---
title: Hantera åtkomst till appar med hjälp av Azure AD
description: Beskriver hur Azure Active Directory gör det möjligt för organisationer att ange de appar som varje användare har åtkomst till.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 5afc6aa8f52011eba6d7cfdfaa09b0ab995183e8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257431"
---
# <a name="managing-access-to-apps"></a>Hantera åtkomst till appar

Pågående åtkomst hantering, användnings utvärdering och rapportering fortsätter att vara en utmaning när en app har integrerats i din organisations identitets system. I många fall måste IT-administratörer eller supportavdelningen ta en pågående aktiv roll vid hantering av åtkomst till dina appar. Ibland utförs tilldelningen av ett allmänt eller divisions IT-team. Ofta är tilldelnings beslutet avsett att delegeras till affärs besluts fattaren, vilket kräver godkännande innan tilldelningen görs.  Andra organisationer investerar i integrering med ett befintligt automatiserat identitets-och åtkomst hanterings system, t. ex. Role-Based Access Control (RBAC) eller Attribute-Based Access Control (ABAC). Både integrerings-och regel utvecklingen tenderar att vara specialiserade och dyra. Övervakning eller rapportering av antingen hanterings metoden är en egen separat, kostsam och komplex investering.

## <a name="how-does-azure-active-directory-help"></a>Hur hjälper Azure Active Directory?

Azure AD har stöd för omfattande åtkomst hantering för konfigurerade program, vilket gör det möjligt för organisationer att enkelt uppnå rätt åtkomst principer som sträcker sig från automatisk, filbaserad tilldelning (ABAC eller RBAC-scenarier) genom delegering och inklusive administratörs hantering. Med Azure AD kan du enkelt få komplexa principer, kombinera flera hanterings modeller för ett enda program och kan till och med återanvända hanterings regler i alla program med samma mål grupper.

Med Azure AD är rapportering av användning och tilldelning helt integrerat, vilket gör det möjligt för administratörer att enkelt rapportera om tilldelnings status, tilldelnings fel och till och med användning.

### <a name="assigning-users-and-groups-to-an-app"></a>Tilldela användare och grupper till en app

Program tilldelningen i Azure AD fokuserar på två primära tilldelnings lägen:

* **Enskild tilldelning** En IT-administratör med global administratörs behörighet för katalog kan välja enskilda användar konton och ge dem åtkomst till programmet.

* **Gruppbaserad tilldelning (kräver Azure AD Premium P1 eller P2)** En IT-administratör med katalogens globala administratörs behörighet kan tilldela en grupp till programmet. Specifika användares åtkomst bestäms av huruvida de är medlemmar i gruppen vid den tidpunkt då de försöker få åtkomst till programmet. Med andra ord kan en administratör effektivt skapa en tilldelnings regel som anger "alla aktuella medlemmar i den tilldelade gruppen har åtkomst till programmet". Med det här tilldelnings alternativet kan administratörer ha nytta av något av alternativen för Azure AD-grupphantering, inklusive [attributbaserade dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md), externa system grupper (t. ex. lokala Active Directory eller arbets dagar) eller grupper som hanteras av en administratör eller självbetjänings grupp. En enda grupp kan enkelt tilldelas till flera appar, och se till att program med tilldelnings tillhörighet kan dela tilldelnings regler, vilket minskar den övergripande hanterings komplexiteten. Observera att kapslade grupp medlemskap inte stöds för gruppbaserad tilldelning till program för tillfället.

Med de här två tilldelnings lägena kan administratörer uppnå lämplig metod för tilldelnings hantering.

### <a name="requiring-user-assignment-for-an-app"></a>Kräva användar tilldelning för en app

Med vissa typer av program kan du välja att kräva att [användarna ska tilldelas till programmet](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). På så sätt förhindrar du att alla loggar in förutom de användare som du uttryckligen tilldelar programmet. Följande typer av program stöder det här alternativet:

* Program som kon figurer ATS för federerad enkel inloggning (SSO) med SAML-baserad autentisering
* Program för programproxy som använder Azure Active Directory förautentisering
* Program som bygger på Azure AD-programplattformen som använder OAuth 2,0/OpenID Connect-autentisering när en användare eller administratör har samtyckt till det programmet. Vissa företags program ger ytterligare kontroll över vem som tillåts att logga in.

När användar tilldelning *inte krävs* ser inte otilldelade användare appen i sina appar, men de kan fortfarande logga in i själva programmet (även kallat SP-initierad inloggning) eller använda **URL: en för användar åtkomst** på programmets **egenskaps** sida (kallas även IDP inloggning).

För vissa program är alternativet för att kräva användar tilldelning inte tillgängligt i programmets egenskaper. I dessa fall kan du använda PowerShell för att ange egenskapen appRoleAssignmentRequired för tjänstens huvud namn.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Bestämma användar upplevelsen för åtkomst till appar

Azure AD tillhandahåller [flera anpassningsbara sätt att distribuera program](end-user-experiences.md) till slutanvändare i din organisation:

* Mina appar i Azure AD
* Microsoft 365 program start
* Direkt inloggning till federerade appar (service – PR)
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Du kan avgöra om användare som är tilldelade till en företags app kan se den i Mina appar och Microsoft 365 programmet starta.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exempel: komplex program tilldelning med Azure AD
Överväg ett program som Salesforce. I många organisationer används Salesforce främst av marknadsförings-och Sälj teamen. Ofta har medlemmar i marknadsförings teamet hög privilegie rad åtkomst till Salesforce, medan medlemmar i Sälj teamet har begränsad åtkomst. I många fall har en bred population av informations anställda begränsad åtkomst till programmet. Undantag till de här reglerna är att det är en komplicerad angelägenhet. Det är ofta förmåns gruppen för marknads organisations-eller Sälj organisations team för att ge en användare åtkomst eller ändra sina roller oberoende av dessa allmänna regler.

Med Azure AD kan program som Salesforce vara förkonfigurerade för enkel inloggning (SSO) och automatisk etablering. När programmet har kon figurer ATS kan en administratör vidta en engångs åtgärd för att skapa och tilldela lämpliga grupper. I det här exemplet kan en administratör köra följande tilldelningar:

* [Dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md) kan definieras för att automatiskt representera alla medlemmar i marknadsförings-och försäljnings teamen med attribut som avdelning eller roll:
  
  * Alla medlemmar i marknadsförings grupper tilldelas rollen "marknadsföring" i Salesforce
  * Alla medlemmar i Sälj grupps grupper tilldelas rollen "försäljning" i Salesforce. En ytterligare förfining kan använda flera grupper som representerar regionala försäljnings team som tilldelats till olika Salesforce-roller.

* Om du vill aktivera undantags funktionen kan en självbetjänings grupp skapas för varje roll. Till exempel kan gruppen "Salesforce Marketing Exception" skapas som en självbetjänings grupp. Gruppen kan tilldelas till Salesforce-marknadsförings rollen och marknads ledande laget kan göras ägare. Medlemmar i marknads ledningen kan lägga till eller ta bort användare, ange en kopplings princip eller till och med godkänna eller neka enskilda användares begär Anden att ansluta. Den här mekanismen stöds via en informations anställds lämpligaste upplevelse som inte kräver särskild utbildning för ägare eller medlemmar.

I det här fallet är alla tilldelade användare automatiskt etablerade i Salesforce, eftersom de läggs till i olika grupper. roll tilldelningen uppdateras i Salesforce. Användare skulle kunna identifiera och få åtkomst till Salesforce via Mina appar, Office-webbklienter eller till och med genom att gå till deras organisations inloggnings sida. Administratörer kan enkelt Visa användnings-och tilldelnings status med hjälp av Azure AD repor ting.

Administratörer kan använda [villkorlig Azure AD-åtkomst](../conditional-access/concept-conditional-access-users-groups.md) för att ange åtkomst principer för särskilda roller. Dessa principer kan omfatta om åtkomst tillåts utanför företags miljön och till och med Multi-Factor Authentication-eller enhets krav för att få åtkomst i olika fall.

## <a name="access-to-microsoft-applications"></a>Åtkomst till Microsoft-program

Microsoft-program (t. ex. Exchange, SharePoint, Yammer osv.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

En användare kan få till gång till ett Microsoft-publicerat program på tre sätt.

- För program i Microsoft 365 eller andra betalda sviter beviljas användare åtkomst via **licens tilldelning** antingen direkt till sitt användar konto eller via en grupp som använder vår gruppbaserade licens tilldelnings funktion.
- För program som Microsoft eller en tredje part publicerar fritt för alla som ska användas kan användare beviljas åtkomst genom [användar medgivande](configure-user-consent.md). Det innebär att de loggar in på programmet med sitt Azure AD-arbets-eller skol konto och ger åtkomst till viss begränsad uppsättning data på sitt konto.
- För program som Microsoft eller en tredje part publicerar fritt för alla som ska användas kan användare även beviljas åtkomst genom [Administratörs medgivande](manage-consent-requests.md). Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörs konto och ger åtkomst till alla i organisationen.

Vissa program kombinerar dessa metoder. Vissa Microsoft-program ingår exempelvis i en Microsoft 365-prenumeration, men kräver fortfarande medgivande.

Användare kan komma åt Microsoft 365 program via deras Office 365-portaler. Du kan också visa eller dölja Microsoft 365 program i Mina appar med [Office 365-synlighet växla](hide-application-from-user-portal.md) i katalogens **användar inställningar**. 

Precis som med företags program kan du [tilldela användare](assign-user-or-group-access-portal.md) till vissa Microsoft-program via Azure Portal eller, om Portal alternativet inte är tillgängligt, med hjälp av PowerShell.

## <a name="next-steps"></a>Nästa steg
* [Skydda appar med villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Grupp hantering för självbetjäning/SSAA](../enterprise-users/groups-self-service-management.md)
