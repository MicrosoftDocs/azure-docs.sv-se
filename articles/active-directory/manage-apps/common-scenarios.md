---
title: Vanliga program hanterings scenarier för Azure Active Directory | Microsoft Docs
description: Centralisera program hantering med Azure AD
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6372407a5deebefab60e09a9d0c01d977157357b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259578"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralisera program hantering med Azure AD

Lösen ord, både IT-Nightmare och en smärta för anställda över hela världen. Detta är orsaken till att fler och fler företag vänder sig till Azure Active Directory, Microsofts identitets-och åtkomst hanterings lösning för molnet och alla andra resurser. Hoppa från program till program utan att behöva ange ett lösen ord för vart och ett. Hoppa från Outlook, till Workday, till ADP så snabbt som du kan öppna dem på ett snabbt och säkert sätt. Samar beta med partner och till och med andra utanför organisationen utan att behöva anropa det. Dessutom hjälper Azure AD dig att hantera risker genom att skydda appar som du använder med saker som Multi-Factor Authentication för att kontrol lera vem du använder, med kontinuerlig anpassningsbar maskin inlärning och säkerhets information för att identifiera misstänkta inloggningar som ger dig säker åtkomst till de appar du behöver, var du än är. Det är inte bara bra för användare, men även för IT. Med just-in-Time-åtkomst och en full skala styrnings Svit hjälper Azure AD dig att hålla koll på efterlevnaden och även tillämpa principer. Och få detta kan du till och med automatisera etableringen av användar konton, vilket gör åtkomst hantering till en enkelt. Kolla in några vanliga scenarier som kunden använder Azure Active Directory program hanterings funktioner för.

**Vanliga scenarier**


> [!div class="checklist"]
> * SSO för alla dina program
> * Automatisera etablering och avetablering 
> * Skydda dina program
> * Styr åtkomsten till dina program
> * Hybrid säker åtkomst

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: Konfigurera SSO för alla dina program

Det går inte att hantera lösen ord. Få säker åtkomst till alla resurser du behöver med företagets autentiseringsuppgifter. 

|Funktion  | Beskrivning | Rekommendation |
|---------|---------|---------|
|Enkel inloggning|Standardbaserad federerad enkel inloggning med tillförlitliga bransch standarder.|Använd alltid [SAML/OIDC](../develop/v2-howto-app-gallery-listing.md) för att aktivera SSO när ditt program stöder det.|
|Mina appar|Erbjud dina användare en enkel hubb för att upptäcka och komma åt alla sina program. Gör det möjligt för dem att bli mer produktiva med självbetjänings funktioner, t. ex. att begära åtkomst till appar och grupper eller att hantera åtkomst till resurser för andras räkning.| Distribuera [Mina appar](my-apps-deployment-plan.md) i din organisation när du har integrerat dina appar med Azure AD för enkel inloggning.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: automatisera etablering och avetablering 


De flesta program kräver att en användare är etablerad i programmet innan de får åtkomst till de resurser som de behöver. Att använda CSV-filer eller komplexa skript kan vara kostsamt och svårt att hantera. Dessutom måste kunderna se till att kontona tas bort när någon inte längre har åtkomst. Utnyttja verktygen nedan för att automatisera etablering och avetablering. 


