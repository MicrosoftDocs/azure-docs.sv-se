---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig vad som är nytt med Azure Active Directory. till exempel senaste viktig information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c767d114ad6a1b5cb8b9c6bf4c0fe4f2ccd1dae6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650132"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` i din ![ RSS feed reader-ikon ](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

Den här sidan uppdateras varje månad, så du kan uppdatera den regelbundet. Om du söker efter objekt som är äldre än sex månader kan du hitta dem i [arkivera efter nyheter i Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2021"></a>Januari 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Hemlig token är ett obligatoriskt fält vid konfigurering av etablering

**Typ:** Planera för ändring  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

I det förflutna skulle fältet hemligt token vara tomt när du konfigurerar etableringen för det anpassade/BYOA-programmet. Den här funktionen var avsedd att endast användas för testning. Vi uppdaterar användar gränssnittet för att göra fältet obligatoriskt. 

Kunder kan komma runt detta krav för testning genom att använda en funktions flagga i webbläsarens URL. [Läs mer](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-firstline-workers-at-scale"></a>Offentlig för hands version – anpassa och konfigurera Android-delade enheter för firstline arbetare i stor skala

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Azure AD-och Microsoft Endpoint Manager-grupper har kombinerat för att kunna anpassa, skala och säkra dina firstline-arbetsenheter.

Följande för hands versions funktioner gör att du kan:
- Etablera Android-delade enheter i stor skala med Microsoft Endpoint Manager
- Skydda din åtkomst för Shift-arbetare med enhets-baserad villkorlig åtkomst
- Anpassa inloggnings upplevelser för växla arbetare med hanterad start sida

Mer information finns i [Anpassa och konfigurera delade enheter för firstline-arbetskrafter i stor skala](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Offentlig för hands version – etablerings loggar kan nu hämtas som en CSV eller JSON

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Kunder kan ladda ned etablerings loggar som en CSV-eller JSON-fil via användar gränssnittet och via Graph API. Mer information finns [i etablerings rapporter i Azure Active Directory-portalen](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Offentlig för hands version – tilldela moln grupper till anpassade Azure AD-roller och administrativa enhets omfång roller

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Kunder kan tilldela en moln grupp till anpassade Azure AD-roller eller en administrativ enhets omfattnings roll. Information om hur du använder den här funktionen finns i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Allmän tillgänglighet – Azure AD Connect Cloud Sync (kallades tidigare moln etablering)

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Connect Cloud Sync  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Connect Cloud Sync är nu allmänt tillgänglig för alla kunder.

Azure AD Connect Cloud flyttar den kraftigt avlyftningen av omvandlings logiken till molnet, vilket minskar ditt lokala utrymme. Dessutom finns det flera låg vikts agent distributioner tillgängliga för högre tillgänglighet för synkronisering. [Läs mer](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Allmän tillgänglighet-attack simulerings administratör och angrepps nytto Last redigera inbyggda roller

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Två nya roller i Role-Based Access Control är tillgängliga för att tilldela användare, attack simulerings administratör och angrepps nytto Last. 

Användare i rollen för [attack simulerings administratörer](../roles/permissions-reference.md#attack-simulation-administrator) har åtkomst till alla simuleringar i klienten och kan:
- Skapa och hantera alla aspekter av genereringen av attack simulering
- Starta/schemalägga en simulering
-  granska simulerings resultat. 

Användare i redigerings rollen för [angrepps nytto Last](../roles/permissions-reference.md#attack-payload-author) kan skapa nytto laster, men inte starta eller schemalägga dem. Angrepps nytto lasterna är sedan tillgängliga för alla administratörer i klienten som kan använda dem för att skapa en simulering.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Allmän tillgänglighet-användning Sammanfattning rapporter läsare inbyggd roll

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Användare med rollen användnings sammanfattnings rapport läsare kan komma åt sammanställda data på klient nivå och associerade insikter i Microsoft 365 administrations Center för användning och produktivitets poäng. De kan dock inte komma åt information om användar nivå eller insikter. 

I Microsoft 365 administrations Center för de två rapporterna skiljer vi mellan information om sammanställda data på klient nivå och information på användar nivå. Den här rollen lägger till ett extra skydds lager för enskilda användares identifierbara data. [Läs mer](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Allmän tillgänglighet – Kräv skydds princip för appar i villkorlig åtkomst för Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Azure AD-Grant för villkorlig åtkomst för "Kräv app Protection-princip" är nu GA. 

Principen innehåller följande funktioner:
- Tillåter endast åtkomst när du använder ett mobilt program som har stöd för Intune App Protection
- Tillåter endast åtkomst när en användare har en Intune App Protection-princip som levereras till det mobila programmet

Läs mer om hur du konfigurerar en princip för villkorlig åtkomst för app Protection [här](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Allmän tillgänglighet – e-post One-Time lösen ord

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Eng ång slö sen ord gör det möjligt för organisationer att samar beta med vem som helst genom att skicka en länk eller inbjudan via e-post. Inbjudna användare kan verifiera sin identitet med eng ång slö sen ord som skickas till deras e-post för att få åtkomst till partnerns resurser. [Läs mer](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nya etablerings anslutningar i Azure AD Application Gallery – januari 2021

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Mer information finns i [Vad är automatiserad SaaS app User-etablering i Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – januari 2021

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I januari 2021 har vi lagt till följande 29 nya program i vårt app-galleri med stöd för federation:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [SOVELTO Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[Bienvenue-bildande](https://formation.bienvenue.pro/login), [Aida sjukvård SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO damm](../saas-apps/ephoto-dam-tutorial.md), [begrepp](../saas-apps/notion-tutorial.md), [Syndio](../saas-apps/syndio-tutorial.md) [, Yello, timeclock](../saas-apps/yello-enterprise-tutorial.md) [365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [vakans filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI-tillväxt eko system](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva data säkerhet](../saas-apps/imperva-data-security-tutorial.md), [Illusive nätverk](../saas-apps/illusive-networks-tutorial.md), profiler, [splan](../saas-apps/splan-visitor-tutorial.md) [, Aruba](../saas-apps/proware-tutorial.md) [, Contentsquare](../saas-apps/aruba-user-experience-insight-tutorial.md), burp [SSO](../saas-apps/contentsquare-sso-tutorial.md), [perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här. https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Offentlig för hands version – hanteraren för andra nivån kan anges som en alternativ god kännare

**Typ:** Ändrad funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
Ett extra alternativ när du väljer god kännare är nu tillgängligt i hantering av rättigheter. Om du väljer "chef som god kännare" för den första god kännaren har du ett annat alternativ, "andra nivå hanteraren som alternativ god kännare" tillgänglig för att välja i fältet alternativ god kännare. Om du väljer det här alternativet måste du lägga till en reserv god kännare för att vidarebefordra begäran till om systemet inte kan hitta den andra nivå hanteraren. [Läs mer](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Allmän tillgänglighet – gå till Teams direkt från min åtkomst Portal

**Typ:** Ändrad funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
Nu kan du starta Teams direkt från min åtkomst Portal. 

Det gör du genom att logga in till min åtkomst ( https://myaccess.microsoft.com/) , navigera till "åtkomst paket" och sedan gå till fliken "aktiv" för att se alla åtkomst paket som du redan har åtkomst till. När du expanderar det valda Access-paketet och hovrar över team kan du starta det genom att klicka på öppna. [Läs mer](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Förbättrad loggning & End-User-prompter för riskfyllda gäst användare

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Loggnings-och End-User meddelanden om riskfyllda gäst användare har uppdaterats. Läs mer i [identitets skydd och B2B-användare](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>December 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Offentlig för hands version – Azure AD B2C telefonin loggning och inloggning med hjälp av en inbyggd princip

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
B2C telefonin loggning och inloggning med inbyggd princip gör det möjligt för IT-administratörer och utvecklare av organisationer att tillåta sina slutanvändare att logga in och registrera sig med hjälp av ett telefonnummer i användar flöden. Läs [Konfigurera telefon registrering och inloggning för användar flöden (för hands version)](../../active-directory-b2c/phone-authentication-user-flows.md) om du vill veta mer.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Allmän tillgänglighet – säkerhets inställningar har nu Aktiver ATS för alla nya klienter som standard

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
För att skydda användar konton kommer alla nya klienter som skapats den 12 november 2020 att komma med säkerhets inställningar aktiverade. Säkerhets standarder tillämpar flera principer, inklusive:
- Kräver att alla användare och administratörer registrerar MFA med hjälp av Microsoft Authenticator-appen
- Kräver viktiga administratörs roller för att använda MFA varje gång de loggar in. Alla andra användare uppmanas att använda MFA när det behövs. 
- Äldre autentisering kommer att blockeras för hela klienten. 

Mer information finns i [Vad är säkerhets inställningar?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Allmän tillgänglighet – stöd för grupper med upp till 250 000-medlemmar i AADConnect

**Typ:** Ändrad funktion  
**Tjänste kategori:** AD Connect  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Microsoft har distribuerat en ny slut punkt (API) för Azure AD Connect som förbättrar prestandan för synkroniseringstjänsten i Azure Active Directory. När du använder den nya [v2-slutpunkten](../hybrid/how-to-connect-sync-endpoint-api-v2.md)får du märkbara prestanda vinster vid export och import till Azure AD. Den nya slut punkten stöder följande scenarier:

- Synkronisera grupper med upp till 250 000 medlemmar
- Prestanda vinster vid export och import till Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Allmän tillgänglighet – hantering av rättigheter som är tillgängligt för klienter i Azure Kina-molnet

**Typ:** Ny funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 

Funktionerna för rättighets hantering är nu tillgängliga för alla klienter i Azure Kina-molnet. Mer information finns på webbplatsen för [identitets styrnings dokumentation](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – december 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Bizagi Studio för digital processautomatisering](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut-utskriftshantering i molnet](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – december 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I december 2020 har vi lagt till följande 18 nya program i vårt app-galleri med stöd för federation:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ett juridiskt undantag](https://www.zylab.com/en/product/legal-hold), [Guide](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [Count on-Operations instrument panel](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [Proprofs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd personal Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL](../saas-apps/jll-tririga-tutorial.md)TRIRIGA, [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb brand vägg för webb program](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn personal-lösningar](../saas-apps/linkedin-talent-solutions-tutorial.md), Equinix [Federation app](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Navigera till Team direkt från min åtkomst Portal

**Typ:** Ändrad funktion  
**Tjänste kategori:** Produkt åtkomst hantering **produkt funktion:** rättighets hantering

Nu kan du starta Teams direkt från min åtkomst Portal. Det gör du genom att logga in till [min åtkomst](https://myaccess.microsoft.com/), navigera till **åtkomst paket** och sedan gå till fliken **aktiv** för att se alla åtkomst paket som du redan har åtkomst till. När du expanderar Access-paketet och hovrar på Teams kan du starta det genom att klicka på knappen **Öppna** . 

Om du vill veta mer om hur du använder min åtkomst portal går du till [begär åtkomst till ett Access-paket i hantering av Azure AD-rättigheter](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Offentlig för hands version – hanteraren för andra nivån kan anges som en alternativ god kännare

**Typ:** Ändrad funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter

Ett extra alternativ är nu tillgängligt i godkännande processen i hantering av rättigheter. Om du väljer chef som god kännare för den första god kännaren har du ett annat alternativ, andra nivå hanteraren som alternativ god kännare, tillgänglig för att välja i fältet alternativ god kännare. När du väljer det här alternativet måste du lägga till en reserv god kännare för att vidarebefordra begäran till om systemet inte kan hitta den andra nivå hanteraren.

Mer information finns [i ändra godkännande inställningar för ett Access-paket i hantering av Azure AD-rättigheter](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>November 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 och 3DES-utfasning

**Typ:** Planera för ändring  
**Tjänste kategori:** Alla Azure AD-program  
**Produkt kapacitet:** Standardisering

Azure Active Directory kommer att föråldra följande protokoll i Azure Active Directory globala regioner senast den 30 juni 2021:

- TLS 1.0
- TLS 1.1
- 3DES-chiffrering (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Berörda miljöer är:
- Azures kommersiella moln
- Office 365 GCC och WW

Relaterat meddelande alla kombinationer av klient-och webb läsar servrar bör använda TLS 1,2 och moderna chiffersviter för att upprätthålla en säker anslutning till Azure Active Directory för Azure, Office 365 och Microsoft 365 Services. Detta är en ändring som är relaterad till [Azure Active Directory TLS 1,0 & 1,1 och 3DES-chiffrering i US gov molnet](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – november 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I november 2020 har vi lagt till följande 52 nya program i vårt app-galleri med stöd för federation:

[Rese & utgifts hantering](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [itslearning fil väljare](https://pmteam.itslearning.com/), [Crises-kontroll](../saas-apps/crises-control-tutorial.md), [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum-studie](https://app.studyisland.com/cfw/login/), [Virtual risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview – rekrytering](https://recruit.talview.com/login), real tids översättare, [Klaxoon](https://access.klaxoon.com/login), [podbean](../saas-apps/podbean-tutorial.md), [zcal, EXPENSEMANAGER](https://zcal.co/signup), [netspark-Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [en-trak klient miljö plattform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [Eva check-in](https://my.evacheckin.com/organization), [HowNow webapp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [](../saas-apps/coupa-risk-assess-tutorial.md)kopplings analys, [lucid (alla produkter)](../saas-apps/lucid-tutorial.md), [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), Cequence- [programsäkerhet](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [arbets uppgifter](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [frakt mått](https://www.gpcsl.com/freight.html), [terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [natur Research](../saas-apps/nature-research-tutorial.md), [Play Digital signing](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus, Hirebridge](../saas-apps/prolorus-tutorial.md) [ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Software relationer CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [mentor® av eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/) [](https://api.expense-manager.com/)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Offentlig för hands version – anpassade roller för företags program

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
 [Anpassade RBAC-roller för delegerad hantering av företags program](../roles/custom-available-permissions.md) finns nu i offentlig för hands version. Dessa nya behörigheter bygger på de anpassade rollerna för registrerings hantering av appar, vilket ger detaljerad kontroll över vilken åtkomst dina administratörer har. Med tiden frigörs ytterligare behörigheter för att delegera hantering av Azure AD.

Några vanliga Delegerings scenarier:
- tilldelning av användare och grupper som har åtkomst till SAML-baserade enkla inloggnings program
- Skapa Azure AD Gallery-program
- uppdatering och läsning av grundläggande SAML-konfigurationer för SAML-baserade enkla inloggnings program
- hantering av signerings certifikat för SAML-baserade enkla inloggnings program
- uppdatering av utgående inloggning av certifikat e-postadresser för SAML-baserade enkla inloggnings program
- uppdatering av SAML-token signatur och inloggnings algoritm för SAML-baserade enkla inloggnings program
- skapa, ta bort och uppdatera användarattribut och anspråk för SAML-baserade enkla inloggnings program
- möjlighet att aktivera, inaktivera och starta om etablerings jobb
- uppdateringar av attributmappning
- möjlighet att läsa etablerings inställningar som är associerade med objektet
- möjlighet att läsa etablerings inställningar som är associerade med tjänstens huvud namn
- möjlighet att auktorisera program åtkomst för etablering

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Allmänt tillgänglig för hands version – Azure AD-programproxy har inbyggt stöd för enkel inloggning till program som använder rubriker för autentisering

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control
 
Azure Active Directory (Azure AD) Application Proxy har inbyggt stöd för enkel inloggning till program som använder rubriker för autentisering. Du kan konfigurera huvud värden som krävs av ditt program i Azure AD. Huvud värden skickas till programmet via programproxyn. Mer information finns i [sidhuvud-baserad enkel inloggning för lokala appar med Azure AD App proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Allmän tillgänglighet – Azure AD B2C telefon registrering och inloggning med anpassad princip

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Med telefonnummer för registrering och inloggning kan utvecklare och företag låta sina kunder registrera sig och logga in med ett eng ång slö sen ord som skickas till användarens telefonnummer via SMS. Den här funktionen gör det också möjligt för kunden att ändra sina telefonnummer om de förlorar åtkomsten till sin telefon. Med de anpassade principerna kan utvecklare och företag kommunicera sitt varumärke genom sidan anpassning. Ta reda på hur du [ställer in telefonin loggning och inloggning med anpassade principer i Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – november 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogg](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Luffarschack – TAC mobil](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Mer information finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Allmänt tillgänglig för hands version – e-Sign-In med ProxyAddresses som nu har distribuerats via mellanlagrad distribution

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Klient organisations administratörer kan nu använda stegvis distribution för att distribuera e-Sign-In med ProxyAddresses till vissa Azure AD-grupper. Detta kan hjälpa dig med att testa funktionen innan du distribuerar den till hela klienten via Start sfär identifierings principen. Anvisningar för att distribuera e-Sign-In med ProxyAddresses via mellanlagrad distribution finns i [dokumentationen](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Begränsad förhands granskning-inloggning diagnostik

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Med den första för hands versionen av inloggnings diagnosen kan administratörer nu granska användar inloggningar. Administratörer kan ta emot sammanhangsbaserad, detaljerad och relevant information och vägledning om vad som hände vid en inloggning och hur du kan åtgärda problem. Diagnostiken är tillgänglig både på Azure AD-nivån och den villkorliga åtkomsten diagnostiserar och löser blad. De diagnostiska scenarier som beskrivs i den här versionen är villkorlig åtkomst, Multi-Factor Authentication och lyckad inloggning.

Mer information finns i [Vad är inloggnings diagnostik i Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Förbättrade okända inloggnings egenskaper

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet

  Okända inloggnings egenskaper identifieringar har uppdaterats. Kunder kan lägga märke till att det inte är välkända inloggnings egenskaper för en hög risk. Mer information finns i [Vad är risk?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Offentlig för hands versions uppdatering av Cloud Provisioning agent nu tillgänglig (version: 1.1.281.0)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD Cloud-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Cloud Provisioning agent har lanserats i offentlig för hands version och är nu tillgänglig via portalen. Den här versionen innehåller flera förbättringar, inklusive, stöd för GMSA för dina domäner, vilket ger bättre säkerhet, förbättrade inledande synkroniseringar och stöd för stora grupper. Mer information finns i versions [historiken](../app-provisioning/provisioning-agent-release-version-history.md) . 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>API-slutpunkt för BitLocker-återställningsnyckel under/informationProtection

**Typ:** Ändrad funktion  
**Tjänste kategori:** Enhets åtkomst hantering  
**Produkt kapacitet:** Hantering av enhetens livs cykel
 
Tidigare kunde du återställa BitLocker-nycklar via/BitLocker-slutpunkten. Den här slut punkten kommer att bli föråldrad och kunderna ska börja använda API: et som nu faller under/informationProtection. 

Se [återställnings-API för BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) för uppdateringar i dokumentationen för att avspegla dessa ändringar.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Allmän tillgänglighet för application proxy-stöd för Fjärrskrivbordstjänster HTML5-webbklient

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control
 
Azure AD-programproxy-support för webb klienten Fjärrskrivbordstjänster (RDS) är nu allmänt tillgänglig. Klient webb klienten för fjärr skrivbords tjänster ger användare åtkomst till fjärr skrivbords infrastrukturen via valfri HTLM5 webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome och så vidare. Användare kan interagera med fjärrappar eller skriv bord som de skulle ha en lokal enhet från var som helst. 

Genom att använda Azure AD-programproxy kan du öka säkerheten för din RDS-distribution genom att tvinga Förautentiserings-och villkorliga åtkomst principer för alla typer av rika klient program. Läs mer i [publicera fjärr skrivbord med Azure AD-programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Ny utökad dynamisk grupp tjänst är i offentlig för hands version

**Typ:** Ändrad funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 
Förbättrad dynamisk grupp tjänst är nu i offentlig för hands version. Nya kunder som skapar dynamiska grupper i sina klienter kommer att använda den nya tjänsten. Tiden som krävs för att skapa en dynamisk grupp är proportionerlig till storleken på den grupp som skapas i stället för klientens storlek. Den här uppdateringen förbättrar prestandan för stora klienter avsevärt när kunderna skapar mindre grupper. 

Den nya tjänsten syftar också till att fullständigt lägga till och ta bort medlemmar på grund av attributändringar inom några minuter. Enskilda bearbetnings problem blockerar inte heller klient bearbetningen. Mer information om hur du skapar dynamiska grupper finns i vår [dokumentation](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD lokala hybrid agenter som påverkas av ändringar i Azure TLS-certifikat

**Typ:** Planera för ändring  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Systemet

Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Den här uppdateringen beror på att de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarens forum bas linje. Den här ändringen påverkar Azure AD hybrid-agenter som installerats lokalt och har en fast lista över rot certifikat och måste uppdateras för att lita på nya certifikat utfärdare.

Den här ändringen resulterar i avbrott i tjänsten om du inte omedelbart vidtar åtgärder. Dessa agenter innehåller [programproxy-anslutningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) för fjärråtkomst till lokala, genom [strömnings](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenter som gör att användarna kan logga in på program med samma lösen ord och för [hands versioner av moln](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) tjänster som utför AD till Azure AD Sync. 

Om du har en miljö med brand Väggs regler som tillåter utgående samtal till enbart en speciell lista över återkallade certifikat (CRL) måste du tillåta följande CRL-och OCSP-URL: er. Fullständig information om ändrings-och CRL-och OCSP-URL: er för att ge åtkomst till finns i  [Azure TLS-certifikat ändringar](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Etablerings händelser tas bort från gransknings loggar och publiceras enbart för etablering av loggar

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Aktivitet av SCIM [Provisioning-tjänsten](../app-provisioning/user-provisioning.md) loggas i både gransknings loggar och etablerings loggar. Detta inkluderar aktivitet som att skapa en användare i ServiceNow, grupp i GSuite eller importera en roll från AWS. I framtiden kommer dessa händelser endast att publiceras i etablerings loggarna. Den här ändringen implementeras för att undvika dubbla händelser i loggar och ytterligare kostnader som uppstår av kunder som använder loggarna i Log Analytics. 

Vi ger en uppdatering när ett datum har slutförts. Den här utfasningen har inte planer ATS för kalender året 2020. 

> [!NOTE]
> Detta påverkar inte några händelser i gransknings loggarna utanför de synkroniseringsproblem som genereras av etablerings tjänsten. Händelser som att skapa ett program, en princip för villkorlig åtkomst, en användare i katalogen osv. kommer även fortsättnings vis att genereras i gransknings loggarna. [Läs mer](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD lokala hybrid agenter som påverkas av Azure Transport Layer Security (TLS) certifikat ändringar

**Typ:** Planera för ändring  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Systemet
 
Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Det kommer att finnas en uppdatering på grund av de aktuella CA-certifikaten som inte följer någon av bas linje kraven för CA/webbläsare. Den här ändringen påverkar Azure AD hybrid-agenter som installerats lokalt och har en fast lista över rot certifikat. Dessa agenter måste uppdateras för att lita på nya certifikat utfärdare.

Den här ändringen resulterar i avbrott i tjänsten om du inte omedelbart vidtar åtgärder. Dessa agenter omfattar: 
- [Application Proxy-kopplingar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) för fjärråtkomst till lokalt 
- [Genom strömnings](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenter som gör att användarna kan logga in på program med samma lösen ord
- [Moln etablering för hands versions](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenter som gör AD till Azure AD Sync. 

Om du har en miljö med brand Väggs regler som tillåter utgående samtal till enbart en speciell lista över återkallade certifikat (CRL) måste du tillåta CRL-och OCSP-URL: er. Fullständig information om ändrings-och CRL-och OCSP-URL: er för att ge åtkomst till finns i  [Azure TLS-certifikat ändringar](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 och 3DES-utfasning i US Gov molnet

**Typ:** Planera för ändring  
**Tjänste kategori:** Alla Azure AD-program  
**Produkt kapacitet:** Standardisering
 
Azure Active Directory kommer att föråldra följande protokoll senast den 31 mars 2021:
- TLS 1.0
- TLS 1.1
- 3DES-chiffrering (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Alla kombinationer av klient-och webb läsar servrar bör använda TLS 1,2 och moderna chiffersviter för att upprätthålla en säker anslutning till Azure Active Directory för Azure, Office 365 och Microsoft 365 Services.

Berörda miljöer är:
- Azure-US Gov
- [DoD för Office 365 GCC High &](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Tilldela program till roller i AU och objekt omfång

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Den här funktionen gör det möjligt att tilldela ett program (SPN) till en administratörs roll i det administrativa enhets omfånget. Mer information finns i [tilldela begränsade roller till en administrativ enhet](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Nu kan du inaktivera och ta bort gäst användare när de nekas åtkomst till en resurs

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Identitets styrning
 
Inaktivera och ta bort är en avancerad kontroll av åtkomst granskningar i Azure AD för att hjälpa organisationer att bättre hantera externa gäster i grupper och appar. Om gäster nekas i en åtkomst granskning kommer **inaktivera och ta bort** att automatiskt blockera dem från att logga in i 30 dagar. Efter 30 dagar kommer de att tas bort helt från klient organisationen.

Mer information om den här funktionen finns i [inaktivera och ta bort externa identiteter med åtkomst granskningar för Azure AD (för hands version)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Åtkomst gransknings skapare kan lägga till anpassade meddelanden i e-postmeddelanden till granskare

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Identitets styrning
 
I Azure AD Access-granskningar kan administratörer som skapar recensioner nu skriva ett anpassat meddelande till granskarna. Granskarna ser meddelandet i det e-postmeddelande som de får för att bekräfta granskningen. Mer information om hur du använder den här funktionen finns i steg 14 i avsnittet [skapa en eller flera åtkomst granskningar](../governance/create-access-review.md#create-one-or-more-access-reviews) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – oktober 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integrations assistent för Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Integrerings assistenten (för hands versionen) är nu tillgänglig för Azure AD B2C Appregistreringar. Med den här upplevelsen får du hjälp att konfigurera ditt program för vanliga scenarier. Lär dig mer om [metod tips och rekommendationer för Microsoft Identity Platform](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Visa mall-ID för roll i Azure Portal användar gränssnitt

**Typ:** Ny funktion  
**Tjänste kategori:** Azure-roller  
**Produkt kapacitet:** Access Control
 

Nu kan du Visa mall-ID: t för varje Azure AD-roll i Azure Portal. I Azure AD väljer du  **Beskrivning** av den valda rollen. 

Vi rekommenderar att kunderna använder mall-ID: n i PowerShell-skriptet och kod i stället för visnings namnet. Roll mal len ID stöds för användning av [directoryRoles](/graph/api/resources/directoryrole) -och [roll definitions](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) -objekt. Mer information om roll mal len ID finns i [mall-ID: n](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>API-kopplingar för Azure AD B2C registrering av användar flöden är nu i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 

API-kopplingar är nu tillgängliga för användning med Azure Active Directory B2C. Med API-kopplingar kan du använda webb-API: er för att anpassa dina registrerade användar flöden och integrera med externa moln system. Du kan använda API-kopplingar för att:

- Integrera med arbets flöden för anpassade godkännanden
- Verifiera indata från användaren
- Skriv över användarattribut 
- Kör anpassad affärs logik 

 Besök [use API-anslutningarna för att anpassa och utöka registrerings](../../active-directory-b2c/api-connectors-overview.md) dokumentationen för mer information.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Tillstånds egenskap för anslutna organisationer i hantering av rättigheter

**Typ:** Ny funktion  
**Tjänste kategori:** **Produkt kapacitet** för katalog hantering: hantering av rättigheter
 

 Alla anslutna organisationer har nu ytterligare en egenskap med namnet "State". Statusen styr hur den anslutna organisationen ska användas i principer som refererar till "alla konfigurerade anslutna organisationer". Värdet är antingen "konfigurerat" (vilket innebär att organisationen är inom omfånget för principer som använder "all"-satsen) eller "föreslagen" (vilket innebär att organisationen inte ingår i omfånget).  

Manuellt skapade anslutna organisationer kommer att ha standardinställningen "konfigurerad". Under tiden skapas automatiskt de automatiskt (som skapats via principer som gör att alla användare från Internet kan begära åtkomst) som standard till "föreslaget".  Alla anslutna organisationer som skapats före september 9 2020 ställs in på "konfigurerad". Administratörer kan uppdatera den här egenskapen efter behov. [Läs mer](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory externa identiteter har nu Premium avancerade säkerhets inställningar för B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
De riskfyllda funktionerna för villkorlig åtkomst och riskhantering i identitets skydd är nu tillgängliga i [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Med dessa avancerade säkerhetsfunktioner kan kunderna nu:
- Använd intelligenta insikter för att utvärdera risker med B2C-appar och slutanvändare-konton. Identifieringar omfattar ovanlig resor, anonyma IP-adresser, länkade IP-adresser och Azure AD Threat intelligence. Det finns även Portal-och API-baserade rapporter.
- Hantera risker automatiskt genom att konfigurera principer för anpassad autentisering för B2C-användare. Utvecklare och administratörer kan undvika real tids risker genom att kräva Multi-Factor Authentication (MFA) eller blockera åtkomst beroende på användar risk nivån, med ytterligare kontroller som är tillgängliga baserat på plats, grupp och app.
- Integrera med Azure AD B2C användar flöden och anpassade principer. Villkor kan utlösas från inbyggda användar flöden i Azure AD B2C eller kan införlivas i anpassade principer för B2C. Precis som med andra aspekter av användar flödet för B2C kan slutanvändaren av användar upplevelsen anpassas. Anpassning sker enligt organisationens alternativ för röst, varumärke och minskning.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – oktober 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I oktober 2020 har vi lagt till följande 27 nya program i vårt app-galleri med stöd för federation:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee – produktivitets superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [genera cloud integration för Azure](https://apps.mypurecloud.com/msteams-integration/), [zon teknik Portal](https://portail.zonetechnologie.com/signin), [Beautiful.AI](../saas-apps/beautiful.ai-tutorial.md), [Datawiza åtkomst utjämning](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ECOCHALLENGE.org](https://events.ecochallenge.org/users/login), [AtSpoke](http://atspoke.com/login), [avtalad tid för avtal](https://app.appointmentreminder.co.nz/account/login), [Cloud. marknad](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [hälsnings fras](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Last Air](../saas-apps/web-cargo-air-tutorial.md), [loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail zipline](../saas-apps/retail-zipline-tutorial.md) [, Hoxhunt, MEVISIO](../saas-apps/hoxhunt-tutorial.md) [, Samsara](../saas-apps/mevisio-tutorial.md) [, Nimbus](../saas-apps/samsara-tutorial.md) [,](../saas-apps/nimbus-tutorial.md) [Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Etablerings loggar kan nu strömmas till Log Analytics

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 

Publicera dina etablerings loggar i Log Analytics för att:
- Lagra etablerings loggar i mer än 30 dagar
- Definiera anpassade aviseringar och meddelanden
- Utforma instrument paneler för att visualisera loggarna
- Analysera loggarna genom att köra komplexa frågor 

Information om hur du använder funktionen finns i [förstå hur etablering integreras med Azure Monitor loggar](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Etablerings loggar kan nu visas av program ägare

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Du kan nu tillåta att program ägare övervakar aktivitet från etablerings tjänsten och felsöker problem utan att ge dem en privilegie rad roll eller göra den till en Flask hals. [Läs mer](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Byta namn på 10 Azure Active Directory roller

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure-roller  
**Produkt kapacitet:** Access Control
 
Vissa Azure Active Directory (AD) inbyggda roller har namn som skiljer sig från de som visas i Microsoft 365 administrations Center, Azure AD-portalen och Microsoft Graph. Den här inkonsekvensen kan orsaka problem i automatiserade processer. Med den här uppdateringen byter vi namn på 10 rollnamn så att de blir konsekventa. Följande tabell har de nya roll namnen:

![Tabell med nya roll namn](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C stöd för auth Code Flow för SPAs med MSAL JS 2. x

**Typ:** Ändrad funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
MSAL.js version 2. x har nu stöd för auktoriserings kod flödet för webb program med enkel sida (SPAs). Azure AD B2C kommer nu att ha stöd för användning av typen av SPA-appar på Azure Portal och användningen av MSAL.js auktoriseringskod med PKCE för appar med en sida. Detta gör att SPAs kan använda Azure AD B2C för att upprätthålla SSO med nyare webbläsare och följa senare rekommendationer för autentiseringsprotokoll. Kom igång med att [Registrera en Enkels Ides applikation (Spa) i Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) själv studie kursen.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Uppdateringar för att komma ihåg Multi-Factor Authentication (MFA) på en betrodd enhets inställning

**Typ:** Ändrad funktion  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Vi har nyligen uppdaterat funktionen [kom ihåg Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) på en betrodd enhet för att utöka autentiseringen i upp till 365 dagar. Azure Active Directory (Azure AD) Premium-licenser kan också använda [principen för villkorlig åtkomst – inloggnings frekvens](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) som ger större flexibilitet för inställningarna för omautentisering.

För den bästa användar upplevelsen rekommenderar vi att du använder inloggnings frekvensen för villkorlig åtkomst för att förlänga livs längden för sessioner på betrodda enheter, platser eller låg riskfyllda sessioner som ett alternativ till inställningen kom ihåg MFA på en betrodd enhet. Kom igång genom att läsa vår [senaste vägledning om hur du optimerar återautentiseringen](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – september 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [CODA](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Säkerhets medvetenhet om Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Moln etablering, offentlig för hands versions uppdatering

**Typ:** Ny funktion  
**Tjänste kategori:** **Produkt kapacitet** för Azure AD Cloud-etablering: identitets livs cykel hantering
 
Azure AD Connect moln etablering offentlig för hands version uppdatera funktioner två större förbättringar som har utvecklats från kundfeedback: 

- Attribut mappnings upplevelsen genom Azure Portal

    Med den här funktionen kan IT-administratörer mappa användar-, grupp-eller kontakt-attribut från AD till Azure AD med olika mappnings typer som finns idag. Attributmappning är en funktion som används för att standardisera värdena för attributen som flödar från Active Directory till Azure Active Directory. En kan bestämma om attributvärdet ska mappas direkt som det är från AD till Azure AD eller om du använder uttryck för att transformera attributvärdena när du konfigurerar användare. [Läs mer](../cloud-sync/how-to-attribute-mapping.md)

- Etablering på begäran eller testa användar upplevelsen

    När du har konfigurerat konfigurationen kanske du vill testa för att se om användar omvandlingen fungerar som förväntat innan den tillämpas på alla användare i omfånget. Med etablering på begäran kan IT-administratörer ange unikt namn för en AD-användare och se om de synkroniseras som förväntat. Etablering på begäran är ett bra sätt att se till att de mappningar för attribut som du tidigare fungerade som förväntat. [Läs mer](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Granskad BitLocker-återställning i Azure AD – offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets åtkomst hantering  
**Produkt kapacitet:** Hantering av enhetens livs cykel
 
När IT-administratörer eller slutanvändare läser de BitLocker-återställnings nycklar som de har åtkomst till, skapar Azure Active Directory nu en Gransknings logg som samlar in vem som har åtkomst till återställnings nyckeln. Samma granskning ger information om enheten som BitLocker-nyckeln var kopplad till.

Slutanvändare kan [komma åt sina återställnings nycklar via mitt konto](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). IT-administratörer kan komma åt återställnings nycklar via [nyckel-API: t för BitLocker-återställning i beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) eller via Azure AD-portalen. Mer information finns i [Visa eller kopiera BitLocker-nycklar i Azure AD-portalen](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Den inbyggda rollen team enheters administratör

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Användare med rollen [team enheter administratör](../roles/permissions-reference.md#teams-devices-administrator) kan hantera [team-certifierade enheter](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) från Team administrations centret. 

Med den här rollen kan användaren Visa alla enheter på ett enkelt sätt, med möjlighet att söka efter och filtrera enheter. Användaren kan också kontrol lera information om varje enhet, inklusive inloggat konto och enhetens märke och modell. Användaren kan ändra inställningarna på enheten och uppdatera program varu versionerna. Den här rollen beviljar inte behörigheter för att kontrol lera team aktiviteter och enhetens kvalitet.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Avancerade fråge funktioner för katalog objekt

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö
 
Alla nya fråge funktioner som introducerats för katalog objekt i Azure AD-API: er är nu tillgängliga i 5 1.0-slutpunkten och produktions klara. Utvecklare kan räkna, söka efter, filtrera och sortera katalog objekt och relaterade länkar med hjälp av standard-OData-operatörer.

Mer information finns i dokumentationen [här](https://aka.ms/BlogPostMezzoGA)och du kan också skicka feedback med den här [korta undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Offentlig för hands version: utvärdering av kontinuerlig åtkomst för klienter som har konfigurerat principer för villkorlig åtkomst

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
En utvärdering av kontinuerlig åtkomst (CAE) är nu tillgänglig i offentlig för hands version för Azure AD-klienter med principer för villkorlig åtkomst. Med CAE utvärderas viktiga säkerhets händelser och principer i real tid. Detta inkluderar konto inaktive ring, återställning av lösen ord och plats ändring. Läs mer i utvärderingen av [kontinuerlig åtkomst](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Offentlig för hands version: be användare som begär ett åtkomst paket ytterligare frågor för att förbättra godkännande beslut

**Typ:** Ny funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
Administratörer kan nu kräva att användare som begär ett åtkomst paket svarar på ytterligare frågor utöver bara affärs skäl i Azure AD-hanteringens åtkomst Portal. Användarnas svar visas sedan för god kännare för att hjälpa dem att fatta ett mer exakt beslut om godkännande av åtkomst. Mer information finns i [samla in ytterligare information om begär Ande för godkännande (för hands version)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Offentlig för hands version: förbättrad användar hantering

**Typ:** Ny funktion  
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Användar hantering
 

Azure AD-portalen har uppdaterats för att göra det enklare att hitta användare på sidorna alla användare och borttagna användare. Ändringarna i förhands granskningen är: 
- Mer synliga användar egenskaper, inklusive objekt-ID, status för katalog synkronisering, skapande typ och identitets utfärdare.
- Nu kan du söka i kombinerad sökning efter namn, e-post och objekt-ID: n.
- Utökad filtrering efter användar typ (medlem, gäst och ingen), status för katalog synkronisering, typ av katalog, företags namn och domän namn.
- Nya sorterings funktioner för egenskaper som namn, User Principal Name och borttagnings datum.
- Ett nytt totalt antal användare som uppdaterar med eventuella sökningar eller filter.

Mer information finns i avsnittet om [förbättringar av användar hantering (för hands version) i Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nytt antecknings fält för företags program

**Typ:** Ny funktion  
**Tjänste kategori:** **Produkt kapacitet** för företags appar: SSO

Du kan lägga till kostnads fria text anteckningar i företags program. Du kan lägga till all relevant information som kan hjälpa dig att hantera program i företags program. Mer information finns i [snabb start: konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD)-klient](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – september 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I september 2020 har vi lagt till följande 34 nya program i vårt app-galleri med stöd för federation:

[VMware-Horisont – enhetlig åtkomst-Gateway](), [puls säkra datorer](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData för företag](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions](../saas-apps/cybersolutions-cybermail-tutorial.md)CYBERMAILΣ, [LimbleCMMS](https://auth.limblecmms.com/), [glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [köns lämplighet](https://app.genderfitness.com/), [Coeo-portalen](https://my.coeo.com/), [grammatik](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [Raumfürraum](../saas-apps/raumfurraum-tutorial.md) [, Saviynt,](../saas-apps/saviynt-tutorial.md) [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile LOCKer](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), Protected [Identity & Access Management](https://my.priva.com/), Nitro [Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secure Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Secure Signing Enterprise Portal AAD setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle-skydd med F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån: https://aka.ms/AppsTutorial .

Information om hur du visar ditt program i Azure AD App-galleriet finns här: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Ny Delegerings roll i hantering av Azure AD-rättighets hantering: Access Package Assignment Manager

**Typ:** Ny funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
En ny Access Package Assignment Manager-roll har lagts till i hantering av Azure AD-hantering för att ge detaljerade behörigheter för att hantera tilldelningar. Nu kan du delegera uppgifter till en användare med den här rollen, som kan delegera uppdrags hanteringen av ett Access-paket till en affärs ägare. Men ett Access Package tilldelnings hanterare kan inte ändra åtkomst paket principer eller andra egenskaper som anges av administratörerna. 

Med den nya rollen drar du nytta av de lägsta behörigheterna som krävs för att delegera hantering av tilldelningar och upprätthålla administrativ kontroll för alla andra paket konfigurationer för åtkomst. Läs mer i [rättighets hanterings roller](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Ändringar av Privileged Identity Managementns onboarding-flöde

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Tidigare krävde onboarding till Privileged Identity Management (PIM) användar medgivande och ett onboarding-flöde i PIM-bladet som inkluderade registreringen i Azure AD MFA. Med den senaste integrationen av PIM-upplevelsen i bladet Azure AD-roller och-administratörer tar vi bort den här upplevelsen. Alla innehavare med giltig P2-licens registreras automatiskt i PIM.

Onboarding till PIM har ingen direkt negativ inverkan på din klient. Du kan vänta på följande ändringar:
- Ytterligare tilldelnings alternativ, till exempel aktiva kontra berättigade med start-och slut tid när du gör en tilldelning i något av webbrollerna PIM eller Azure AD och administratörer. 
- Ytterligare omfångs metoder, som administrativa enheter och anpassade roller, introducerades direkt i tilldelnings upplevelsen. 
- Om du är global administratör eller administratör för privilegierade roller kan du börja få ytterligare e-postmeddelanden som PIM veckovis sammandrag. 
- Du kan också se MS-PIM-tjänstens huvud namn i gransknings loggen som är relaterad till roll tilldelningen. Den här förväntade ändringen påverkar inte ditt vanliga arbets flöde.

 Mer information finns i [börja använda Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Hantering av Azure AD-hantering: fönstret Välj för åtkomst paket resurser visas nu som standard de resurser som för närvarande finns i den valda katalogen

**Typ:** Ändrad funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 

I flödet för att skapa åtkomst paket, under fliken resurs roller, ändras beteendet för val fönster. För närvarande är standard beteendet att visa alla resurser som ägs av användaren och resurserna som läggs till i den valda katalogen. 

Den här funktionen kommer att ändras så att endast de resurser som för närvarande läggs till i katalogen visas som standard, så att användarna enkelt kan välja resurser från katalogen. Uppdateringen hjälper till att identifiera resurser som ska läggas till i åtkomst paket och minskar risken för att oavsiktligt lägga till resurser som ägs av användaren som inte är en del av katalogen. Mer information finns i [skapa ett nytt Access-paket i hantering av Azure AD-behörighet](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Augusti 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Uppdateringar av krav för Azure Multi-Factor Authentication-server-brandvägg

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Från och med 1 oktober 2020 kräver Azure MFA Server Firewall-krav ytterligare IP-intervall.

Om du har utgående brand Väggs regler i din organisation uppdaterar du reglerna så att dina MFA-servrar kan kommunicera med alla IP-adressintervall som behövs. IP-intervallen beskrivs i [Azure Multi-Factor Authentication-Server brand Väggs krav](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Kommande ändringar av användar upplevelsen i identitetens säkra Poäng

**Typ:** Planera för ändring  
**Tjänste kategori:** **Produkt kapacitet** för identitets skydd: identitets säkerhet & skydd

Vi uppdaterar portalen för identitetens säkra poäng så att den överensstämmer med de ändringar som introducerades i Microsofts [nya version](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)av säkerhets poängen. 

För hands versionen med ändringarna är tillgänglig i början av september. Ändringarna i för hands versionen är:
- "Identifiera säker Poäng" byter namn till "säkra Poäng för identitet" för varumärkes anpassning med Microsofts säkra Poäng
- Punkter normaliserade till standard skala och rapporteras i procent i stället för punkter

I den här för hands versionen kan kunderna växla mellan den befintliga upplevelsen och den nya upplevelsen. Den här för hands versionen kommer att vara sist i slutet av november 2020. Efter förhands granskningen dirigeras kunderna automatiskt till den nya UX-upplevelsen.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nya begränsade gäst åtkomst behörigheter i Azure AD – offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Access Control   
**Produkt kapacitet:** Användar hantering

Vi har uppdaterat katalog nivå behörigheter för gäst användare. Med dessa behörigheter kan administratörer kräva ytterligare begränsningar och kontroller av åtkomst till externa gäst användare. Administratörer kan nu lägga till ytterligare begränsningar för externa gästers åtkomst till användar-och grupp profil-och medlemskaps information. Med den här offentliga för hands versionen kan kunder hantera externa användares åtkomst i skala genom dölja grupp medlemskap, inklusive att begränsa gäst användare från att se medlemskap i de grupper som de finns i.

Läs mer i [begränsade gäst åtkomst behörigheter](../enterprise-users/users-restrict-guest-permissions.md) och [användares standard behörigheter](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Allmän tillgänglighet för delta frågor för tjänstens huvud namn

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö
 
Microsoft Graph delta fråga stöder nu resurs typen i v 1.0:
- Tjänstens huvudnamn

Nu kan klienter snabbt spåra ändringar i resurserna och tillhandahålla den bästa lösningen för att synkronisera ändringar av dessa resurser med ett lokalt data lager. Information om hur du konfigurerar dessa resurser i en fråga finns i [använda delta fråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Allmän tillgänglighet för delta frågor för oAuth2PermissionGrant

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö

Microsoft Graph delta fråga stöder nu resurs typen i v 1.0:
- OAuth2PermissionGrant

Klienter kan nu spåra ändringar i dessa resurser effektivt och ger den bästa lösningen för att synkronisera ändringar av dessa resurser med ett lokalt data lager. Information om hur du konfigurerar dessa resurser i en fråga finns i [använda delta fråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – augusti 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I augusti 2020 har vi lagt till följande 25 nya program i vårt app-galleri med stöd för federation:

[Backup365](https://portal.backup365.io/login), [tvål](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365-anslutning](http://enlyft.com/), [Serraview för användning av program varu lösningar](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [synlig](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum-kurs program vara exakt sökväg](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira](../saas-apps/altamira-hrm-tutorial.md), WireWheel [, ZIX, GreenLight](../saas-apps/wirewheel-tutorial.md) [efterlevnad och avbildning](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GENETEC företags affärs kontroll plattform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [VeraSMART](https://www.clearance.network/), [Amiko](../saas-apps/isams-tutorial.md), twingate [,](../saas-apps/verasmart-tutorial.md)Scalefusion [,](https://amiko.web.rivero.app/) [Bpanda](https://auth.twingate.com/signup), [tratt](https://nestiolistings.com/sso/oidc/azure/authorize/) [, Vivun, Fortigate](https://scalefusion.com/users/sign_in/), [Wandera](https://goto.bpanda.com/login) [kalender Connect](https://app.vivun.com/dashboard/calendar/connect), [SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md),- [slutanvändare](https://www.wandera.com/)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resurs skogar är nu tillgängliga för Azure AD DS 

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services   
**Produkt kapacitet:** Azure AD Domain Services
 
Funktionen för resurs skogar i Azure AD Domain Services är nu allmänt tillgänglig. Nu kan du aktivera auktorisering utan att lösen ordets hash-synkronisering använder Azure AD Domain Services, inklusive smartkort-auktorisering. Läs mer i [replik uppsättnings koncept och funktioner för Azure Active Directory Domain Services (för hands version)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regional replik stöd för Azure AD DS-hanterade domäner är nu tillgängliga

**Typ:** Ny funktion   
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services
 
Du kan expandera en hanterad domän så att den har fler än en replik uppsättning per Azure AD-klient. Replik uppsättningar kan läggas till i alla peer-kopplat virtuella nätverk i alla Azure-regioner som har stöd för Azure AD Domain Services. Ytterligare replik uppsättningar i olika Azure-regioner ger geografisk haveri beredskap för äldre program om en Azure-region försätts i offlineläge. Läs mer i [replik uppsättnings koncept och funktioner för Azure Active Directory Domain Services (för hands version)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Allmän tillgänglighet för Azure AD My Sign-Ins

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Slut användar upplevelser
 
Azure AD My Sign-Ins är en ny funktion som gör det möjligt för företags användare att granska sin inloggnings historik för att söka efter ovanliga aktiviteter. Dessutom gör den här funktionen att slutanvändarna kan rapportera "det här inte jag" eller "det var jag" på misstänkta aktiviteter. Mer information om hur du använder den här funktionen finns i [Visa och söka efter din senaste inloggnings aktivitet från sidan mina Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR driven användar etablering till Azure AD är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Nu kan du integrera SAP-SuccessFactors som den auktoritativa identitets källan med Azure AD och automatisera hela identitets livs cykeln från slut punkt till slut punkt med hjälp av HR-händelser som nya anställningar och uppsägningar för att driva etablering och avetablering av konton i Azure AD. 

Mer information om hur du konfigurerar SAP SuccessFactors inkommande etablering i Azure AD finns i själv studie kursen [Konfigurera SAP SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Anpassad öppen ID Anslut MS Graph API-stöd för Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Tidigare kunde anpassade öppen ID Connect-providrar endast läggas till eller hanteras via Azure Portal. Nu kan Azure AD B2C-kunder lägga till och hantera dem via Microsoft Graph API: er beta version också. Information om hur du konfigurerar den här resursen med API: er finns i [identityProvider resurs typ](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Tilldela inbyggda Azure AD-roller till moln grupper

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control

Nu kan du tilldela inbyggda Azure AD-roller till moln grupper med den här nya funktionen. Du kan till exempel tilldela rollen SharePoint-administratör till Contoso_SharePoint_Admins grupp. Du kan också använda PIM för att göra gruppen till en behörig medlem i rollen, i stället för att bevilja ständig åtkomst. Information om hur du konfigurerar den här funktionen finns i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Den inbyggda rollen insikter för affärs ledare finns nu tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Användare i rollen insikter affärs ledare kan komma åt en uppsättning instrument paneler och insikter via [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Detta omfattar fullständig åtkomst till alla instrument paneler och visade insikter och data utforsknings funktioner. Användare i den här rollen har dock inte åtkomst till produkt konfigurations inställningar, vilket är ansvaret för rollen insikter-administratör. Mer information om den här rollen finns [i administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Den inbyggda rollen insikts administratör finns nu tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Användare i rollen insikter-administratör kan komma åt fullständig uppsättning administrativa funktioner i [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). En användare med den här rollen kan läsa katalog information, övervaka tjänstens hälsa, stöd för fil support och få åtkomst till inställningarna för Insights-administratörer. Mer information om den här rollen finns [i administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Program administratör och moln program administratör kan hantera tilläggs egenskaper för program

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Tidigare kunde endast den globala administratören hantera [tilläggs egenskapen](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Nu är det möjligt att aktivera den här funktionen för program administratören och moln program administratören.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 Hotfix 4.6.263.0 och kopplingar 1.1.1301.0

**Typ:** Ändrad funktion  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel

Det finns ett [samlat snabb korrigerings paket (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här samlade paketet innehåller uppdateringar för MIM CM, MIM Synchronization Manager och PAM-komponenter. Dessutom innehåller MIM Generic Connectors 1.1.1301.0 uppdateringar för graf Connector.

---
