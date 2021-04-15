---
title: Vanliga programhanteringsscenarier för Azure Active Directory | Microsoft Docs
description: Centralisera programhantering med Azure AD
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: overview
ms.date: 03/02/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acfff8a9152c767b61463bed0165d5ae390f649
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375006"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralisera programhantering med Azure AD

Lösenord, både en IT-värsta och en ont för anställda över hela världen. Det är därför allt fler företag vänder sig till Azure Active Directory, Microsofts lösning för identitets- och åtkomsthantering för molnet och alla dina andra resurser. Hoppa från program till program utan att behöva ange ett lösenord för var och en. Hoppa från Outlook till Workday till ADP så snabbt som du kan öppna dem, snabbt och säkert. Samarbeta sedan med partner och även andra utanför organisationen utan att behöva ringa IT. Dessutom hjälper Azure AD till att hantera risker genom att skydda de appar som du använder med till exempel multifaktorautentisering för att verifiera vem du är, med hjälp av kontinuerlig anpassningsbar maskininlärning och säkerhetsintelligens för att identifiera misstänkta inloggningar som ger dig säker åtkomst till de appar du behöver, oavsett var du befinner dig. Det är inte bara bra för användarna, utan även för IT. Med just-in-time-åtkomstgranskningar och en fullskalig styrningssvit hjälper Azure AD dig att hålla dig i efterlevnads- och framtvinga principer. Du kan också automatisera etableringen av användarkonton, vilket gör åtkomsthanteringen enkel. Kolla in några av de vanliga scenarier som kunden använder Azure Active Directory programhanteringsfunktioner för.

**Vanliga scenarier**


> [!div class="checklist"]
> * Enkel inloggning för alla dina program
> * Automatisera etablering och avetablering 
> * Skydda dina program
> * Styra åtkomsten till dina program
> * Säker hybridåtkomst

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: Konfigurera enkel inloggning för alla dina program

Ingen mer hantering av lösenord. Få säker åtkomst till alla resurser du behöver med företagets autentiseringsuppgifter. 

|Funktion  | Beskrivning | Rekommendation |
|---------|---------|---------|
|Enkel inloggning|Standardbaserad federerad enkel inloggning med betrodda branschstandarder.|Använd alltid [SAML/OIDC för](../develop/v2-howto-app-gallery-listing.md) att aktivera enkel inloggning när ditt program stöder det.|
|Mina appar|Erbjuda användarna en enkel hubb för att identifiera och komma åt alla sina program. Gör det möjligt för dem att vara mer produktiva med självbetjäningsfunktioner, t.ex. att begära åtkomst till appar och grupper eller hantera åtkomst till resurser för andras räkning.| Distribuera [Mina appar](my-apps-deployment-plan.md) i din organisation när du har integrerat dina appar med Azure AD för enkel inloggning.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Automatisera etablering och avetablering 


De flesta program kräver att en användare etableras i programmet innan de får åtkomst till de resurser som de behöver. Det kan vara dyrt och svårt att hantera csv-filer eller komplexa skript. Dessutom måste kunderna se till att konton tas bort när någon inte längre ska ha åtkomst. Använd verktygen nedan för att automatisera etablering och avetablering. 