|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|SCIM-etablering|[Scim](https://aka.ms/SCIMOverview) är en bästa praxis för automatisering av användar etablering. Alla SCIM-kompatibla program kan integreras med Azure AD. Skapa, uppdatera och ta bort användar konton automatiskt utan att behöva hantera CSV-filer, anpassade skript eller lokal-lösningar.|Kolla in den växande listan över [förintegrerade](../saas-apps/tutorial-list.md) appar i Azure AD App-galleriet|
|Microsoft Graph|Använd inandning och data djupet som Azure AD har för att förbättra ditt program med de data som krävs.|Utnyttja [Microsoft Graph](https://developer.microsoft.com/graph/) för att hämta data från Microsoft-eko systemet. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: skydda dina program
Identiteten är grundbult för säkerhet. Om en identitet blir komprometterad är det otroligt svårt att stoppa Domino-effekt innan den är för sen. I genomsnitt över 100 dagar innan organisationer upptäcker att det har uppstått ett problem. Använd de verktyg som tillhandahålls av Azure AD för att förbättra säkerhets positionen för dina program. 

|Funktion  |Beskrivning| Rekommendation |
|---------|---------| ---------|
|Azure AD MFA|Azure AD Multi-Factor Authentication (MFA) är Microsofts lösning för verifiering av två steg. Med hjälp av godkända autentiseringsmetoder hjälper Azure AD MFA till att skydda åtkomsten till dina data och program samtidigt som du kan uppfylla behovet av en enkel inloggnings process.| [Aktivera MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) för dina användare.  |
|Villkorlig åtkomst|Med villkorlig åtkomst kan du implementera automatiska åtkomst kontroll beslut för vilka som kan komma åt dina molnappar, baserat på villkor.| Granska [säkerhets inställningarna](../fundamentals/concept-fundamentals-security-defaults.md) och de [vanliga principerna](../conditional-access/concept-conditional-access-policy-common.md) som kunderna använder. | 
|Identity Protection|Identitets skydd använder den information som Microsoft har förvärvat från sin ståndpunkt i organisationer med Azure AD, konsument utrymmet med Microsoft-konton och i spel med Xbox för att skydda dina användare. Microsoft analyserar 6 500 000 000 000-signaler per dag för att identifiera och skydda kunder mot hot.|Aktivera [standard principerna för identitets skydd](../identity-protection/concept-identity-protection-policies.md) som tillhandahålls av vår tjänst. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: styr åtkomsten till dina program
Identitets styrning hjälper organisationer att få en balans mellan produktiviteten – hur snabbt kan en person ha åtkomst till de program som de behöver, till exempel när de ansluter till min organisation? Och säkerhet – hur ska deras åtkomst ändras över tid, till exempel på grund av ändringar i personens anställnings status? 

|Funktion  |Beskrivning|Rekommendation |
|---------|---------| ---------|
|GYMNASIUM|Hantering av Azure AD-behörighet kan hjälpa användare både i och utanför organisationen att hantera åtkomst till sina program effektivt.| Tillåt icke-administratörer att hantera åtkomst till sina program med [åtkomst paket](../governance/entitlement-management-access-package-first.md).|
|Åtkomstgranskningar|Användarens åtkomst till appar kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.| [Granska åtkomst](../governance/access-reviews-overview.md) till dina mest känsliga program. |
|Log Analytics|Generera rapporter om vem som har åtkomst till vilka program och lagra dem i ditt SIEM-verktyg som du kan använda för att korrelera data mellan data källor och över tid.| Aktivera [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) och konfigurera aviseringar för kritiska händelser relaterade till dina program. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: hybrid säker åtkomst
Identiteten kan bara vara ditt kontroll plan om den kan ansluta allt över molnet och lokala program. Använd de verktyg som tillhandahålls av Azure AD och dess partner för att skydda åtkomsten till äldre program som är baserade på autentisering.

|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|Programproxy|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De behöver åtkomst till SaaS-appar i molnet och i företags program lokalt. Azure AD Application Proxy ger denna robust åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs).|Konfigurera [fjärråtkomst](./application-proxy.md) för dina lokal-appar. |
|F5, Akamai, Zscaler|Med hjälp av din befintliga nätverks-och leverans kontroll kan du enkelt skydda äldre program som fortfarande är kritiska för dina affärs processer, men som du inte kunde skydda innan Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda programmen.| Använder du Akamai, Citrix, F5 eller Zscaler? Ta en titt på våra färdiga [lösningar](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Relaterade artiklar

- [Programhantering](./index.yml)
- [Programetablering](../app-provisioning/user-provisioning.md)
- [Hybrid säker åtkomst](./secure-hybrid-access.md)
- [Identitetsstyrning](../governance/identity-governance-overview.md)
- [Microsoft identitetsplattform](../develop/v2-overview.md)
- [Identitets säkerhet](../conditional-access/index.yml)