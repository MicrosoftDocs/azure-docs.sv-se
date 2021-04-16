---
title: Nyheter Information – Azure Active Directory | Microsoft Docs
description: Lär dig vad som är nytt med Azure Active Directory; till exempel den senaste viktiga informationen, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar.
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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532308"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Få ett meddelande om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL:en: i `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ rss-feedens ](./media/whats-new/feed-icon-16x16.png) läsare-läsare.

Azure AD tar kontinuerligt emot förbättringar. Den här artikeln innehåller information om hur du håller dig uppdaterad med den senaste utvecklingen:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader hittar du dem i Arkiv för nyheter [i Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2021"></a>Mars 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Vägledning om hur du aktiverar stöd för TLS 1.2 i din miljö inför kommande utfasning av Azure AD TLS 1.0/1.1

**Typ:** Planera för förändring  
**Tjänstkategori:** Ej A  
**Produktfunktion:** Standarder

Azure Active Directory kommer att inaktuella följande protokoll i Azure Active Directory regioner från och med 30 juni 2021:


- TLS 1.0
- TLS 1.1
- 3DES-chiffersvit (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Berörda miljöer är:

- Azures kommersiella moln
- Office 365 GCC och WW

Mer information finns i Aktivera stöd för TLS 1.2 i din miljö för Utfasning av [Azure AD TLS 1.1 och 1.0.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Offentlig förhandsversion – Azure AD-berättigandehantering har nu stöd för Multi-Geo SharePoint Online

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Berättigandehantering
 
För organisationer som använder Multi-Geo SharePoint Online kan du nu inkludera platser från specifika multi-geo-miljöer till dina åtkomstpaket för berättigandehantering. [Läs mer](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Offentlig förhandsversion – Återställa borttagna appar från Appregistreringar

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Utvecklarupplevelse
 
Kunder kan nu visa, återställa och permanent ta bort borttagna appregistreringar från Azure Portal. Detta gäller endast för program som är associerade med en katalog, inte program från en personlig Microsoft-konto. [Läs mer](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Offentlig förhandsversion – Ny "Användaråtgärd" i Villkorlig åtkomst för registrering eller anslutning av enheter

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection
 
 En ny användaråtgärd som kallas "Registrera eller anslut enheter" i Villkorlig åtkomst är tillgänglig. Med den här användaråtgärden kan du styra principer för multifaktorautentisering (MFA) för Azure AD-enhetsregistrering. 

För närvarande tillåter den här användaråtgärden endast att du aktiverar MFA som en kontroll när användare registrerar eller ansluter enheter till Azure AD. Andra kontroller som är beroende av eller inte tillämpliga för Azure AD-enhetsregistrering inaktiveras med den här användaråtgärden. [Läs mer](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Offentlig förhandsversion – Optimera anslutningsgrupper för att använda den Programproxy molntjänsten

**Typ:** Ny funktion  
**Tjänstkategori:** Appproxy  
**Produktfunktion:** Access Control
 
Med den här nya funktionen kan anslutningsgrupper tilldelas till den närmaste regionala Programproxy som ett program finns i. Detta kan förbättra appens prestanda i scenarier där appar finns i andra regioner än hemklientorganisationens region. [Läs mer](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Offentlig förhandsversion – External Identities Self-Service I AAD med hjälp av E-One-Time lösenordskonton

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C

Externa användare kommer nu att kunna använda E-One-Time lösenordskonton för att registrera sig på Azure AD 1-part och LOB-appar. [Läs mer](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Offentlig förhandsversion – tillgänglighet för AD FS Sign-Ins i Azure AD

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Övervakning & rapportering
 
AD FS kan nu integreras med Azure AD-aktivitetsrapportering, vilket ger en enhetlig vy över hybrididentitetsinfrastrukturen. Med hjälp av Azure AD Sign-Ins-rapporten, Log Analytics och Azure Monitor-arbetsböcker är det möjligt att utföra djupgående analys för både AAD- och AD FS-inloggningsscenarier, till exempel AD FS-kontolåsningar, misslyckade lösenordsförsök och toppar med oväntade inloggningsförsök.

Mer information finns i [AD FS inloggningar i Azure AD med Connect Health.](../hybrid/how-to-connect-health-ad-fs-sign-in.md)

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Allmän tillgänglighet – mellanlagring av molnautentisering

**Typ:** Ny funktion  
**Tjänstkategori:** AD Connect  
**Produktfunktion:** Användarautentisering
 
Mellanlagring av molnautentisering är nu allmänt tillgänglig. Med funktionen för mellanlagring kan du selektivt testa grupper av användare med molnautentiseringsmetoder, till exempel direktautentisering (PTA) eller synkronisering av lösenordshashar (PHS). Under tiden fortsätter alla andra användare i de federerade domänerna att använda federationstjänster, till exempel AD FS eller andra federationstjänster för att autentisera användare. [Läs mer](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Allmän tillgänglighet – Attributet Användartyp kan nu uppdateras i Azure-administratörsportalen

**Typ:** Ny funktion  
**Tjänstkategori:** Användarupplevelse och hantering  
**Produktfunktion:** Användarhantering
 
Kunder kan nu uppdatera användartypen för Azure AD-användare när de uppdaterar sin användarprofilinformation från Azure-administratörsportalen. Användartypen kan uppdateras från Microsoft Graph också. Mer information finns i Lägga [till eller uppdatera användarprofilinformation.](active-directory-users-profile-azure-portal.md)
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Allmän tillgänglighet – replikuppsättningar för Azure Active Directory Domain Services

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD Domain Services  
**Produktfunktion:** Azure AD Domain Services
 
Funktionen för replikuppsättningar i Azure AD DS är nu allmänt tillgänglig. [Läs mer](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Allmän tillgänglighet – Samarbeta med dina partner med hjälp av One-Time lösenord i Azure Government molnet

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Organisationer i Microsoft Azure Government molnet kan nu göra det möjligt för sina gäster att lösa in inbjudningar med e-post One-Time lösenord. Detta säkerställer att alla gästanvändare utan Azure AD-, Microsoft- eller Gmail-konton i Azure Government-molnet fortfarande kan samarbeta med sina partner genom att begära och ange en tillfällig kod för att logga in på delade resurser. [Läs mer](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Nya federerade appar som är tillgängliga i Azure AD-programgalleriet – mars 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I mars 2021 lade vi till följande 37 nya program i vårt appgalleri med federationsstöd:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [MOBH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid](https://auth.flipgrid.com/), [hCaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Skavda LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [Exium](../saas-apps/exium-tutorial.md), [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [DEPOTH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-tutorial.md), Enterprise Vault.cloud for [Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX. PLUS,](../saas-apps/fax.plus-tutorial.md) [ValidSign,](../saas-apps/validsign-tutorial.md) [AWS enkel](../saas-apps/aws-single-sign-on-tutorial.md)inloggning, [Nura Space,](https://dashboard.nuraspace.com/login) [Broadcom DX SaaS,](../saas-apps/broadcom-dx-saas-tutorial.md) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [SendPro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Du hittar även dokumentationen för alla program här: https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – mars 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Enkel inloggning för AWS](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [Hanterad GitHub Enterprise-användare](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [Securelogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk användarkontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Introduktion till MS Graph API for Company Branding

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** MS Graph  
**Produktfunktion:** B2B/B2C

[MS Graph API för företags varumärke](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  är tillgängligt för Azure AD eller Microsoft 365 inloggningsupplevelse för att tillåta hantering av varumärkesparametrarna programmatiskt.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Allmän tillgänglighet – huvudbaserad autentisering med enkel inloggning med Programproxy

**Typ:** Ändrad funktion  
**Tjänstkategori:** Appproxy  
**Produktfunktion:** Access Control
 
Azure AD Programproxy inbyggt stöd för huvudbaserad autentisering är nu allmänt tillgängligt. Med den här funktionen kan du konfigurera de användarattribut som krävs som HTTP-huvuden för programmet utan att ytterligare komponenter behövs för att distribuera. [Läs mer](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Tvåvägs-SMS för MFA Server stöds inte längre

**Typ:** Deprecated  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 

Tvåvägs-SMS för MFA Server blev ursprungligen inaktuellt 2018 och kommer inte att stödjas efter den 24 februari 2021. Administratörer bör aktivera en annan metod för användare som fortfarande använder tvåvägs-SMS.

E-postmeddelanden och azure-Service Health-meddelanden skickades till berörda administratörer den 8 december 2020 och 28 januari 2021. Aviseringarna gick till RBAC-rollerna Ägare, Medägare, Administratör och Tjänstadministratör knutna till prenumerationerna. [Läs mer](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Februari 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Autentisering via e-post med lösenord som standard från oktober 2021

**Typ:** Planera för förändring  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 

Från och med 31 oktober 2021 Microsoft Azure Active Directory autentisering med [e-post](../external-identities/one-time-passcode.md) med ett lösenord standardmetoden för att bjuda in konton och klienter för B2B-samarbetsscenarier. För stunden kommer Microsoft inte längre att tillåta inlösning av inbjudningar med hjälp av ohanterade Azure Active Directory konton. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Obevakade men godkännda behörigheter läggs inte längre till i token om de utlöser villkorsstyrd åtkomst

**Typ:** Planera för förändring  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Plattform
 
För närvarande får [program som](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) använder dynamiska behörigheter alla behörigheter som de har samtyckt till åtkomst till. Detta inkluderar program som inte har frågats och även om de utlöser villkorlig åtkomst. Detta kan till exempel göra att en app som bara begär som också har medgivande för , tvingas att godkänna den villkorliga `user.read` `files.read` åtkomst som tilldelats för `files.read` behörigheten. 

För att minska antalet onödiga frågor om villkorsstyrd åtkomst ändrar Azure AD det sätt som obevakade omfång tillhandahålls till program. Appar utlöser endast villkorlig åtkomst för behörighet som de uttryckligen begär. Mer information finns i [Vad är nytt i autentisering?](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Offentlig förhandsversion – Använd en Tillfällig åtkomstkod för att registrera lösenordslösa autentiseringsuppgifter

**Typ:** Ny funktion  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection

Tillfällig åtkomstkod är ett tidsbegränsat lösenord som fungerar som starka autentiseringsuppgifter och möjliggör registrering av lösenordslösa autentiseringsuppgifter och återställning när en användare har förlorat eller glömt sin starka autentiseringsfaktor (till exempel FIDO2-säkerhetsnyckel eller Microsoft Authenticator)-app och behöver logga in för att registrera nya starka autentiseringsmetoder. [Läs mer](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Offentlig förhandsversion – Håll mig inloggad (KMSI) i nästa generations användarflöden

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C

Nästa generation av B2C-användarflöden har nu stöd för funktionen för att hålla mig [inloggad (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) som gör att kunder kan utöka sessionslivslängden för användarna av deras webbprogram och interna program med hjälp av en beständig cookie.  funktionen håller sessionen aktiv även när användaren stänger och öppnar webbläsaren igen och återkallas när användaren loggar ut.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Offentlig förhandsversion – External Identities Self-Service registrering i AAD med MSA-konton

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Externa användare kan nu använda Microsoft-konton för att logga in på Azure AD-appar från första part och i LOB-appar. [Läs mer](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Offentlig förhandsversion – Återställa inlösningsstatus för en gästanvändare

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Kunder kan nu återinbjuda befintliga externa gästanvändare att återställa sin inlösningsstatus, vilket gör att gästanvändarkontot kan förbli utan att de förlorar någon åtkomst. [Läs mer](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Offentlig förhandsversion – API:er för /synchronization (etablering) stöder nu programbehörigheter

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Kunder kan nu använda application.readwrite.ownedby som programbehörighet för att anropa synkroniserings-API:erna. Observera att detta endast stöds för etablering från Azure AD till program från tredje part (till exempel AWS, Data Bricks osv.). Det stöds för närvarande inte för HR-etablering (Workday/Successfactors) eller Cloud Sync (AD till Azure AD). [Läs mer](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Allmän tillgänglighet – inbyggd roll för administratör för autentiseringsprincip

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Användare med den här rollen kan konfigurera principen för autentiseringsmetoder, MFA-inställningar för hela klientorganisationen och lösenordsskyddsprincipen. Den här rollen ger behörighet att hantera inställningar för lösenordsskydd: smart utelåsningskonfiguration och uppdatering av listan över anpassade förbjudna lösenord. [Läs mer](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Allmän tillgänglighet – Användarsamlingar på Mina appar är tillgängliga nu!

**Typ:** Ny funktion  
**Tjänstkategori:** Mina appar  
**Produktfunktion:** Slutanvändarupplevelser
 
Användare kan nu skapa egna gruppningar av appar i Mina appar appstartaren. De kan också ordna om och dölja samlingar som delas med dem av administratören. [Läs mer](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Allmän tillgänglighet – Autofyll i Authenticator

**Typ:** Ny funktion  
**Tjänstkategori:** Microsoft Authenticator App  
**Produktfunktion:** Identity Security & Protection
 
Microsoft Authenticator tillhandahåller funktioner för multifaktorautentisering (MFA) och kontohantering, och fyller nu även i lösenord automatiskt på webbplatser och appar som användare besöker på sin mobil (iOS och Android). 

För att kunna använda autofyll i Authenticator måste användarna lägga till sina personliga Microsoft-konto Authenticator och använda dem för att synkronisera sina lösenord. Arbets- eller skolkonton kan inte användas för att synkronisera lösenord just nu. [Läs mer](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Allmän tillgänglighet – Bjud in interna användare till B2B-samarbete

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Kunder kan nu bjuda in interna gäster att använda B2B-samarbete i stället för att skicka en inbjudan till ett befintligt internt konto. På så sätt kan kunder behålla användarens objekt-ID, UPN, gruppmedlemskap och apptilldelningar. [Läs mer](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Allmän tillgänglighet – inbyggd roll som domännamnsadministratör

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Användare med den här rollen kan hantera (läsa, lägga till, verifiera, uppdatera och ta bort) domännamn. De kan också läsa kataloginformation om användare, grupper och program, eftersom dessa objekt har domänberoenden. 

För lokala miljöer kan användare med den här rollen konfigurera domännamn för federation så att associerade användare alltid autentiseras lokalt. Dessa användare kan sedan logga in på Azure AD-baserade tjänster med sina lokala lösenord via enkel inloggning. Federationsinställningar måste synkroniseras via Azure AD Connect, så användarna har också behörighet att hantera Azure AD Connect. [Läs mer](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – februari 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I februari 2021 har vi lagt till följande 37 nya program i vårt appgalleri med federationsstöd:

[Loop Yahoo Extension](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD Adapter](http://www.silverfort.com/), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), Yooz EU , [UXPressia](https://uxpressia.com/users/sign-in), [introDus Pre- and Onboarding Platform](http://app.introdus.dk/login), [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), [Fenloop Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), Samsung [Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md) [](https://eu1.getyooz.com/?kc_idp_hint=microsoft)

Du hittar även dokumentationen för alla program här: https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – februari 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Mer information finns i Automatisera [användareablering för SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Allmän tillgänglighet – 10 Azure Active Directory roller har nu bytt namn

**Typ:** Ändrad funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
10 inbyggda Roller i Azure AD har bytt namn så att de justeras över [Administrationscenter för Microsoft 365,](/microsoft-365/admin/microsoft-365-admin-center-preview) [Azure AD-portalen](https://portal.azure.com/) [och Microsoft Graph](https://developer.microsoft.com/graph/). Mer information om de nya rollerna finns i [Administratörsrollbehörigheter i Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabell som visar rollnamn i MS Graph API och Azure Portal samt det föreslagna slutliga namnet i API, Azure Portal och Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Ny varumärkesregistrering i MFA/SSPR kombinerad registrering

**Typ:** Ändrad funktion  
**Tjänstkategori:** Användarupplevelse och hantering  
**Produktfunktion:** Slutanvändarupplevelser
 
Tidigare användes inte företagslogotyp på Azure Active Directory inloggningssidor. Företagsregistrering finns nu längst upp till vänster i MFA/SSPR Combined Registration. Företags varumärke finns också på Sidan Sign-Ins och Säkerhetsinformation. [Läs mer](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Allmän tillgänglighet – Chef på andra nivån kan anges som alternativ godkännare

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 
Ett extra alternativ när du väljer godkännare är nu tillgängligt i Berättigandehantering. Om du väljer "Chef som godkännare" för den första godkännaren, har du ett annat alternativ, "Chef på andra nivån som alternativ godkännare", som kan väljas i fältet alternativ godkännare. Om du väljer det här alternativet måste du lägga till en reserv godkännare för att vidarebefordra begäran till om systemet inte kan hitta chefen på den andra nivån. [Läs mer](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Aktivitetsinstrumentpanel för autentiseringsmetoder

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 

Den uppdaterade instrumentpanelen för autentiseringsmetoders aktivitet ger administratörer en översikt över registrering av autentiseringsmetoder och användningsaktivitet i klientorganisationen. Rapporten sammanfattar antalet användare som har registrerats för varje metod och även vilka metoder som används vid inloggning och lösenordsåterställning. [Läs mer](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Uppdaterings- och sessionstokens livslängd konfigurerbarhet i konfigurerbar tokenlivslängd (CTL) har dragits tillbaka

**Typ:** Deprecated  
**Tjänstkategori:** Andra  
**Produktfunktion:** Användarautentisering
 
Uppdaterings- och sessionstokens livslängd för konfigurerbarhet i listan över betrodda certifikat dras tillbaka. Azure Active Directory inte längre uppdaterings- och sessionstokenkonfiguration i befintliga principer. [Läs mer](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Januari 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Hemlig token är ett obligatoriskt fält när du konfigurerar etablering

**Typ:** Planera för förändring  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel

Tidigare kunde fältet för hemlig token vara tomt när du konfigurerade etablering för det anpassade/BYOA-programmet. Den här funktionen var avsedd att enbart användas för testning. Vi uppdaterar användargränssnittet för att göra fältet obligatoriskt. 

Kunder kan komma runt det här kravet i testsyfte genom att använda en funktionsflagga i webbläsarens URL. [Läs mer](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Offentlig förhandsversion – Anpassa och konfigurera delade Android-enheter för frontline-arbetare i stor skala

**Typ:** Ny funktion  
**Tjänstkategori:** Enhetsregistrering och -hantering  
**Produktfunktion:** Identity Security & Protection
 
Azure AD och Microsoft Endpoint Manager team har kombinerats för att ge möjlighet att anpassa, skala och skydda dina frontline worker-enheter.

Med följande förhandsgranskningsfunktioner kan du:
- Etablera delade Android-enheter i stor skala med Microsoft Endpoint Manager
- Skydda din åtkomst för skiftarbetare med enhetsbaserad villkorlig åtkomst
- Anpassa inloggningsupplevelser för skiftarbetare med hanterad startskärm

Mer information finns i Anpassa [och konfigurera delade enheter för frontline-arbetare i stor skala.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Offentlig förhandsversion – Etableringsloggar kan nu laddas ned som en CSV- eller JSON-fil

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Livscykelhantering för identitet

Kunder kan ladda ned etableringsloggarna som en CSV- eller JSON-fil via användargränssnittet och via Graph API. Mer information finns i [Etableringsrapporter i Azure Active Directory portalen.](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Offentlig förhandsversion – Tilldela molngrupper till anpassade Azure AD-roller och roller för administratörsenhetsomfång

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Kunder kan tilldela en molngrupp till anpassade Azure AD-roller eller en roll med administratörsenhetsomfång. Information om hur du använder den här funktionen finns i [Använda molngrupper för att hantera rolltilldelningar i Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Allmän tillgänglighet – Azure AD Connect molnsynkronisering (kallades tidigare för molnetablering)

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD Connect molnsynkronisering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Azure AD Connect molnsynkronisering är nu allmänt tillgänglig för alla kunder.

Azure AD Connect molnet flyttar det tunga arbetet med transformeringslogik till molnet, vilket minskar ditt lokala fotavtryck. Dessutom är flera lättviktsagentdistributioner tillgängliga för högre synkroniseringstillgänglighet. [Läs mer](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Allmän tillgänglighet – inbyggda roller för attacksimuleringsadministratör och attacknyttolastförfattare

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Två nya roller i Role-Based Access Control kan tilldelas till användare, attacksimuleringsadministratör och attacknyttolastförfattare. 

Användare i rollen [Administratör för attacksimulering](../roles/permissions-reference.md#attack-simulation-administrator) har åtkomst för alla simuleringar i klientorganisationen och kan:
- skapa och hantera alla aspekter av skapande av attacksimulering
- starta/schemalägga en simulering
-  granska simuleringsresultat. 

Användare med rollen [Attack Payload Author kan](../roles/permissions-reference.md#attack-payload-author) skapa attacknyttolaster men inte starta eller schemalägga dem. Attacknyttolaster är sedan tillgängliga för alla administratörer i klientorganisationen som kan använda dem för att skapa en simulering.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Allmän tillgänglighet – den inbyggda rollen Läsare för användningssammanfattningsrapporter

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Användare med rollen Läsare för användningssammanfattningsrapporter kan komma åt aggregerade data på klientnivå och tillhörande insikter i Microsoft 365 Administrationscenter för användning och produktivitetspoäng. De kan dock inte komma åt information eller insikter på användarnivå. 

I Microsoft 365 administrationscenter för de två rapporterna skiljer vi mellan aggregerade data på klientnivå och användarnivåinformation. Den här rollen lägger till ett extra skyddslager för enskilda användaridentifierade data. [Läs mer](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Allmän tillgänglighet – Kräv Appskydd princip för villkorlig åtkomst i Azure AD

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection
 
Azure AD-beviljande för villkorsstyrd åtkomst för "Kräv appskyddsprincip" är nu GA. 

Principen innehåller följande funktioner:
- Tillåter endast åtkomst när du använder ett mobilprogram som stöder Intune-Appskydd
- Tillåter endast åtkomst när en användare har en Intune-appskyddsprincip levererad till mobilprogrammet

Läs mer om hur du ställer in en princip för villkorlig åtkomst för appskydd [här.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Allmän tillgänglighet – lösenord One-Time e-post

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Med E-postlösenord kan organisationer över hela världen samarbeta med vem som helst genom att skicka en länk eller inbjudan via e-post. Inbjudna användare kan verifiera sin identitet genom att skicka ett enda lösenord till sin e-postadress för att få åtkomst till partnerns resurser. [Läs mer](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nya etableringsanslutningar i Azure AD-programgalleriet – januari 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Mer information finns i Vad [är automatisk etablering av SaaS-appanvändare i Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Nya federerade appar som är tillgängliga i Azure AD-programgalleriet – januari 2021

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part

I januari 2021 lade vi till följande 29 nya program i vårt appgalleri med federationsstöd:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[BienvenueFormation](https://formation.bienvenue.pro/login), [AIDA Healthcare SSO](https://aidaforparents.com/login/organizations), [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Begrepp,](../saas-apps/notion-tutorial.md) [Syndio,](../saas-apps/syndio-tutorial.md) [Betero Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [Nalco E-data](https://www.ecolab.com/), [Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Offentlig förhandsversion – Chef på andra nivån kan anges som alternativ godkännare

**Typ:** Ändrad funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 
Ett extra alternativ när du väljer godkännare är nu tillgängligt i Berättigandehantering. Om du väljer "Chef som godkännare" för den första godkännaren har du ett annat alternativ, "Chef på andra nivån som alternativ godkännare", som kan väljas i fältet alternativ godkännare. Om du väljer det här alternativet måste du lägga till en reserv godkännare för att vidarebefordra begäran till om systemet inte kan hitta chefen på den andra nivån. [Läs mer](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Allmän tillgänglighet – Gå till Teams direkt från Min åtkomst portalen

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 
Nu kan du starta Teams direkt från Min åtkomst portalen. 

För att göra det loggar du in på Min åtkomst ( , navigerar till "Åtkomstpaket" och går sedan till fliken "Aktiv" för att se alla åtkomstpaket som du https://myaccess.microsoft.com/) redan har åtkomst till. När du expanderar det valda åtkomstpaketet och hovrar över Teams kan du starta det genom att klicka på knappen "Öppna". [Läs mer](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Förbättrad loggning & End-User prompter för riskfyllda gästanvändare

**Typ:** Ändrad funktion  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection
 

Loggnings- End-User frågor om riskfyllda gästanvändare har uppdaterats. Läs mer i [Identity Protection och B2B-användare](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>December 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Offentlig förhandsversion – Azure AD B2C registrering och inloggning via telefon med inbyggd princip

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Registrering och inloggning för B2C Phone med inbyggd princip gör det möjligt för IT-administratörer och utvecklare av organisationer att tillåta sina slutanvändare att logga in och registrera sig med ett telefonnummer i användarflöden. Läs [Konfigurera telefon registrering och inloggning för användarflöden (förhandsversion) om du](../../active-directory-b2c/phone-authentication-user-flows.md) vill veta mer.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Allmän tillgänglighet – Standardvärden för säkerhet är nu aktiverade för alla nya klienter som standard

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Identity Security & Protection
 
För att skydda användarkonton kommer alla nya klienter som skapats den 12 november 2020 eller senare att ha säkerhet som standard aktiverade. Standardinställningar för säkerhet tillämpar flera principer, inklusive:
- Kräver att alla användare och administratörer registrerar sig för MFA med hjälp av Microsoft Authenticator appen
- Kräver kritiska administratörsroller för att använda MFA varje gång de loggar in. Alla andra användare uppmanas att ange MFA när det behövs. 
- Äldre autentisering blockeras i hela klientorganisationen. 

Mer information finns i Vad [är standardinställningar för säkerhet?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Allmän tillgänglighet – Stöd för grupper med upp till 250 000 medlemmar i AADConnect

**Typ:** Ändrad funktion  
**Tjänstkategori:** AD Connect  
**Produktfunktion:** Hantering av identitetslivscykel
 
Microsoft har distribuerat en ny slutpunkt (API) för Azure AD Connect som förbättrar prestanda för synkroniseringstjänstens åtgärder för att Azure Active Directory. När du använder den nya [V2-slutpunkten](../hybrid/how-to-connect-sync-endpoint-api-v2.md)får du märkbara prestandaförbättringar vid export och import till Azure AD. Den här nya slutpunkten stöder följande scenarier:

- Synkronisera grupper med upp till 250 000 medlemmar
- Prestandaförbättringar vid export och import till Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Allmän tillgänglighet – Berättigandehantering är tillgängligt för klienter i Azure China-molnet

**Typ:** Ny funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 

Funktionerna i Berättigandehantering är nu tillgängliga för alla klienter i Azure China-molnet. Mer information finns på [dokumentationswebbplatsen för identitetsstyrning.](https://docs.azure.cn/zh-cn/active-directory/governance/)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nya etableringsanslutningar i Azure AD-programgalleriet – december 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Bizagi Studio för digital processautomatisering](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut-utskriftshantering i molnet](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – december 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I december 2020 lade vi till följande 18 nya program i vårt appgalleri med federationsstöd:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [ochage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md), [Asteriskstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Information om hur du listar ditt program i Azure AD-appgalleriet finns här https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Gå till Teams direkt från Min åtkomst portalen

**Typ:** Ändrad funktion  
**Tjänstkategori:** Produktfunktion för **hantering av användaråtkomst:** Berättigandehantering

Nu kan du starta Teams direkt från Min åtkomst portalen. Om du vill göra det loggar du in [på Min åtkomst,](https://myaccess.microsoft.com/)går till **Åtkomstpaket** och går sedan till **fliken Aktiv** för att se alla åtkomstpaket som du redan har åtkomst till. När du expanderar åtkomstpaketet och hovrar över Teams kan du starta det genom att klicka på **knappen** Öppna. 

Mer information om hur du använder Min åtkomst portalen finns i [Begär åtkomst till ett åtkomstpaket i Azure AD-berättigandehantering.](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Offentlig förhandsversion – Chef på andra nivån kan anges som alternativ godkännare

**Typ:** Ändrad funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering

Ett extra alternativ är nu tillgängligt i godkännandeprocessen i Berättigandehantering. Om du väljer Chef som godkännare för den första godkännaren har du ett annat alternativ, Chef på andra nivån som alternativ godkännare, som kan väljas i fältet alternativ godkännare. När du väljer det här alternativet måste du lägga till en reserv godkännare för att vidarebefordra begäran till om systemet inte kan hitta chefen på den andra nivån.

Mer information finns i Ändra [godkännandeinställningar för ett åtkomstpaket i Azure AD-berättigandehantering.](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)

--- 

## <a name="november-2020"></a>November 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0, TLS 1.1 och 3DES-utfasning

**Typ:** Planera för förändring  
**Tjänstkategori:** Alla Azure AD-program  
**Produktfunktion:** Standarder

Azure Active Directory kommer att inaktuella följande protokoll i Azure Active Directory regioner från och med 30 juni 2021:

- TLS 1.0
- TLS 1.1
- 3DES-chiffersvit (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Berörda miljöer är:
- Azures kommersiella moln
- Office 365 GCC och WW

Relaterat meddelande Alla kombinationer av klient-server och webbläsarserver bör använda TLS 1.2 och moderna chiffersviter för att upprätthålla en säker anslutning till Azure Active Directory för Azure, Office 365 och Microsoft 365 tjänster. Detta är en ändring som [är relaterad Azure Active Directory TLS 1.0 & 1.1 och 3DES Cipher Suite Deprecation i US Gov Cloud](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

Vägledning om hur du tar bort föråldrade protokollberoenden finns i Aktivera stöd för TLS 1.2 i din miljö för Utfasning av [Azure AD TLS 1.1 och 1.0.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Nya federerade appar som är tillgängliga i Azure AD-programgalleriet – november 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part

I november 2020 lade vi till följande 52 nya program i vårt appgalleri med federationsstöd:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login) [,Styrsel,](../saas-apps/tribeloo-tutorial.md) [Itslearning File Picker,](https://pmteam.itslearning.com/)Förvalskontroll, [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), Virtual Risk [Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU,](../saas-apps/timu-tutorial.md) [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - Recruit](https://recruit.talview.com/login), Real Time Translator, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), [EVA Check-in](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md), [Lucid (Alla produkter),](../saas-apps/lucid-tutorial.md) [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md),[Resource Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [Freight Measure](https://www.gpcsl.com/freight.html), [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), Nature [Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Software Relations CRM](https://cloud.relations-crm.com/), [Procrio](../saas-apps/procaire-tutorial.md), [Mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/) [](../saas-apps/crises-control-tutorial.md)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Offentlig förhandsversion – Anpassade roller för företagsappar

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
 [Anpassade RBAC-roller för delegerad företagsprogramhantering](../roles/custom-available-permissions.md) är nu i offentlig förhandsversion. Dessa nya behörigheter bygger på de anpassade rollerna för appregistreringshantering, vilket ger mer exakt kontroll över vilken åtkomst dina administratörer har. Med tiden släpps ytterligare behörigheter för att delegera hanteringen av Azure AD.

Några vanliga delegeringsscenarier:
- tilldelning av användare och grupper som har åtkomst till SAML-baserade program för enkel inloggning
- skapa Azure AD-galleriprogram
- uppdatera och läsa grundläggande SAML-konfigurationer för SAML-baserade program för enkel inloggning
- hantering av signeringscertifikat för SAML-baserade program för enkel inloggning
- uppdatering av utgående e-postadresser för certifikataviseringar för SAML-baserade program för enkel inloggning
- uppdatering av SAML-tokensignaturen och inloggningsalgoritmen för SAML-baserade program för enkel inloggning
- skapa, ta bort och uppdatera användarattribut och anspråk för SAML-baserade program för enkel inloggning
- möjlighet att aktivera, inaktivera och starta om etableringsjobb
- uppdateringar av attributmappning
- möjlighet att läsa etableringsinställningar som är associerade med objektet
- möjlighet att läsa etableringsinställningar som är associerade med tjänstens huvudnamn
- möjlighet att auktorisera programåtkomst för etablering

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Offentlig förhandsversion – Azure AD Programproxy har inbyggt stöd för åtkomst med enkel inloggning till program som använder huvuden för autentisering

**Typ:** Ny funktion  
**Tjänstkategori:** Appproxy  
**Produktfunktion:** Access Control
 
Azure Active Directory (Azure AD) Programproxy har inbyggt stöd för åtkomst med enkel inloggning till program som använder huvuden för autentisering. Du kan konfigurera huvudvärden som krävs av ditt program i Azure AD. Huvudvärdena skickas ned till programmet via Programproxy. Mer information finns i [Rubrikbaserad enkel inloggning för lokala appar med Azure AD App Proxy](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Allmän tillgänglighet – Azure AD B2C registrering och inloggning via anpassad princip

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C

Med registrering och inloggning via telefonnummer kan utvecklare och företag tillåta sina kunder att registrera sig och logga in med ett lösenord som skickas till användarens telefonnummer via SMS. Med den här funktionen kan kunden också ändra sitt telefonnummer om de förlorar åtkomst till sin telefon. Med kraften i anpassade principer kan utvecklare och företag förmedla sitt varumärke via sidanpassning. Lär dig hur du ställer in [registrering och inloggning via telefon med anpassade principer i Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – november 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic – Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Mer information finns i Automatisera [användareablering för SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Offentlig förhandsversion – E-Sign-In med ProxyAddresses kan nu distribueras via mellan olika distributioner

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
Klientorganisationsadministratörer kan nu använda mellanlagring för att distribuera e-Sign-In med ProxyAddresses till specifika Azure AD-grupper. Detta kan hjälpa dig när du provar funktionen innan du distribuerar den till hela klientorganisationen via identifieringsprincipen för hemsfär. Anvisningar för att distribuera eSign-In med ProxyAddresses via mellanstadierad distribution finns i [dokumentationen](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Begränsad förhandsversion – inloggningsdiagnostik

**Typ:** Ny funktion  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Med den första förhandsversionen av Inloggningsdiagnostik kan administratörer nu granska användarloggar. Administratörer kan få sammanhangsbaserad, specifik och relevant information och vägledning om vad som hände under en inloggning och hur problem kan åtgärdas. Diagnostiken är tillgänglig på både Azure AD-nivån och bladen Diagnostisera och lösa villkorsstyrd åtkomst. De diagnostikscenarier som beskrivs i den här versionen är villkorsstyrd åtkomst, multifaktorautentisering och lyckad inloggning.

Mer information finns i [Vad är inloggningsdiagnostik i Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Förbättrade egenskaper för obekant inloggning

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection

  Identifieringar av okända inloggningsegenskaper har uppdaterats. Kunder kan märka mer riskfyllda identifieringar av okända inloggningsegenskaper. Mer information finns i [Vad är risk?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Offentlig förhandsversion av Cloud Provisioning-agenten är nu tillgänglig (version: 1.1.281.0)

**Typ:** Ändrad funktion  
**Tjänstkategori:** Azure AD Cloud Provisioning  
**Produktfunktion:** Hantering av identitetslivscykel
 
Molnetableringsagenten har släppts i offentlig förhandsversion och är nu tillgänglig via portalen. Den här versionen innehåller flera förbättringar, inklusive stöd för GMSA för dina domäner, vilket ger bättre säkerhet, förbättrade inledande synkroniseringscykler och stöd för stora grupper. Mer information finns i [versionshistoriken.](../app-provisioning/provisioning-agent-release-version-history.md) 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>API-slutpunkten för BitLocker-återställningsnyckeln finns nu under /informationProtection

**Typ:** Ändrad funktion  
**Tjänstkategori:** Åtkomsthantering för enhet  
**Produktfunktion:** Livscykelhantering för enhet
 
Tidigare kunde du återställa BitLocker-nycklar via /bitlocker-slutpunkten. Vi kommer så småningom att göra den här slutpunkten inaktuell och kunderna bör börja använda API:et som nu hamnar under /informationProtection. 

Se [BitLocker-återställnings-API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) för uppdateringar av dokumentationen som återspeglar dessa ändringar.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Allmän tillgänglighet för Programproxy för HTML5 Fjärrskrivbordstjänster webbklienten

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Appproxy  
**Produktfunktion:** Access Control
 
Azure AD Programproxy för webbklienten Fjärrskrivbordstjänster (RDS) är nu allmänt tillgängligt. RDS-webbklienten ger användare åtkomst till fjärrskrivbordsinfrastrukturen via valfri HTLM5-kompatibel webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome och så vidare. Användare kan interagera med fjärrappar eller stationära datorer på samma sätt som med en lokal enhet var de än befinner sig. 

Genom att använda Azure AD Programproxy kan du öka säkerheten för rds-distributionen genom att tillämpa förautentisering och principer för villkorsstyrd åtkomst för alla typer av avancerade klientappar. Mer information finns i [Publicera fjärrskrivbord med Azure AD Programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Den nya förbättrade tjänsten dynamisk grupp är i offentlig förhandsversion

**Typ:** Ändrad funktion  
**Tjänstkategori:** Grupphantering  
**Produktfunktion:** Samarbete
 
Förbättrad dynamisk grupptjänst är nu i offentlig förhandsversion. Nya kunder som skapar dynamiska grupper i sina klientorganisationer kommer att använda den nya tjänsten. Den tid som krävs för att skapa en dynamisk grupp är proportionell mot storleken på gruppen som skapas i stället för storleken på klientorganisationen. Den här uppdateringen förbättrar prestanda för stora klienter avsevärt när kunder skapar mindre grupper. 

Den nya tjänsten syftar också till att slutföra tillägg och borttagning av medlemmar på grund av attributändringar inom några minuter. Dessutom blockerar inte enskilda bearbetningsfel klientbearbetning. Mer information om hur du skapar dynamiska grupper finns i vår [dokumentation.](../enterprise-users/groups-create-rule.md)
 
---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Lokala Azure AD-hybridagenter som påverkas av ändringar i Azure TLS-certifikat

**Typ:** Planera för förändring  
**Tjänstkategori:** Ej a-A  
**Produktfunktion:** Plattform

Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rotcertifikatutfärdare (CAs). Den här uppdateringen beror på att de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarforumet. Den här ändringen påverkar Azure AD-hybridagenter som är installerade lokalt och har härdade miljöer med en fast lista över rotcertifikat och måste uppdateras för att lita på de nya certifikatutfärdarena.

Den här ändringen leder till avbrott i tjänsten om du inte gör något omedelbart. Dessa agenter [](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) innehåller Programproxy-anslutningsappar för fjärråtkomst till [lokala,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenter för direktautentisering som gör att användarna kan logga in i program med samma lösenord och agenter för förhandsversionen av [molnetablering](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) som utför AD till Azure AD-synkronisering. 

Om du har en miljö med brandväggsregler som tillåter utgående anrop till endast specifik nedladdning av listan över återkallade certifikat (CRL) måste du tillåta följande CRL- och OCSP-URL:er. Fullständig information om ändringen och CRL- och OCSP-URL:er för att ge åtkomst till finns i  [Azure TLS-certifikatändringar](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Etableringshändelser tas bort från granskningsloggar och publiceras enbart till etableringsloggar

**Typ:** Planera för förändring  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Aktivitet av [SCIM-etableringstjänsten](../app-provisioning/user-provisioning.md) loggas i både granskningsloggarna och etableringsloggarna. Detta inkluderar aktivitet som att skapa en användare i ServiceNow, gruppera i GSuite eller importera en roll från AWS. I framtiden publiceras dessa händelser endast i etableringsloggarna. Den här ändringen implementeras för att undvika duplicerade händelser mellan loggar och ytterligare kostnader som uppstår för kunder som använder loggarna i Log Analytics. 

Vi tillhandahåller en uppdatering när ett datum har slutförts. Den här utfasningen är inte planerad för kalenderåret 2020. 

> [!NOTE]
> Detta påverkar inte händelser i granskningsloggarna utanför de synkroniseringshändelser som genereras av etableringstjänsten. Händelser som att skapa ett program, en princip för villkorlig åtkomst, en användare i katalogen osv. genereras fortfarande i granskningsloggarna. [Läs mer](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Lokala Azure AD-hybridagenter som påverkas av ändringar i Azure Transport Layer Security-certifikat (TLS)

**Typ:** Planera för förändring  
**Tjänstkategori:** Ej a-A  
**Produktfunktion:** Plattform
 
Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rotcertifikatutfärdare (CAs). Det kommer att ske en uppdatering på grund av att de aktuella CA-certifikaten inte följer något av kraven för CA-/webbläsarforumbaslinje. Den här ändringen påverkar Azure AD-hybridagenter som är installerade lokalt och har härdade miljöer med en fast lista över rotcertifikat. Dessa agenter måste uppdateras för att de nya certifikatutfärdarna ska lita på dem.

Den här ändringen leder till avbrott i tjänsten om du inte gör något omedelbart. Dessa agenter omfattar: 
- [Programproxy för](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) fjärråtkomst till lokala platser 
- [Agenter för](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) genomströmningsautentisering som gör att användarna kan logga in på program med samma lösenord
- [Cloud Provisioning Preview-agenter](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) som gör AD till Azure AD-synkronisering. 

Om du har en miljö med brandväggsregler som tillåter utgående anrop till endast specifik nedladdning av listan över återkallade certifikat (CRL) måste du tillåta CRL- och OCSP-URL:er. Fullständig information om ändringen och CRL- och OCSP-URL:er för att ge åtkomst till finns i [Azure TLS-certifikatändringar.](../../security/fundamentals/tls-certificate-changes.md)
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1.0, TLS 1.1 och 3DES-utfasning i US Gov Cloud

**Typ:** Planera för förändring  
**Tjänstkategori:** Alla Azure AD-program  
**Produktfunktion:** Standarder
 
Azure Active Directory inaktuella följande protokoll från och med 31 mars 2021:
- TLS 1.0
- TLS 1.1
- 3DES-chiffersvit (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Alla kombinationer av klient-server och webbläsare-server bör använda TLS 1.2 och moderna chiffersviter för att upprätthålla en säker anslutning till Azure Active Directory för Azure, Office 365 och Microsoft 365 tjänster.

Berörda miljöer är:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Anvisningar för hur du tar bort inaktuella protokollberoenden finns i Aktivera stöd för TLS 1.2 i din miljö för [Azure AD TLS 1.1 och 1.0-utfasning.](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Tilldela program till roller i administrativ enhet och objektomfång

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Med den här funktionen kan du tilldela ett program (SPN) till en administratörsroll i omfånget för den administrativa enheten. Mer information finns i Tilldela [begränsade roller till en administrativ enhet.](../roles/admin-units-assign-roles.md)

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Nu kan du inaktivera och ta bort gästanvändare när de nekas åtkomst till en resurs

**Typ:** Ny funktion  
**Tjänstkategori:** Åtkomstgranskningar  
**Produktfunktion:** Identitetsstyrning
 
Inaktivera och ta bort är en avancerad kontroll i Azure AD-åtkomstgranskningar som hjälper organisationer att bättre hantera externa gäster i grupper och appar. Om gäster nekas i en åtkomstgranskning blockerar **inaktivera och ta bort** dem automatiskt från att logga in i 30 dagar. Efter 30 dagar tas de bort helt från klientorganisationen.

Mer information om den här funktionen finns i [Inaktivera och ta bort externa identiteter med Azure AD-åtkomstgranskningar.](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Access Review Creators kan lägga till anpassade meddelanden i e-postmeddelanden till granskare

**Typ:** Ny funktion  
**Tjänstkategori:** Åtkomstgranskningar  
**Produktfunktion:** Identitetsstyrning
 
I Azure AD-åtkomstgranskningar kan administratörer som skapar granskningar nu skriva ett anpassat meddelande till granskarna. Granskarna ser meddelandet i e-postmeddelandet som uppmanar dem att slutföra granskningen. Mer information om hur du använder den här funktionen finns i steg 14 i [avsnittet Skapa en eller flera åtkomstgranskningar.](../governance/create-access-review.md#create-one-or-more-access-reviews)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – oktober 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk etablering av användarkonton finns i Automatisera användareablering till [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integrationsassistent för Azure AD B2C

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Integreringsassistenten (förhandsversion) är nu tillgänglig för Azure AD B2C Appregistreringar. Den här upplevelsen hjälper dig att konfigurera ditt program för vanliga scenarier. Läs mer om [metodtips och rekommendationer för Microsoft Identity Platform.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Visa rollmalls-ID i Azure Portal användargränssnitt

**Typ:** Ny funktion  
**Tjänstkategori:** Azure-roller  
**Produktfunktion:** Access Control
 

Nu kan du visa mall-ID:t för varje Azure AD-roll i Azure Portal. I Azure AD väljer du  **beskrivning** av den valda rollen. 

Vi rekommenderar att kunder använder rollmalls-ID:er i sina PowerShell-skript och kod, i stället för visningsnamnet. Rollmalls-ID stöds för användning till [directoryRoles- och](/graph/api/resources/directoryrole) [roleDefinition-objekt.](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) Mer information om rollmalls-ID:er finns i [Inbyggda roller i Azure AD.](../roles/permissions-reference.md)

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>API-anslutningsappar Azure AD B2C användarflöden för registrering är nu i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 

API-kopplingar är nu tillgängliga för användning med Azure Active Directory B2C. Med API-anslutningsappar kan du använda webb-API:er för att anpassa dina användarflöden för registrering och integrera med externa molnsystem. Du kan använda API-anslutningsappar för att:

- Integrera med anpassade godkännandearbetsflöden
- Verifiera användarindata
- Skriva över användarattribut 
- Köra anpassad affärslogik 

 Mer information [finns i Använda API-anslutningsappar för att anpassa](../../active-directory-b2c/api-connectors-overview.md) och utöka registreringdokumentationen.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Tillståndsegenskap för anslutna organisationer i rättighetshantering

**Typ:** Ny funktion  
**Tjänstkategori:** Kataloghantering **produktfunktion: Berättigandehantering**
 

 Alla anslutna organisationer har nu ytterligare en egenskap som kallas "Delstat". Tillståndet styr hur den anslutna organisationen ska användas i principer som refererar till "alla konfigurerade anslutna organisationer". Värdet blir antingen "konfigurerat" (vilket innebär att organisationen ingår i omfånget för principer som använder "all"-satsen) eller "föreslagen" (vilket innebär att organisationen inte ingår i omfånget).  

Manuellt skapade anslutna organisationer har standardinställningen "konfigurerad". Under tiden kommer automatiskt skapade (skapade via principer som tillåter alla användare från Internet att begära åtkomst) som standard att vara "föreslagna".  Alla anslutna organisationer som skapats före den 9 september 2020 ställs in på "konfigurerad". Administratörer kan uppdatera den här egenskapen efter behov. [Läs mer](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory External Identities har nu avancerade premiumsäkerhetsinställningar för B2C

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Funktioner för riskbaserad villkorlig åtkomst och riskidentifiering i Identity Protection är nu tillgängliga i [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Med dessa avancerade säkerhetsfunktioner kan kunder nu:
- Utnyttja intelligenta insikter för att utvärdera risker med B2C-appar och slutanvändarkonton. Identifieringar omfattar ovanliga resor, anonyma IP-adresser, länkade IP-adresser för skadlig kod och Azure AD-hotinformation. Portal- och API-baserade rapporter är också tillgängliga.
- Åtgärda risker automatiskt genom att konfigurera anpassningsbara autentiseringsprinciper för B2C-användare. Apputvecklare och administratörer kan minska risken för realtidsrisker genom att kräva multifaktorautentisering (MFA) eller blockera åtkomst beroende på vilken användarrisknivå som har identifierats, med ytterligare kontroller som är tillgängliga baserat på plats, grupp och app.
- Integrera med Azure AD B2C användarflöden och anpassade principer. Villkor kan utlösas från inbyggda användarflöden i Azure AD B2C eller införlivas i anpassade B2C-principer. Precis som med andra aspekter av B2C-användarflödet kan meddelanden om slutanvändarupplevelsen anpassas. Anpassningen är enligt organisationens alternativ för röst, varumärke och minskning.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – oktober 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I oktober 2020 har vi lagt till följande 27 nya program i vårt appgalleri med federationsstöd:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblepool - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), Zone Technologies [Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckGivande](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Appointment Reminder](https://app.appointmentreminder.co.nz/account/login), [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetly](https://app.greetly.com/), [OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), Loop Flow [CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Etableringsloggar kan nu strömmas till Log Analytics

**Typ:** Ny funktion  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 

Publicera dina etableringsloggar till Log Analytics för att:
- Lagra etableringsloggar i mer än 30 dagar
- Definiera anpassade aviseringar och meddelanden
- Skapa instrumentpaneler för att visualisera loggarna
- Köra komplexa frågor för att analysera loggarna 

Information om hur du använder funktionen finns i [Förstå hur etablering integreras med Azure Monitor loggar](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Etableringsloggar kan nu visas av programägare

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Du kan nu tillåta programägare att övervaka aktivitet av etableringstjänsten och felsöka problem utan att ge dem en privilegierad roll eller göra IT till en flaskhals. [Läs mer](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Byta namn på 10 Azure Active Directory roller

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Azure-roller  
**Produktfunktion:** Access Control
 
Vissa Azure Active Directory (AD) inbyggda roller har namn som skiljer sig från de som visas i Administrationscenter för Microsoft 365, Azure AD-portalen och Microsoft Graph. Den här inkonsekvensen kan orsaka problem i automatiserade processer. Med den här uppdateringen byter vi namn på 10 rollnamn så att de blir konsekventa. Följande tabell innehåller de nya rollnamnen:

![Tabell som visar rollnamn i MS Graph API och Azure Portal samt det föreslagna nya rollnamnet i administrationscentret för M365, Azure Portal och API.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C för auth-kodflöde för SPA:er med MSAL JS 2.x

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
MSAL.js version 2.x har nu stöd för auktoriseringskodflödet för ensideswebbappar (SPA). Azure AD B2C stöder nu användning av SPA-apptypen på Azure Portal och användning av MSAL.js-auktoriseringskodflöde med PKCE för ensidesappar. Detta gör att SPA:er som använder Azure AD B2C för att underhålla enkel inloggning med nyare webbläsare och följa nyare rekommendationer för autentiseringsprotokoll. Kom igång med [självstudien Registrera en ensidesapplikation (SPA) i Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) självstudie.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Uppdateringar av inställningen Kom ihåg multifaktorautentisering (MFA) på en betrodd enhet

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 

Vi har nyligen uppdaterat funktionen Kom ihåg multifaktorautentisering [(MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) på en betrodd enhet för att utöka autentiseringen med upp till 365 dagar. Azure Active Directory (Azure AD) [Premium-licenser](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) kan också använda principen Villkorlig åtkomst – inloggningsfrekvens som ger mer flexibilitet för inställningar för omauthenticering.

För en optimal användarupplevelse rekommenderar vi att du använder inloggningsfrekvensen för villkorsstyrd åtkomst för att utöka sessionens livslängd på betrodda enheter, platser eller sessioner med låg risk som ett alternativ till inställningen kom ihåg MFA på en betrodd enhet. Kom igång genom att läsa [vår senaste vägledning om hur du optimerar omauthenticeringsupplevelsen.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

---