|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|SCIM-etablering|[SCIM](https://aka.ms/SCIMOverview) är bästa praxis för att automatisera användareablering. Alla SCIM-kompatibla program kan integreras med Azure AD. Skapa, uppdatera och ta bort användarkonton automatiskt utan att behöva underhålla CSV-filer, anpassade skript eller lokala lösningar.|Kolla in den växande listan över [förintegrerade](../saas-apps/tutorial-list.md) appar i Azure AD-appgalleriet|
|Microsoft Graph|Utnyttja de data som Azure AD behöver för att utöka ditt program med de data som behövs.|Använd [Microsoft-diagrammet](https://developer.microsoft.com/graph/) för att hämta data från Microsofts ekosystem. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Skydda dina program
Identitet är linchpin för säkerhet. Om en identitet komprometteras är det otroligt svårt att stoppa dominoeffekten innan den är för sent. I genomsnitt passerar mer än 100 dagar innan organisationer upptäcker att det har blivit en kompromiss. Använd de verktyg som tillhandahålls av Azure AD för att förbättra dina programs säkerhetsstatus. 

|Funktion  |Beskrivning| Rekommendation |
|---------|---------| ---------|
|Azure AD MFA|Azure AD Multi-Factor Authentication (MFA) är Microsofts tvåstegsverifieringslösning. Med hjälp av administratörsgodkända autentiseringsmetoder hjälper Azure AD MFA till att skydda åtkomsten till dina data och program samtidigt som behovet av en enkel inloggningsprocess kan uppfyllas.| [Aktivera MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) för dina användare.  |
|Villkorlig åtkomst|Med villkorsstyrd åtkomst kan du implementera automatiska beslut om åtkomstkontroll för vem som kan komma åt dina molnappar, baserat på villkor.| Granska [standardinställningarna för säkerhet](../fundamentals/concept-fundamentals-security-defaults.md) [och vanliga principer](../conditional-access/concept-conditional-access-policy-common.md) som kunder använder. | 
|Identity Protection|Identity Protection använder de lärdomar som Microsoft har fått från sin position i organisationer med Azure AD, konsumentutrymmet med Microsoft-konton och spel med Xbox för att skydda dina användare. Microsoft analyserar 6,5 biljoner signaler per dag för att identifiera och skydda kunder mot hot.|Aktivera de [standardprinciper för identitetsskydd](../identity-protection/concept-identity-protection-policies.md) som tillhandahålls av vår tjänst. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: Styra åtkomsten till dina program
Identitetsstyrning hjälper organisationer att uppnå en balans mellan produktivitet – Hur snabbt kan en person ha åtkomst till de program som de behöver, till exempel när de går med i min organisation? Och säkerhet – Hur bör deras åtkomst ändras med tiden, till exempel på grund av ändringar i personens anställningsstatus? 

|Funktion  |Beskrivning|Rekommendation |
|---------|---------| ---------|
|Elm|Azure AD-rättighetshantering kan hjälpa användare både inom och utanför organisationen att effektivt hantera åtkomsten till sina program.| Tillåt icke-administratörer att hantera åtkomst till sina program med [åtkomstpaket](../governance/entitlement-management-access-package-first.md).|
|Åtkomstgranskningar|Användarens åtkomst till appar kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.| [Granska åtkomsten](../governance/access-reviews-overview.md) till dina mest känsliga program. |
|Log Analytics|Generera rapporter om vem som har åtkomst till vilka program och lagra dem i valbara SIEM-verktyget för att korrelera data mellan datakällor och över tid.| Aktivera [logganalys](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) och konfigurera aviseringar för kritiska händelser relaterade till dina program. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Säker hybridåtkomst
Identiteten kan bara vara ditt kontrollplan om den kan ansluta allt i molnprogram och lokala program. Utnyttja de verktyg som tillhandahålls av Azure AD och dess partner för att skydda åtkomsten till äldre autentiseringsbaserade program.

|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|Programproxy|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De måste ha åtkomst till SaaS-appar i molnet och företagsappar lokalt. Azure AD-programproxy ger den här robusta åtkomsten utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZs).|Konfigurera [fjärråtkomst](./application-proxy.md) för dina appar på plats. |
|F5, Akamai, Zscaler|Med din befintliga nätverks- och leveranskontrollant kan du enkelt skydda äldre program som fortfarande är viktiga för dina affärsprocesser, men som du inte kunde skydda tidigare med Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda dessa program.| Använder du Akamai, Citrix, F5 eller Zscaler? Kolla in [våra färdiga lösningar.](./secure-hybrid-access.md) | 

## <a name="related-articles"></a>Relaterade artiklar

- [Programhantering](./index.yml)
- [Programetablering](../app-provisioning/user-provisioning.md)
- [Säker hybridåtkomst](./secure-hybrid-access.md)
- [Identitetsstyrning](../governance/identity-governance-overview.md)
- [Microsoft identitetsplattform](../develop/v2-overview.md)
- [Identitetssäkerhet](../conditional-access/index.yml)