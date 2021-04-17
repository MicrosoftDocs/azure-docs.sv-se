---
title: Arkiv för Nyheter i Azure Active Directory? | Microsoft Docs
description: Den nya publiceringsanteckningarna i avsnittet Översikt i den här innehållsuppsättningen innehåller sex månaders aktivitet. Efter 6 månader tas objekten bort från huvudartikeln och läggs till i den här arkivartikeln.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0500c8535c6230bad85c97c23787a405edfbfee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588215"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Arkiv för Nyheter i Azure Active Directory?

Den primära [artikeln Nyheter i Azure Active Directory viktig](whats-new.md) information innehåller uppdateringar för de senaste sex månaderna, medan den här artikeln innehåller all äldre information.

Vad är nytt i Azure Active Directory? viktig information ger information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

---

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nya etableringsanslutningar i Azure AD-programgalleriet – september 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Uppdatering av den offentliga förhandsversionen av molnetablering

**Typ:** Ny funktion  
**Tjänstkategori:** Produktfunktion för Azure **AD-molnetablering: Hantering** av identitetslivscykel
 
Azure AD Connect den offentliga förhandsversionen av Molnetablering har två viktiga förbättringar som utvecklats utifrån kundfeedback: 

- Attributmappning via Azure Portal

    Med den här funktionen kan IT-administratörer mappa användar-, grupp- eller kontaktattribut från AD till Azure AD med hjälp av olika mappningstyper som finns i dag. Attributmappning är en funktion som används för att standardisera värdena för attributen som flödar från Active Directory till Azure Active Directory. Man kan avgöra om attributvärdet ska mappas direkt som det är från AD till Azure AD eller använda uttryck för att transformera attributvärdena när användare etableras. [Läs mer](../cloud-sync/how-to-attribute-mapping.md)

- Etablering på begäran eller testanvändare

    När du har ställt in konfigurationen kanske du vill testa för att se om användaromvandlingen fungerar som förväntat innan du tillämpar den på alla användare i omfånget. Med etablering på begäran kan IT-administratörer ange unikt namn (DN) för en AD-användare och se om de synkroniseras som förväntat. Etablering på begäran är ett bra sätt att se till att attributmappningarna som du gjorde tidigare fungerar som förväntat. [Läs mer](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Granskad BitLocker-återställning i Azure AD – offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** Hantering av enhetsåtkomst  
**Produktfunktion:** Livscykelhantering för enhet
 
När IT-administratörer eller slutanvändare läser BitLocker-återställningsnyckel(er) som de har åtkomst till genererar Azure Active Directory nu en granskningslogg som samlar in vem som har åtkomst till återställningsnyckeln. Samma granskning innehåller information om enheten som BitLocker-nyckeln var associerad med.

Slutanvändare kan komma [åt sina återställningsnycklar via Mitt konto.](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) IT-administratörer kan komma åt återställningsnycklar via [API:et för BitLocker-återställningsnyckel](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) i betaversion eller via Azure AD-portalen. Mer information finns i [Visa eller kopiera BitLocker-nycklar i Azure AD-portalen.](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)

---

### <a name="teams-devices-administrator-built-in-role"></a>Den inbyggda rollen Administratör för Teams-enheter

**Typ:** Ny funktion  
**Tjänstkategori:** Rbac  
**Produktfunktion:** Access Control
 
Användare med rollen [Administratör för Teams-enheter](../roles/permissions-reference.md#teams-devices-administrator) kan hantera [Teams-certifierade enheter](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) från Administrationscenter för Teams. 

Med den här rollen kan användaren visa alla enheter på en enda gång, med möjlighet att söka efter och filtrera enheter. Användaren kan också kontrollera informationen om varje enhet, inklusive det inloggade kontot och enhetens modell och deras modell. Användaren kan ändra inställningarna på enheten och uppdatera programvaruversionerna. Den här rollen beviljar inte behörigheter för att kontrollera Teams-aktivitet och anropa enhetens kvalitet.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Avancerade frågefunktioner för katalogobjekt

**Typ:** Ny funktion  
**Tjänstkategori:** MS Graph  
**Produktfunktion:** Utvecklarupplevelse
 
Alla nya frågefunktioner som introducerades för katalogobjekt i Azure AD-API:er är nu tillgängliga i v1.0-slutpunkten och produktionsklara. Utvecklare kan räkna, söka, filtrera och sortera katalogobjekt och relaterade länkar med hjälp av OData-standardoperatorer.

Mer information finns i dokumentationen [här, och du](https://aka.ms/BlogPostMezzoGA)kan även skicka feedback med den här [korta undersökningen.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Offentlig förhandsversion: Utvärdering av kontinuerlig åtkomst för klienter som har konfigurerat principer för villkorlig åtkomst

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Identity Security & Protection
 
Utvärdering av kontinuerlig åtkomst (CAE) är nu tillgängligt i offentlig förhandsversion för Azure AD-klienter med principer för villkorsstyrd åtkomst. Med CAE utvärderas kritiska säkerhetshändelser och principer i realtid. Detta inkluderar inaktivering av konto, lösenordsåterställning och platsändring. Mer information finns i Utvärdering [av kontinuerlig åtkomst.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Offentlig förhandsversion: Be användare som begär ett åtkomstpaket att ställa ytterligare frågor för att förbättra godkännandebesluten

**Typ:** Ny funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 
Administratörer kan nu kräva att användare som begär ett åtkomstpaket svarar på ytterligare frågor utöver bara affärsberättigande i Azure AD-rättighetshanteringens Min åtkomst portalen. Användarnas svar visas sedan för godkännare för att hjälpa dem att fatta ett mer korrekt beslut om åtkomstgodkännande. Mer information finns i Samla [in ytterligare information om beställare för godkännande (förhandsversion).](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)
 
---

### <a name="public-preview-enhanced-user-management"></a>Offentlig förhandsversion: Förbättrad användarhantering

**Typ:** Ny funktion  
**Tjänstkategori:** Användarhantering  
**Produktfunktion:** Användarhantering
 

Azure AD-portalen har uppdaterats för att göra det enklare att hitta användare på sidorna Alla användare och Borttagna användare. Här är några av ändringarna i förhandsversionen: 
- Mer synliga användaregenskaper, inklusive objekt-ID, katalogsynkroniseringsstatus, skapandetyp och identitetsutfärdare.
- Sök tillåter nu kombinerad sökning av namn, e-postmeddelanden och objekt-ID:er.
- Förbättrad filtrering efter användartyp (medlem, gäst och ingen), katalogsynkroniseringsstatus, skapandetyp, företagsnamn och domännamn.
- Nya sorteringsfunktioner för egenskaper som namn, användarens huvudnamn och borttagningsdatum.
- Ett nytt antal användare som uppdateras med sökningar eller filter.

Mer information finns i Förbättringar [av användarhantering (förhandsversion) i Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Fältet Ny information för företagsprogram

**Typ:** Ny funktion  
**Tjänstkategori:** Produktfunktion **för Enterprise Apps:** SSO

Du kan lägga till fritextanteckningar i företagsprogram. Du kan lägga till all relevant information som hjälper dig att leda program under Företagsprogram. Mer information finns i [Snabbstart: Konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD).](../manage-apps/add-application-portal-configure.md) 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – september 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part

I september 2020 lade vi till följande 34 nya program i vårt appgalleri med federationsstöd:

[VMware Horizon – Unified Access Gateway](), [Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEGET/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILGET](../saas-apps/cybersolutions-cybermail-tutorial.md), [ParentleCMMS](https://auth.limblecmms.com/), [Mobint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender Fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Arch](../saas-apps/rsa-archer-suite-tutorial.md)Suite , [TeamzSkill](../saas-apps/teamzskill-tutorial.md) [,mfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Suitegine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & Access Management](https://my.priva.com/), Oracle [Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), Secured Signing Enterprise Portal [AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec Online](https://wisteconline.com/auth/oidc), Oracle [PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Du hittar även dokumentationen för alla program här: https://aka.ms/AppsTutorial .

Information om hur du listar ditt program i Azure AD-appgalleriet finns här: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Ny delegeringsroll i Azure AD-berättigandehantering: Åtkomstpakettilldelningshanterare

**Typ:** Ny funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 
En ny roll för Tilldelningshanteraren för åtkomstpaket har lagts till i Azure AD-rättighetshanteringen för att ge detaljerade behörigheter för att hantera tilldelningar. Du kan nu delegera uppgifter till en användare i den här rollen, som kan delegera tilldelningshantering av ett åtkomstpaket till en företagsägare. En tilldelningshanterare för åtkomstpaket kan dock inte ändra åtkomstpaketprinciper eller andra egenskaper som angetts av administratörerna. 

Med den här nya rollen får du den minsta behörighet som krävs för att delegera hantering av tilldelningar och behålla administrativ kontroll över alla andra konfigurationer av åtkomstpaket. Mer information finns i [Berättigandehanteringsroller.](../governance/entitlement-management-delegate.md#entitlement-management-roles)
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Ändringar Privileged Identity Management av onboarding-flödet

**Typ:** Ändrad funktion  
**Tjänstkategori:** Privileged Identity Management  
**Produktfunktion:** Privileged Identity Management
 
Tidigare krävde registrering till Privileged Identity Management (PIM) användarmedgivande och ett registreringsflöde på PIM-bladet som inkluderade registrering i Azure AD MFA. Med den senaste integreringen av PIM-upplevelsen i bladet Azure AD-roller och administratörer tar vi bort den här upplevelsen. Alla klienter med giltig P2-licens publiceras automatiskt till PIM.

Registrering till PIM har ingen direkt negativ effekt på din klientorganisation. Du kan förvänta dig följande ändringar:
- Ytterligare tilldelningsalternativ, till exempel aktiv kontra berättigad med start- och sluttid när du gör en tilldelning på Antingen PIM- eller Azure AD-roller och -administratörsbladet. 
- Ytterligare omfångsmekanismer, till exempel administrativa enheter och anpassade roller, introduceras direkt i tilldelningsupplevelsen. 
- Om du är global administratör eller privilegierad rolladministratör kan du börja få några ytterligare e-postmeddelanden, till exempel sammanfattningen av PIM varje vecka. 
- Du kan också se ms-pim-tjänstens huvudnamn i granskningsloggen som rör rolltilldelning. Den här förväntade ändringen bör inte påverka ditt vanliga arbetsflöde.

 Mer information finns i [Börja använda Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-berättigandehantering: Fönstret Välj för åtkomstpaketresurser visar nu som standard de resurser som för närvarande finns i den valda katalogen

**Typ:** Ändrad funktion  
**Tjänstkategori:** Hantering av användaråtkomst  
**Produktfunktion:** Berättigandehantering
 

Beteendet för välj fönster ändras under fliken Resursroller i flödet för att skapa åtkomstpaket. För närvarande är standardbeteendet att visa alla resurser som ägs av användaren och resurser som läggs till i den valda katalogen. 

Den här upplevelsen ändras så att endast de resurser som för närvarande läggs till i katalogen visas som standard, så att användarna enkelt kan välja resurser från katalogen. Uppdateringen hjälper till att identifiera resurser för att lägga till åtkomstpaket och minskar risken för att oavsiktligt lägga till resurser som ägs av användaren som inte ingår i katalogen. Mer information finns i Skapa [ett nytt åtkomstpaket i Azure AD-berättigandehantering.](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>Augusti 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Uppdateringar av Azure Multi-Factor Authentication-server brandväggskrav

**Typ:** Planera för förändring  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 
Från och med 1 oktober 2020 Azure MFA Server brandväggskrav ytterligare IP-intervall.

Om du har utgående brandväggsregler i din organisation uppdaterar du reglerna så att MFA-servrarna kan kommunicera med alla nödvändiga IP-intervall. IP-intervallen dokumenteras i [Azure Multi-Factor Authentication-server brandväggskrav.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Kommande ändringar i användarupplevelsen i Identity Secure Score

**Typ:** Planera för förändring  
**Tjänstkategori:** Produktfunktion **för Identity Protection:** Identity Security & Protection

Vi uppdaterar identity secure score-portalen så att den överensstämmer med de ändringar som introducerades i Microsoft Secure Scores [nya version](/microsoft-365/security/mtp/microsoft-secure-score-whats-new). 

Förhandsversionen med ändringarna blir tillgänglig i början av september. Ändringarna i förhandsversionen är:
- "Identity Secure Score" har bytt namn till "Secure Score for Identity" för varumärkesjustering med Microsoft Secure Score
- Punkter normaliseras till standardskala och rapporteras i procent i stället för punkter

I den här förhandsversionen kan kunder växla mellan den befintliga upplevelsen och den nya upplevelsen. Den här förhandsversionen varar fram till slutet av november 2020. Efter förhandsversionen dirigeras kunderna automatiskt till den nya UX-upplevelsen.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nya begränsade gäståtkomstbehörigheter i Azure AD – offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** Access Control   
**Produktfunktion:** Användarhantering

Vi har uppdaterat behörigheter på katalognivå för gästanvändare. Dessa behörigheter gör att administratörer kan kräva ytterligare begränsningar och kontroller för extern gästanvändaråtkomst. Administratörer kan nu lägga till ytterligare begränsningar för externa gästers åtkomst till användar- och gruppers profil- och medlemskapsinformation. Med den här offentliga förhandsversionsfunktionen kan kunder hantera extern användaråtkomst i stor skala genom att fördrösa gruppmedlemskap, inklusive att hindra gästanvändare från att se medlemskap i de grupper som de finns i.

Mer information finns i Behörighet [för begränsad gäståtkomst och](../enterprise-users/users-restrict-guest-permissions.md) [Standardbehörigheter för användare.](./users-default-permissions.md)
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Allmän tillgänglighet för deltafrågor för tjänstens huvudnamn

**Typ:** Ny funktion  
**Tjänstkategori:** MS Graph  
**Produktfunktion:** Utvecklarupplevelse
 
Microsoft Graph Delta Query stöder nu resurstypen i v1.0:
- Tjänstens huvudnamn

Nu kan klienter spåra ändringar av dessa resurser effektivt och ger den bästa lösningen för att synkronisera ändringar till dessa resurser med ett lokalt datalager. Information om hur du konfigurerar dessa resurser i en fråga finns i [Använda deltafråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Allmän tillgänglighet för deltafrågor för oAuth2PermissionGrant

**Typ:** Ny funktion  
**Tjänstkategori:** MS Graph  
**Produktfunktion:** Utvecklarupplevelse

Microsoft Graph Delta Query stöder nu resurstypen i v1.0:
- OAuth2PermissionGrant

Klienter kan nu spåra ändringar av dessa resurser effektivt och ger den bästa lösningen för att synkronisera ändringar till dessa resurser med ett lokalt datalager. Information om hur du konfigurerar dessa resurser i en fråga finns i [Använda deltafråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – augusti 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part

I augusti 2020 lade vi till följande 25 nya program i vårt appgalleri med federationsstöd:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Sis Sis](https://almau.getalma.com/), [Enlyft Dynamics 365 Connector](http://enlyft.com/), [Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq](https://web.uniq.app/), [Vivis](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Debitmira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireHjul](../saas-apps/wirewheel-tutorial.md), [Zix-efterlevnad](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)och Capture , [Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec-reglage,](https://www.clearance.network/) [iSAMS](../saas-apps/isams-tutorial.md) [,Smart](../saas-apps/verasmart-tutorial.md), [Amsmart](https://amiko.web.rivero.app/), [Twingate](https://auth.twingate.com/signup), [trattleasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scale skalbara](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Wandera-slutanvändare](https://www.wandera.com/)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resursskogar är nu tillgängliga för Azure AD DS 

**Typ:** Ny **funktionskategori för tjänst:** Azure AD Domain Services   
**Produktfunktion:** Azure AD Domain Services
 
Funktionen för resursskogar i Azure AD Domain Services är nu allmänt tillgänglig. Du kan nu aktivera auktorisering utan synkronisering av lösenordshashar för att Azure AD Domain Services, inklusive smartkortsauktorisering. Mer information finns i Begrepp [och funktioner för replikuppsättningar för Azure Active Directory Domain Services (förhandsversion)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Stöd för regionala repliker för Azure AD DS hanterade domäner är nu tillgängligt

**Typ:** Ny funktion   
**Tjänstkategori:** Azure AD Domain Services  
**Produktfunktion:** Azure AD Domain Services
 
Du kan expandera en hanterad domän så att den har fler än en replikuppsättning per Azure AD-klientorganisation. Replikuppsättningar kan läggas till i alla peer-lagda virtuella nätverk i valfri Azure-region som stöder Azure AD Domain Services. Ytterligare replikuppsättningar i olika Azure-regioner ger geografisk haveriberedskap för äldre program om en Azure-region går offline. Mer information finns i Begrepp [och funktioner för replikuppsättningar för Azure Active Directory Domain Services (förhandsversion)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Allmän tillgänglighet för Azure AD My Sign-Ins

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Slutanvändarupplevelser
 
Azure AD My Sign-Ins är en ny funktion som gör att företagsanvändare kan granska sin inloggningshistorik för att söka efter ovanliga aktiviteter. Dessutom gör den här funktionen att slutanvändarna kan rapportera "Det här var inte jag" eller "Det här var jag" om misstänkta aktiviteter. Mer information om hur du använder den här funktionen finns i Visa och söka efter din senaste [inloggningsaktivitet från sidan Sign-Ins min.](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR-driven användareablering till Azure AD är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Du kan nu integrera SAP SuccessFactors som auktoritativ identitetskälla med Azure AD och automatisera identitetslivscykeln från start till slut med hr-händelser som nyanställda och uppsägningar för att driva etablering och avetablering av konton i Azure AD. 

Mer information om hur du konfigurerar ingående etablering för SAP SuccessFactors till Azure AD finns i självstudien [Konfigurera SAP SuccessFactors till Active Directory-användareablering.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Anpassat stöd för Open ID Connect MS Graph API för Azure AD B2C

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Tidigare kunde anpassade Open ID Connect-providers endast läggas till eller hanteras via Azure Portal. Nu kan Azure AD B2C lägga till och hantera dem via betaversionen Microsoft Graph API:er. Information om hur du konfigurerar den här resursen med API:er finns i [identityProvider resource type ( resurstypen identityProvider).](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Tilldela inbyggda roller i Azure AD till molngrupper

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD-roller  
**Produktfunktion:** Access Control

Nu kan du tilldela inbyggda Azure AD-roller till molngrupper med den här nya funktionen. Du kan till exempel tilldela rollen SharePoint-administratör till Contoso_SharePoint_Admins grupp. Du kan också använda PIM för att göra gruppen till en berättigad medlem i rollen, i stället för att bevilja stående åtkomst. Information om hur du konfigurerar den här funktionen finns i Använda molngrupper för att hantera [rolltilldelningar i Azure Active Directory (förhandsversion)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Den inbyggda rollen Insights Business Leader är nu tillgänglig

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD-roller  
**Produktfunktion:** Access Control
 
Användare i rollen Som affärsledare för Insights kan komma åt en uppsättning instrumentpaneler och insikter via [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Detta omfattar fullständig åtkomst till alla instrumentpaneler och presenterade funktioner för insikter och datagranskning. Användare i den här rollen har dock inte åtkomst till produktkonfigurationsinställningar, vilket är insights-administratörsrollens ansvar. Mer information om den här rollen finns i [Administratörsrollbehörigheter i Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Den inbyggda rollen Insights-administratör är nu tillgänglig

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD-roller  
**Produktfunktion:** Access Control
 
Användare med rollen Insights-administratör har åtkomst till alla administrativa funktioner i [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). En användare i den här rollen kan läsa kataloginformation, övervaka tjänstens hälsotillstånd, filsupportärenden och få åtkomst till administratörsinställningarna för Insights. Mer information om den här rollen finns i [Behörigheter för administratörsroll i Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Programadministratör och molnprogramadministratör kan hantera tilläggsegenskaper för program

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Azure AD-roller  
**Produktfunktion:** Access Control
 
Tidigare kunde endast den globala administratören hantera [tilläggsegenskapen](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true). Nu aktiverar vi den här funktionen även för programadministratören och molnprogramadministratören.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 snabbkorrigering 4.6.263.0 och anslutningsappar 1.1.1301.0

**Typ:** Ändrad funktion  
**Tjänstkategori:** Microsoft Identity Manager  
**Produktfunktion:** Hantering av identitetslivscykel

Ett [paket för snabbkorrigeringar (version 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) är tillgängligt för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här sammanslagningspaketet innehåller uppdateringar för MIM CM, MIM-synkroniseringshanteraren och PAM-komponenterna. Dessutom innehåller de allmänna MIM-anslutningsapparna version 1.1.1301.0 uppdateringar för Graph-anslutningsappen.

---

## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Som IT-administratör vill jag rikta in mig på klientappar med villkorlig åtkomst

**Typ:** Planera för förändring   
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection
 
Med GA-versionen av villkoret för klientappar i villkorsstyrd åtkomst kommer nya principer nu att gälla som standard för alla klientprogram. Detta inkluderar äldre autentiseringsklienter. Befintliga principer förblir oförändrade, men reglaget Konfigurera *Ja/Nej* tas bort från befintliga principer för att enkelt se vilka klientappar som tillämpas av principen. 

När du skapar en ny princip ska du se till att undanta användare och tjänstkonton som fortfarande använder äldre autentisering. Om du inte gör det blockeras de. [Läs mer](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Kommande SCIM-efterlevnadskorrigeringar

**Typ:** Planera för förändring  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Azure AD-etableringstjänsten använder SCIM-standarden för integrering med program. Vår implementering av SCIM-standarden utvecklas och vi förväntar oss att göra ändringar i vårt beteende kring hur vi utför PATCH-åtgärder samt ange egenskapen "aktiv" för en resurs. [Läs mer](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Inställningen Gruppägare på Azure-administratörsportalen ändras

**Typ:** Planera för förändring  
**Tjänstkategori:** Grupphantering  
**Produktfunktion:** Samarbete

Ägarinställningar på sidan för gruppinställningar kan konfigureras för att begränsa behörigheterna för ägartilldelning till en begränsad grupp användare i Azure-administratörsportalen och Åtkomstpanelen. Vi kommer snart att kunna tilldela gruppägare privilegier inte bara på dessa två UX-portaler, utan även tillämpa principen på serverdelar för att tillhandahålla konsekvent beteende mellan slutpunkter, till exempel PowerShell och Microsoft Graph. 

Vi börjar inaktivera den aktuella inställningen för kunder som inte använder den och erbjuder ett alternativ för att begränsa gruppägarens behörighet till användare under de kommande månaderna. Vägledning om hur du uppdaterar gruppinställningar finns i Redigera gruppinformation med [hjälp av Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory registreringstjänsten upphör stödet för TLS 1.0 och 1.1

**Typ:** Planera för förändring  
**Tjänstkategori:** Registrering och hantering av enheter  
**Produktfunktion:** Plattform

Transport Layer Security (TLS) 1.2 och uppdateringsservrar och klienter kommer snart att kommunicera med Azure Active Directory Device Registration Service. Stöd för TLS 1.0 och 1.1 för kommunikation med Azure AD Device Registration Service kommer att dra tillbaka:
- Den 31 augusti 2020, i alla nationella moln (GCC High, DoD osv.)
- Den 30 oktober 2020, i alla kommersiella moln

[Läs mer](../devices/reference-device-registration-tls-1-2.md) om TLS 1.2 för Azure AD-registreringstjänsten.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Windows Hello för företag inloggningar som visas i Inloggningsloggar för Azure AD

**Typ:** Fast  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Windows Hello för företag slutanvändare att logga in på Windows-datorer med en gest (till exempel en PIN-kod eller biometrik). Azure AD-administratörer kanske vill skilja Windows Hello för företag inloggningar från andra Windows-inloggningar som en del av en organisations resa till lösenordsfri autentisering. 

Administratörer kan nu se om en Windows-autentisering används Windows Hello för företag genom att kontrollera fliken Autentiseringsinformation för en Windows-inloggningshändelse på bladet Azure AD Sign-Ins i Azure Portal. Windows Hello för företag autentiseringar inkluderar "WindowsHelloForBusiness" i fältet Autentiseringsmetod. Mer information om hur du tolkar Sign-In loggar finns i dokumentationen [om inloggningsloggar.](../reports-monitoring/concept-sign-ins.md)
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Korrigeringar av gruppborttagningsbeteende och prestandaförbättringar

**Typ:** Fast  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
När en grupp tidigare ändrades från "in-scope" till "out-of-scope" och en administratör klickade på Starta om innan ändringen slutfördes, togs inte gruppobjektet bort. Nu tas gruppobjektet bort från målprogrammet när det hamnar utanför omfånget (inaktiverat, borttagna, otilldelade eller inte godkänt omfångsfilter). [Läs mer](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Offentlig förhandsversion: Administratörer kan nu lägga till anpassat innehåll i e-postmeddelandet till granskare när de skapar en åtkomstgranskning

**Typ:** Ny funktion  
**Tjänstkategori:** Åtkomstgranskningar  
**Produktfunktion:** Identitetsstyrning
 
När en ny åtkomstgranskning skapas får granskaren ett e-postmeddelande som ber dem att slutföra åtkomstgranskningen. Många av våra kunder har bett om möjligheten att lägga till anpassat innehåll i e-postmeddelandet, till exempel kontaktinformation eller annat ytterligare stödinnehåll som hjälper granskaren. 

Nu tillgängligt i offentlig förhandsversion kan administratörer ange anpassat innehåll i e-postmeddelandet som skickas till granskare genom att lägga till innehåll i avsnittet "avancerat" i Azure AD-åtkomstgranskningar. Vägledning om hur du skapar åtkomstgranskningar finns i [Skapa en åtkomstgranskning av grupper och program i Azure AD-åtkomstgranskningar.](../governance/create-access-review.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Auktoriseringskodflöde för ensidesappar är tillgängligt

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Utvecklarupplevelse
 
På grund av moderna cookiebegränsningar från tredje part från webbläsare, till exempel Safari ITP, måste SPA:er använda auktoriseringskodflödet i stället för det implicita flödet för att underhålla enkel inloggning, och MSAL.js v 2.x stöder nu auktoriseringskodflödet. 

Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA till typen "spa" och använda auth-kodflödet. Mer [information finns i Logga in användare och få en åtkomsttoken](../develop/quickstart-v2-javascript-auth-code.md) i ett JavaScript SPA med hjälp av auth-kodflödet.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD Programproxy stöder nu Fjärrskrivbordstjänster webbklienten

**Typ:** Ny funktion  
**Tjänstkategori:** Appproxy  
**Produktfunktion:** Access Control

Azure AD Programproxy stöder nu webbklienten Fjärrskrivbordstjänster (RDS). RDS-webbklienten ger användare åtkomst till fjärrskrivbordsinfrastrukturen via valfri HTLM5-kompatibel webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome osv. Användare kan interagera med fjärrappar eller stationära datorer på samma sätt som med en lokal enhet var de än befinner sig. Genom att använda Azure AD Programproxy kan du öka säkerheten för rds-distributionen genom att tillämpa principer för förautentisering och villkorsstyrd åtkomst för alla typer av avancerade klientappar. Mer information finns i [Publicera fjärrskrivbord med Azure AD Programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nästa generations Azure AD B2C användarflöden i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Förenklad användarflödesupplevelse erbjuder funktionsparitet med förhandsgranskningsfunktioner och är här alla nya funktioner finns. Användarna kommer att kunna aktivera nya funktioner i samma användarflöde, vilket minskar behovet av att skapa flera versioner med varje ny funktionsversion. Slutligen förenklar det nya, användarvänliga UX valet och skapandet av användarflöden. Prova nu genom att [skapa ett användarflöde](../../active-directory-b2c/tutorial-create-user-flows.md). 

Mer information om användarflöden finns i [Användarflödesversioner i Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nya federerade appar som är tillgängliga i Azure AD-programgalleriet – juli 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I juli 2020 lade vi till följande 55 nya program i vårt appgalleri med federationsstöd:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects-mottagningsist,](https://msteams.peterconnects.com/) [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Tierom](https://start.cocoom.com/), [CONSTRUCTION Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), Reflekt , [Rever](https://reflekt.konsolute.com/login), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [BlogIn](../saas-apps/titanfile-tutorial.md) [,Pettric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md), Community [Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR,](https://smarthr.jp/) [NumlyEngage™](../saas-apps/numlyengage-tutorial.md) [,Ubb Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Egress](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (School Management System)](https://smartschoolz.com/login), [Zegivande - Intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md) [,Master](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md) [](https://app.reverscore.net/access)

Du hittar även dokumentationen för alla program här https://aka.ms/AppsTutorial

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visa rolltilldelningar för alla omfång och möjlighet att ladda ned dem till en CSV-fil

**Typ:** Ändrad funktion  
**Tjänstkategori:** Azure AD-roller  
**Produktfunktion:** Access Control
 
Nu kan du visa rolltilldelningar för alla omfång för en roll på fliken Roller och administratörer i Azure AD-portalen. Du kan också ladda ned rolltilldelningarna för varje roll till en CSV-fil. Vägledning om hur du visar och lägger till rolltilldelningar finns [i Visa och tilldela administratörsroller i Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Utfasning av Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Typ:** Deprecated  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 
Azure Multi-Factor Authentication Software Development (Azure MFA SDK) nådde slutet på sin livslängd den 14 november 2018, vilket först meddelades i november 2017. Microsoft kommer att stänga av SDK-tjänsten från och med den 30 september 2020. Alla anrop till SDK misslyckas.

Om din organisation använder Azure MFA SDK måste du migrera senast den 30 september 2020:
- Azure MFA SDK för MIM: Om du använder SDK med MIM bör du migrera till Azure MFA Server och aktivera Privileged Access Management (PAM) genom att följa dessa [anvisningar.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Azure MFA SDK för anpassade appar: Överväg att integrera din app i Azure AD och använda villkorsstyrd åtkomst för att framtvinga MFA. Kom igång genom att granska den här [sidan.](../manage-apps/plan-an-application-integration.md) 

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Villkor för användarrisk i principen för villkorsstyrd åtkomst

**Typ:** Planera för förändring  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection
 

Med stöd för användarrisker i principen för villkorsstyrd åtkomst i Azure AD kan du skapa riskbaserade principer för flera användare. Olika lägsta risknivåer för användare kan krävas för olika användare och appar. Baserat på användarrisk kan du skapa principer för att blockera åtkomst, kräva multifaktorautentisering, säker lösenordsändring eller omdirigera till Microsoft Cloud App Security för att framtvinga sessionsprincip, till exempel ytterligare granskning.

Riskvillkoret för användare kräver Azure AD Premium P2 eftersom det använder Azure Identity Protection, som är ett P2-erbjudande. Mer information om villkorlig åtkomst finns i dokumentationen om [villkorsstyrd åtkomst i Azure AD.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO stöder nu appar som kräver att SPNameQualifier anges på begäran

**Typ:** Fast  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso
 
Vissa SAML-program kräver att SPNameQualifier returneras i försäkrans ämne när det begärs. Nu svarar Azure AD korrekt när en SPNameQualifier begärs i begärans NameID-princip. Detta fungerar även för SP-initierad inloggning och IdP-initierad inloggning följer.  Mer information om SAML-protokoll i Azure Active Directory finns i Enkel Sign-On [SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Collaboration har stöd för att bjuda in MSA- och Google-användare Azure Government klientorganisation

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 

Azure Government klienter som använder B2B-samarbetsfunktionerna kan nu bjuda in användare som har ett Microsoft- eller Google-konto. Om du vill ta reda på om din klientorganisation kan använda dessa funktioner följer du anvisningarna i Hur vet jag om [B2B-samarbete](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) är tillgängligt i min Azure US Government-klient?

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Användarobjektet i MS Graph v1 innehåller nu egenskaperna externalUserState och externalUserStateChangedDateTime

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 

Egenskaperna externalUserState och externalUserStateChangedDateTime kan användas för att hitta inbjudna B2B-gäster som inte har accepterat sina inbjudningar ännu samt skapa automatisering, till exempel ta bort användare som inte har accepterat sina inbjudningar efter ett visst antal dagar. Dessa egenskaper är nu tillgängliga i MS Graph v1. Vägledning om hur du använder dessa egenskaper finns i [Användarresurstyp.](/graph/api/resources/user)
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Hantera autentiseringssessioner i villkorsstyrd åtkomst i Azure AD är nu allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection
 
Med funktionerna för hantering av autentiseringssessioner kan du konfigurera hur ofta användarna måste ange inloggningsuppgifter och om de behöver ange autentiseringsuppgifter när de stänger och öppnar webbläsare igen för att ge bättre säkerhet och flexibilitet i din miljö.
 
Dessutom gäller hantering av autentiseringssessioner endast för First Factor Authentication på Azure AD-anslutna, Hybrid Azure AD-anslutna och Azure AD-registrerade enheter. Nu gäller även hantering av autentiseringssessioner för MFA. Mer information finns i Konfigurera [hantering av autentiseringssessioner med villkorlig åtkomst.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nya federerade appar som är tillgängliga i Azure AD-programgalleriet – juni 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I juni 2020 lade vi till följande 29 nya program i vårt appgalleri med federationsstöd:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [Bulls hubbarkoordinator](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), Ai-granskare , [Pobuca Connect](https://app.pobu.ca/), [Proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), Hub [Planner](../saas-apps/hub-planner-tutorial.md), [Ansmail-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement), IBM Digital [Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [](https://www.mindbridge.ai/products/ai-auditor/) [pymetrics](../saas-apps/pymetrics-tutorial.md), [Zero](https://www.teamzero.com/), [InStation](https://instation.invillia.com/), [edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), Field [iD](../saas-apps/field-id-tutorial.md), [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Du hittar även dokumentationen för alla program https://aka.ms/AppsTutorial här. Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen här: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-anslutningsappar External Identities självbetjäning är nu i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
External Identities API-anslutningsappar kan du använda webb-API:er för att integrera självbetjäning för registrering med externa molnsystem. Det innebär att du nu kan anropa webb-API:er som specifika steg i ett registreringflöde för att utlösa molnbaserade anpassade arbetsflöden. Du kan till exempel använda API-anslutningsappar för att:

- Integrera med anpassade godkännandearbetsflöden.
- Utföra identitetsbevis
- Verifiera användarindata
- Skriva över användarattribut
- Köra anpassad affärslogik

Mer information om alla funktioner som är möjliga med API-anslutningsappar finns i Använda API-anslutningsappar för att anpassa och utöka självbetjäning, eller Anpassa External Identities-registrering via självbetjäning med [webb-API-integreringar.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin) [](../external-identities/api-connectors-overview.md)
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Etablera på begäran och få användare till dina appar på några sekunder

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Azure AD-etableringstjänsten fungerar för närvarande cykliskt. Tjänsten körs var 40:e minut. Med [etablering på begäran kan du välja](https://aka.ms/provisionondemand) en användare och etablera dem på några sekunder. Med den här funktionen kan du snabbt felsöka etableringsproblem utan att behöva göra en omstart för att tvinga etableringscykeln att starta igen. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Ny behörighet för att använda Azure AD-berättigandehantering i Graph

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Berättigandehantering
 
En ny delegerad behörighet, EntitlementManagement.Read.All, är nu tillgänglig för användning med entitlement-API för hantering i Microsoft Graph betaversion. Mer information om tillgängliga API:er finns i Arbeta med API:et för [berättigandehantering i Azure AD.](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)

---

### <a name="identity-protection-apis-available-in-v10"></a>Identity Protection-API:er som är tillgängliga i v1.0

**Typ:** Ny funktion  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection
 
RiskyUsers och riskDetections Microsoft Graph API:er är nu allmänt tillgängliga. Nu när de är tillgängliga på v1.0-slutpunkten kan du använda dem i produktion. Mer information finns i Microsoft Graph [dokument.](/graph/api/resources/identityprotectionroot)
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Känslighetsetiketter för att tillämpa principer Microsoft 365 grupper är nu allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänstkategori:** Grupphantering  
**Produktfunktion:** Samarbete
 

Du kan nu skapa känslighetsetiketter och använda etikettinställningarna för att tillämpa principer på Microsoft 365, inklusive sekretess (offentlig eller privat) och extern användaråtkomstprincip. Du kan skapa en etikett med sekretesspolicyn privat och en extern användaråtkomstprincip som inte tillåter att gästanvändare lägger till. När en användare använder den här etiketten för en grupp är gruppen privat och inga gästanvändare tillåts läggas till i gruppen. 

Känslighetsetiketter är viktiga för att skydda dina affärskritiska data och för att du ska kunna hantera grupper i stor skala på ett kompatibelt och säkert sätt. Vägledning om hur du använder känslighetsetiketter finns i [Tilldela känslighetsetiketter till Microsoft 365 grupper i Azure Active Directory (förhandsversion)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Uppdateringar för support för Microsoft Identity Manager för Azure AD Premium kunder

**Typ:** Ändrad funktion  
**Tjänstkategori:** Microsoft Identity Manager  
**Produktfunktion:** Hantering av identitetslivscykel
 
Azure Support är nu tillgängligt för Azure AD-integreringskomponenter för Microsoft Identity Manager 2016, till och med slutet av utökat stöd för Microsoft Identity Manager 2016. Läs mer i [Supportuppdatering för Azure AD Premium kunder som använder Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Användningen av villkor för gruppmedlemskap i SSO-anspråkskonfigurationen ökar

**Typ:** Ändrad funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso
 
Tidigare var antalet grupper som du kunde använda när du villkorligt ändrade anspråk baserat på gruppmedlemskap i en enskild programkonfiguration begränsat till 10. Användningen av villkor för gruppmedlemskap i SSO-anspråkskonfigurationen har nu ökat till högst 50 grupper. Mer information om hur du konfigurerar anspråk finns i Konfiguration av [enterprise-program för enkel inloggning.](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions) 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Aktivera grundläggande formatering på textkomponenten på inloggningssidan i Företagsstilföring.

**Typ:** Ändrad funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
Funktionen för företagsinloggning i Azure AD/Microsoft 365 har uppdaterats så att kunden kan lägga till hyperlänkar och enkel formatering, inklusive fetstil, understrykning och italik. Vägledning om hur du använder den här funktionen finns i [Lägga till varumärkeshantering Azure Active Directory på inloggningssidan](./customize-branding.md)för din organisation.

---

### <a name="provisioning-performance-improvements"></a>Prestandaförbättringar för etablering

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Etableringstjänsten har uppdaterats för att minska tiden för en [inkrementell cykel](../app-provisioning/how-provisioning-works.md#incremental-cycles) att slutföras. Det innebär att användare och grupper kommer att etableras i sina program snabbare än de var tidigare. Alla nya etableringsjobb som skapats efter 2020-06-10 drar automatiskt nytta av prestandaförbättringarna. Alla program som har konfigurerats för etablering före 2020-06-10 måste startas om en gång efter 2020-06-10 för att dra nytta av prestandaförbättringarna. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Meddelande om utfasning av ADAL- och MS Graph Parity

**Typ:** Deprecated  
**Tjänstkategori:** Ej a-A  
**Produktfunktion:** Livscykelhantering för enhet

Nu när Microsoft Authentication Libraries (MSAL) är tillgängligt kommer vi inte längre att lägga till nya funktioner i Azure Active Directory Authentication Libraries (ADAL) och kommer att avsluta säkerhetskorrigeringar den 30 juni 2022. Mer information om hur du migrerar till MSAL finns i [Migrera program till Microsoft Authentication Library (MSAL).](../develop/msal-migration.md)

Dessutom har vi slutfört arbetet med att göra alla Azure AD Graph-funktioner tillgängliga via MS Graph. Därför får Azure AD Graph-API:er endast felkorrigeringar och säkerhetskorrigeringar fram till den 30 juni 2022. Mer information finns i Uppdatera [dina program så att de använder Microsoft Authentication Library och Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>Maj 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Tillbakatagande av egenskaper i signIns, riskyUsers och riskDetections-API:er

**Typ:** Planera för förändring  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection

För närvarande används uppräknade typer för att representera egenskapen riskType i både riskDetections-API:et och riskyUserHistoryItem (i förhandsversion). Uppräknade typer används också för egenskapen riskEventTypes i signIns-API:et. Framöver kommer vi att representera dessa egenskaper som strängar. 

Kunder bör övergå till egenskapen riskEventType i API:et beta riskDetections och riskyUserHistoryItem och till egenskapen riskEventTypes_v2 i api:et beta signIns den 9 september 2020. Vid det datumet kommer vi att ta de aktuella egenskaperna riskType och riskEventTypes i pension. Mer information finns i Ändringar av [riskhändelseegenskaper och Identity Protection-API:er på Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Utfasning av egenskapen riskEventTypes i signIns v1.0-API:et på Microsoft Graph

**Typ:** Planera för förändring  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Identity Security & Protection

Uppräknade typer växlar till strängtyper när de representerar riskhändelseegenskaper i Microsoft Graph september 2020. Förutom att påverka förhandsversions-API:erna påverkar den här ändringen även API:et för inloggningar i produktion.

Vi har introducerat en ny riskEventsTypes_v2 (sträng) för signIns v1.0-API:et. Vi drar tillbaka den aktuella riskEventTypes-egenskapen (uppräkning) den 11 juni 2022 i enlighet med vår Microsoft Graph utfasningsprincip. Kunder bör övergå till egenskapen riskEventTypes_v2 i signIns-API:et för v1.0 senast den 11 juni 2022. Mer information finns i [Utfasning av riskEventTypes-egenskapen i SIGNIns v1.0 API på Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Kommande ändringar i MFA-e-postaviseringar

**Typ:** Planera för förändring  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 

Vi gör följande ändringar i e-postaviseringarna för moln-MFA:

E-postaviseringar skickas från följande adress: azure-noreply@microsoft.com och msonlineservicesteam@microsoftonline.com . Vi uppdaterar innehållet i e-postmeddelanden med bedrägeriaviseringar för att bättre ange de steg som krävs för att avblockera användningen.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Ny självbetjäning registrerar sig för användare i federerade domäner som inte kan komma åt Microsoft Teams eftersom de inte är synkroniserade med Azure Active Directory.

**Typ:** Planera för förändring  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 

För närvarande kan användare som är i domäner som är federerade i Azure AD, men som inte är synkroniserade i klientorganisationen, inte komma åt Teams. Från och med slutet av juni gör den här nya funktionen det möjligt för dem att göra det genom att utöka den befintliga funktionen för e-postverifierade registrering. Detta gör att användare som kan logga in på en federerad IdP, men som ännu inte har ett användarobjekt i Azure-ID, kan skapa ett användarobjekt automatiskt och autentiseras för Teams. Deras användarobjekt markeras som "registrering med självbetjäning". Det här är en utökning av den befintliga funktionen för att göra e-postverifierad självsignering som användare i hanterade domäner kan göra och kan styras med samma flagga. Den här ändringen kommer att slutföras under de kommande två månaderna. Håll utkik efter dokumentationsuppdateringar [här.](../enterprise-users/directory-self-service-signup.md)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Kommande korrigering: OIDC-identifieringsdokumentet för Azure Government-molnet uppdateras för att referera till rätt Graph-slutpunkter.

**Typ:** Planera för förändring  
**Tjänstkategori:** Nationella moln  
**Produktfunktion:** Användarautentisering
 
Från och med juni börjar OIDC-identifieringsdokumentet Microsoft Identity Platform och [OpenID Connect-protokollet](../develop/v2-protocols-oidc.md) på [Azure Government-molnslutpunkten](../develop/authentication-national-cloud.md) (login.microsoftonline.us) att returnera rätt slutpunkt för [nationella](/graph/deployments) molndiagram ( eller , baserat på den klientorganisation som https://graph.microsoft.us https://dod-graph.microsoft.us) tillhandahålls.  Den innehåller för närvarande fel graph-slutpunkt (graph.microsoft.com) "msgraph_host"-fält.  

Den här felkorrigeringen kommer att distribueras gradvis under cirka två månader.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government användare kommer inte längre att kunna logga in på login.microsoftonline.com

**Typ:** Planera för förändring  
**Tjänstkategori:** Nationella moln  
**Produktfunktion:** Användarautentisering
 
Den 1 juni 2018 ändrades den officiella Azure Active Directory (Azure AD) för Azure Government från https://login-us.microsoftonline.com till https://login.microsoftonline.us . Om du äger ett program i en Azure Government klientorganisation måste du uppdatera ditt program för att logga in användare på slutpunkten .us.

Från och med den 5 maj börjar Azure AD framtvinga slutpunktsändringen, vilket blockerar Azure Government-användare från att logga in i appar som finns i Azure Government-klienter med hjälp av den offentliga slutpunkten (microsoftonline.com). Påverkade appar börjar se felet AADSTS900439 – USGClientNotSupportedOnPublicEndpoint. 

Den här ändringen kommer att distribueras gradvis med tillämpning som förväntas vara klar för alla appar i juni 2020. Mer information finns i [Azure Government blogginlägget](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML-begäran om enkel utloggning skickar nu NameID i rätt format

**Typ:** Fast  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
När en användare klickar på utloggning (t.ex. i MyApps-portalen) skickar Azure AD ett SAML-meddelande med enkel utloggning till varje app som är aktiv i användarsessionen och har en konfigurerad utloggnings-URL. Dessa meddelanden innehåller ett NameID i ett beständigt format.

Om den ursprungliga SAML-inloggningstoken använde ett annat format för NameID (t.ex. e-post/UPN) kan SAML-appen inte korrelera NameID i utloggningsmeddelandet med en befintlig session (eftersom de NameID:n som används i båda meddelandena är olika), vilket gjorde att utloggningsmeddelandet ignorerades av SAML-appen och användaren var inloggade. Den här korrigeringen gör utgående meddelandet konsekvent med det NameID som har konfigurerats för programmet.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Administratörsrollen för hybrididentitet är nu tillgänglig med molnetablering

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD Cloud Provisioning  
**Produktfunktion:** Hantering av identitetslivscykel
 
IT-administratörer kan börja använda den nya hybridadministratörsrollen som den minst privilegierade rollen för att konfigurera Azure AD Connect molnetablering. Med den här nya rollen behöver du inte längre använda den globala administratörsrollen för att konfigurera molnetablering. [Läs mer](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nya federerade appar tillgängliga i Azure AD-programgalleriet – maj 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I maj 2020 har vi lagt till följande 36 nya program i vårt appgalleri med federationsstöd:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Teams , Talentsoft](https://powell-software.com/en/powell-teams-en/) [Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), My [IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), Smart Global [Governance](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Tiermsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), [EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md), [Humanage](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander](../saas-apps/textexpander-tutorial.md), [Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Du hittar även dokumentationen för alla program https://aka.ms/AppsTutorial här.

Om du vill visa ditt program i Azure AD-appgalleriet kan du läsa informationen https://aka.ms/AzureADAppRequest här.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Rapportläge för villkorsstyrd åtkomst är nu allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection

[Med rapportläge för villkorsstyrd åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att framtvinga åtkomstkontroller. Du kan testa endast rapportprinciper i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av endast rapportläge – över 26 miljoner användare är redan i omfånget för en princip för endast rapport. Med tillkännagivandet idag kommer nya azure AD-principer för villkorsstyrd åtkomst att skapas i endast rapportläge som standard. Det innebär att du kan övervaka effekten av dina principer från den tidpunkt då de skapas. Och för dig som använder MS Graph-API:er kan [du även hantera endast rapportprinciper programmatiskt.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="self-service-sign-up-for-guest-users"></a>Registrera dig för gästanvändare med självbetjäning

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Med External Identities i Azure AD kan du ge personer utanför organisationen åtkomst till dina appar och resurser samtidigt som de kan logga in med vilken identitet de föredrar. När du delar ett program med externa användare vet du kanske inte alltid i förväg vem som behöver åtkomst till programmet. Med [självbetjäning kan](../external-identities/self-service-sign-up-overview.md)du göra så att gästanvändare kan registrera sig och få ett gästkonto för dina verksamhetsbaserade appar. Inloggningsflödet kan skapas och anpassas för att stödja Azure AD och sociala identiteter. Du kan också samla in ytterligare information om användaren under registrering.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbetsboken Information om villkorsstyrd åtkomst och rapportering är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection

Arbetsboken [insikter och rapportering ger](../conditional-access/howto-conditional-access-insights-reporting.md) administratörer en översikt över villkorsstyrd åtkomst i Azure AD i klientorganisationen. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i realtid. Arbetsboken strömmar data som lagras i Azure Monitor, som du kan konfigurera på några minuter genom att [följa dessa instruktioner.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) För att göra instrumentpanelen mer lätt att identifiera har vi flyttat den till fliken Nya insikter och rapportering på menyn villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Principinformationsbladet för villkorlig åtkomst är i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection

Det nya [principinformationsbladet](../conditional-access/troubleshoot-conditional-access.md) visar tilldelningar, villkor och kontroller som uppfylls under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad på flikarna Villkorlig åtkomst eller Endast rapport i inloggningsinformationen.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nya frågefunktioner för katalogobjekt i Microsoft Graph är i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstkategori:** MS **Graph-produktfunktion:** Utvecklarupplevelse

Nya funktioner introduceras för Microsoft Graph Directory Objects-API:er, vilket möjliggör åtgärder för antal, sökning, filter och sortering. Detta ger utvecklare möjlighet att snabbt köra frågor mot våra katalogobjekt utan lösningar som minnesfiltrering och sortering. Läs mer i det [här blogginlägget.](https://aka.ms/CountFilterMSGraphAAD)

Vi är för närvarande i offentlig förhandsversion och letar efter feedback. Skicka dina kommentarer med den här [korta undersökningen.](https://aka.ms/MsGraphAADSurveyDocs)

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurera SAML-baserad enkel inloggning med hjälp Microsoft Graph API (Beta)

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso
 
Stöd för att skapa och konfigurera ett program från Azure AD-galleriet med HJÄLP av MS Graph API:er i Beta är nu tillgängligt. Om du behöver konfigurera SAML-baserad enkel inloggning för flera instanser av ett program sparar du tid genom att använda Microsoft Graph-API:erna för att automatisera konfigurationen av [SAML-baserad enkel inloggning](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – maj 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

* [8 x 8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic efter organisation](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML-tokenkryptering är allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso
 
[Med SAML-tokenkryptering](../manage-apps/howto-saml-token-encryption.md) kan program konfigureras för att ta emot krypterade SAML-försäkran. Funktionen är nu allmänt tillgänglig i alla moln.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Anspråk för gruppnamn i programtoken är allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso
 
Gruppanspråken som utfärdas i en token kan nu begränsas till bara de grupper som tilldelats till programmet.  Detta är särskilt viktigt när användare är medlemmar i ett stort antal grupper och det finns en risk att överskrida storleksgränserna för token. Med den här nya funktionen på plats är möjligheten att [lägga till gruppnamn i token](../hybrid/how-to-connect-fed-group-claims.md) allmänt tillgänglig.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Tillbakaskrivning av Workday stöder nu inställning av attribut för arbetstelefonnummer

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Vi har förbättrat etableringsappen för tillbakaskrivning av Workday till att nu ha stöd för tillbakaskrivning av arbetstelefonnummer och mobilnummerattribut. Förutom e-post och användarnamn kan du nu konfigurera workday-etableringsappen för tillbakaskrivning för att flöda telefonnummervärden från Azure AD till Workday. Mer information om hur du konfigurerar tillbakaskrivning av telefonnummer finns i självstudien [om workday-appen för tillbakaskrivning.](../saas-apps/workday-writeback-tutorial.md) 

---

### <a name="publisher-verification-preview"></a>Verifiering av utgivare (förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Utvecklarupplevelse
 
Verifiering av utgivare (förhandsversion) hjälper administratörer och slutanvändare att förstå äktheten hos programutvecklare som integrerar med Microsoft Identity Platform. Mer information finns i Verifiering [av utgivare (förhandsversion).](../develop/publisher-verification-overview.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Auktoriseringskodflöde för ensidesappar

**Typ:** Tjänstkategori **för ändrade funktioner:** **Autentiseringsproduktfunktion:** Utvecklarupplevelse

På grund av moderna webbläsarbegränsningar för cookies från tredje part, till exempel [Safari ITP,](../develop/reference-third-party-cookies-spas.md)måste SPA:er använda auktoriseringskodflödet i stället för det implicita flödet för att underhålla enkel inloggning; MSAL.js v 2.x stöder nu auktoriseringskodflödet. Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera ditt SPA så att det skriver "spa" och använder auth-kodflödet. Vägledning finns i Snabbstart: Logga in användare och hämta en åtkomsttoken i ett JavaScript SPA med [hjälp av auth-kodflödet](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Förbättrad filtrering för enheter är i offentlig förhandsversion

**Typ:** Ändrad funktion   
**Tjänstkategori:** Enhetshantering **produktfunktion: Livscykelhantering** för enhet
 
Tidigare var de enda filter du kunde använda "Aktiverat" och "Aktivitetsdatum". Nu kan du filtrera [listan över enheter på fler egenskaper, inklusive](../devices/device-management-azure-portal.md#device-list-filtering-preview)OS-typ, kopplingstyp, efterlevnad med mera. Dessa tillägg bör göra det enklare att hitta en viss enhet.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig

**Typ:** Ändrad funktion   
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** Hantering av identitetslivscykel
 
Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig. 

Tidigare var du tvungen att hantera dina B2C-konsumentriktade program separat från resten av dina appar med hjälp av den äldre programupplevelsen. Det innebar olika upplevelser för att skapa appar på olika platser i Azure.

Den nya upplevelsen visar alla B2C-appregistreringar och Azure AD-appregistreringar på ett och samma ställe och ger ett konsekvent sätt att hantera dem. Oavsett om du behöver hantera en kundriktad app eller en app som har åtkomst till Microsoft Graph för att programmatiskt hantera Azure AD B2C-resurser behöver du bara lära dig ett sätt att göra saker.

Du kan nå den nya upplevelsen genom att Azure AD B2C tjänsten och välja Appregistreringar bladet. Upplevelsen är också tillgänglig från Azure Active Directory tjänsten.

Den Azure AD B2C Appregistreringar upplevelsen baseras på den allmänna [appregistreringsupplevelsen för](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Azure AD-klienter, men är skräddarsydd för Azure AD B2C. Den äldre programupplevelsen kommer att bli inaktuell i framtiden.

Mer information finns i [Den nya appregistreringsupplevelsen för Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Kombinerad registrering av säkerhetsinformation är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänstkategori:** Autentiseringar (inloggningar)

**Produktfunktion:** Identity Security & Protection

Den kombinerade registreringsupplevelsen för Multi-Factor Authentication (MFA) och Self-Service SSPR (Password Reset) är nu allmänt tillgänglig. Med den här nya registreringsupplevelsen kan användare registrera sig för MFA och SSPR i en enda steg-för-steg-process. När du distribuerar den nya upplevelsen för din organisation kan användarna registrera sig på kortare tid och med färre problem. Kolla in blogginlägget [här.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>Kontinuerlig tillgänglighetskontroll

**Typ:** Ny funktion

**Tjänstkategori:** Autentiseringar (inloggningar)

**Produktfunktion:** Identity Security & Protection

Kontinuerlig tillgänglighetskontroll är en ny säkerhetsfunktion som möjliggör framtvingande av principer nästan i realtid för förlitande parter som använder Azure AD-åtkomsttoken när händelser inträffar i Azure AD (till exempel borttagning av användarkonto). Vi distribuerar den här funktionen först för Teams- och Outlook-klienter. Mer information finns i vår [blogg](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) och [dokumentation.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-inloggning: Firstline Workers kan logga in på Azure AD-säkerhetskopierade program med sitt telefonnummer och inget lösenord

**Typ:** Ny funktion

**Tjänstkategori:** Autentiseringar (inloggningar)

**Produktfunktion:** Användarautentisering

Office lanserar en serie mobilappar för företag som riktar sig till icke-traditionella organisationer och för anställda i stora organisationer som inte använder e-post som primär kommunikationsmetod. Dessa appar riktar sig till frontline-anställda, skrivbordlösa arbetare, fältagenter eller butiksanställda som kanske inte får en e-postadress från sin arbetsgivare, har åtkomst till en dator eller till IT. Med det här projektet kan dessa anställda logga in på företagsprogram genom att ange ett telefonnummer och tur och retur för en kod. Mer information finns i vår [administratörsdokumentation och](../authentication/howto-authentication-sms-signin.md) [i slutanvändardokumentationen.](../user-help/sms-sign-in-explainer.md)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Bjud in interna användare att använda B2B-samarbete

**Typ:** Ny funktion

**Tjänstkategori:** B2b

**Produktfunktion:**

Vi utökar funktionen för B2B-inbjudan så att befintliga interna konton kan bjudas in att använda autentiseringsuppgifter för B2B-samarbete framöver. Detta görs genom att skicka användarobjektet till API:et för inbjudan utöver vanliga parametrar som den inbjudna e-postadressen. Användarens objekt-ID, UPN, gruppmedlemskap, apptilldelning osv. förblir intakta, men framöver kommer de att använda B2B för att autentisera med sina autentiseringsuppgifter för hemklientorganisationen i stället för de interna autentiseringsuppgifter som de använde före inbjudan. Mer information finns i [dokumentationen](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Läget Endast rapport för villkorlig åtkomst är nu allmänt tillgängligt

**Typ:** Ny funktion

**Tjänstkategori:** Villkorlig åtkomst

**Produktfunktion:** Identity Security & Protection

[Med läget Endast rapport för villkorsstyrd åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att framtvinga åtkomstkontroller. Du kan testa endast rapportprinciper i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av läget endast rapporter, med över 26 miljoner användare som redan omfattas av en princip för endast rapporter. Med det här meddelandet skapas nya principer för villkorsstyrd åtkomst i Azure AD som standard i rapportläge. Det innebär att du kan övervaka effekten av dina principer från den tidpunkt då de skapas. Och för dig som använder MS Graph-API:er kan du också [hantera principer för endast rapporter programmatiskt.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbetsboken för insikter om villkorsstyrd åtkomst och rapportering är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänstkategori:** Villkorlig åtkomst

**Produktfunktion:** Identity Security & Protection

Arbetsboken information [och rapportering för villkorlig åtkomst](../conditional-access/howto-conditional-access-insights-reporting.md) ger administratörer en översikt över villkorsstyrd åtkomst i Azure AD i sin klientorganisation. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i realtid. Arbetsboken strömmar data som lagras i Azure Monitor, som du kan konfigurera på några minuter genom att [följa dessa instruktioner.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) För att göra instrumentpanelen mer lätt att identifiera har vi flyttat den till fliken Nya insikter och rapportering på menyn villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Principinformationsbladet för villkorlig åtkomst är i offentlig förhandsversion

**Typ:** Ny funktion

**Tjänstkategori:** Villkorlig åtkomst

**Produktfunktion:** Identity Security & Protection

Det nya [principinformationsbladet](../conditional-access/troubleshoot-conditional-access.md) visar vilka tilldelningar, villkor och kontroller som uppfyllts under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en **rad** på **flikarna** Villkorlig åtkomst eller Endast rapport i Inloggningsinformation.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – april 2020

**Typ:** Ny funktion

**Tjänstkategori:** Företagsappar

**Produktfunktion:** Integrering från tredje part

I april 2020 lade vi till dessa 31 nya appar med federationsstöd i appgalleriet: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO for Jira](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft 365 [Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), [Föra,](../saas-apps/honestly-tutorial.md) [Ally](../saas-apps/ally-tutorial.md), [Arbetsflöde](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Suite](../saas-apps/harmony-tutorial.md), [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md), [empower](https://www.made-in-office.com/en/), [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md) [,GIVANDE](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), Telecom Productivity [Suite](../saas-apps/nitro-productivity-suite-tutorial.md), Trend Micro [Web Security(TMWS)](../saas-apps/trend-micro-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph delta-frågestöd för oAuth2PermissionGrant som är tillgängligt för offentlig förhandsversion

**Typ:** Ny funktion

**Tjänstkategori:** MS Graph

**Produktfunktion:** Utvecklarupplevelse

Delta-fråga för oAuth2PermissionGrant är tillgänglig för offentlig förhandsversion! Nu kan du spåra ändringar utan att kontinuerligt behöva avse Microsoft Graph. [Läs mer.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph deltafrågestöd för organisationskontakt som är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänstkategori:** MS Graph

**Produktfunktion:** Utvecklarupplevelse

Delta-fråga för organisationskontakter är allmänt tillgänglig! Nu kan du spåra ändringar i produktionsappar utan att kontinuerligt behöva avse Microsoft Graph. Ersätt all befintlig kod som kontinuerligt avsöker organisationenKontakta data med deltafråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph deltafrågor för program som är allmänt tillgängliga

**Typ:** Ny funktion

**Tjänstkategori:** MS Graph

**Produktfunktion:** Utvecklarupplevelse

Deltafråga för program är allmänt tillgänglig! Nu kan du spåra ändringar i produktionsappar utan att kontinuerligt behöva avse Microsoft Graph. Ersätt eventuell befintlig kod som kontinuerligt avsöker programdata efter deltafråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph deltafrågestöd för administrativa enheter som är tillgängliga för offentlig förhandsversion

**Typ:** Ny funktion

**Tjänstkategori:** MS Graph

**Produktfunktion:** Developer Experience Delta-fråga för administrativa enheter är tillgänglig för offentlig förhandsversion! Nu kan du spåra ändringar utan att kontinuerligt behöva avse Microsoft Graph. [Läs mer.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Hantera autentiseringstelefonnummer med mera i nya Microsoft Graph beta-API:er

**Typ:** Ny funktion

**Tjänstkategori:** MS Graph

**Produktfunktion:** Utvecklarupplevelse

Dessa API:er är ett viktigt verktyg för att hantera användarnas autentiseringsmetoder. Nu kan du förregistrera och hantera autentiseringarna som används för MFA och lösenordsåterställning via självbetjäning (SSPR). Detta har varit en av de mest efterfrågade funktionerna i Azure AD MFA, SSPR och Microsoft Graph lagringsutrymmen. De nya API:erna som vi har lanserat i den här vågen ger dig möjlighet att:

- Läsa, lägga till, uppdatera och ta bort en användares autentiseringstelefoner
- Återställa en användares lösenord
- Aktivera och inaktivera SMS-inloggning

Mer information finns i [Api-översikt över Azure AD-autentiseringsmetoder.](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

---

### <a name="administrative-units-public-preview"></a>Offentlig förhandsversion av administrativa enheter

**Typ:** Ny funktion

**Tjänstkategori:** Azure AD-roller

**Produktfunktion:** Access Control

Med administrativa enheter kan du bevilja administratörsbehörigheter som är begränsade till en avdelning, region eller ett annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på detaljerad nivå. En användarkontoadministratör kan till exempel uppdatera profilinformation, återställa lösenord och tilldela licenser till användare endast i sin administrativa enhet.

Med hjälp av administrativa enheter kan en central administratör:

- Skapa en administrativ enhet för decentraliserad hantering av resurser
- Tilldela en roll med administrativ behörighet över endast Azure AD-användare i en administrativ enhet
- Fyll i de administrativa enheterna med användare och grupper efter behov

Mer information finns i [Hantering av administrativa enheter i Azure Active Directory (förhandsversion)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Inbyggda roller för skrivaradministratör och skrivartekniker

**Typ:** Ny funktion

**Tjänstkategori:** Azure AD-roller

**Produktfunktion:** Access Control

**Skrivaradministratör:** Användare med den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivarkonfigurationer i Microsoft Universell utskrift-lösningen, inklusive inställningarna för Universell utskrift Connector. De kan samtycka till alla delegerade begäranden om utskriftsbehörighet. Skrivaradministratörer har också åtkomst till utskriftsrapporter. 

**Skrivartekniker:** Användare med den här rollen kan registrera skrivare och hantera skrivarstatus i Microsoft Universell utskrift lösningen. De kan också läsa all information om anslutningsappen. Viktiga uppgifter som en skrivartekniker inte kan utföra är att ange användarbehörigheter på skrivare och dela skrivare. [Läs mer.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Inbyggd roll för hybrididentitetsadministratör

**Typ:** Ny funktion

**Tjänstkategori:** Azure AD-roller

**Produktfunktion:** Access Control

Användare i den här rollen kan aktivera, konfigurera och hantera tjänster och inställningar som rör aktivering av hybrididentitet i Azure AD. Den här rollen ger möjlighet att konfigurera Azure AD till någon av de tre autentiseringsmetoder som stöds&#8212;Synkronisering av lösenordshashar (PHS), direktautentisering (PTA) eller federation (AD FS eller tredjeparts federationsprovider)&#8212;och distribuera relaterad lokal infrastruktur för att aktivera dem. Den lokala infrastrukturen omfattar etablering och PTA-agenter. Den här rollen ger möjlighet att aktivera sömlös enkel Sign-On (S-SSO) för att aktivera sömlös autentisering på icke-Windows 10-enheter eller icke-Windows Server 2016-datorer. Dessutom ger den här rollen möjlighet att se inloggningsloggar och få åtkomst till hälsa och analys i övervaknings- och felsökningssyften. [Läs mer.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Inbyggd roll för nätverksadministratör

**Typ:** Ny funktion

**Tjänstkategori:** Azure AD-roller

**Produktfunktion:** Access Control

Användare med den här rollen kan granska microsofts rekommendationer om perimeterarkitektur för nätverk som baseras på nätverkstelemetri från deras användarplatser. Nätverksprestanda för Microsoft 365 förlitar sig på noggrann perimeterarkitektur för företagskundnätverk, som vanligtvis är användarplatsspecifik. Med den här rollen kan du redigera identifierade användarplatser och konfigurera nätverksparametrar för dessa platser för att underlätta förbättrade telemetrimätningar och designrekommendationer. [Läs mer.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Massaktivitet och hämtningar i Azure AD-administratörsportalen

**Typ:** Ny funktion

**Tjänstkategori:** Användarhantering

**Produktfunktion:** Katalog

Nu kan du utföra massaktiviteter på användare och grupper i Azure AD genom att ladda upp en CSV-fil i Azure AD-administratörsportalen. Du kan skapa användare, ta bort användare och bjuda in gästanvändare. Och du kan lägga till och ta bort medlemmar från en grupp.

Du kan också ladda ned listor över Azure AD-resurser från Azure AD-administratörsportalen. Du kan ladda ned listan över användare i katalogen, listan över grupper i katalogen och medlemmarna i en viss grupp.

Mer information finns i följande avsnitt:

- [Skapa användare eller](../enterprise-users/users-bulk-add.md) [bjuda in gästanvändare](../external-identities/tutorial-bulk-invite.md)
- [Ta bort användare](../enterprise-users/users-bulk-delete.md) [eller återställa borttagna användare](../enterprise-users/users-bulk-restore.md)
- [Ladda ned lista över användare](../enterprise-users/users-bulk-download.md) eller Ladda ned lista över [grupper](../enterprise-users/groups-bulk-download.md)
- [Lägga till (importera) medlemmar](../enterprise-users/groups-bulk-import-members.md) eller [ta bort medlemmar](../enterprise-users/groups-bulk-remove-members.md) eller Ladda ned lista över [medlemmar](../enterprise-users/groups-bulk-download-members.md) för en grupp

---

### <a name="my-staff-delegated-user-management"></a>Min personal delegerad användarhantering

**Typ:** Ny funktion

**Tjänstkategori:** Användarhantering

**Produktfunktion:**

Min personal gör att firstline-chefer, till exempel butikschefer, kan se till att personalen kan komma åt sina Azure AD-konton. I stället för att förlita sig på en central support kan organisationer delegera vanliga uppgifter, till exempel återställa lösenord eller ändra telefonnummer, till en chef på förstalinjen. Med Min personal kan en användare som inte kan komma åt sitt konto få åtkomst igen med bara några klick, utan att support eller IT-personal krävs. Mer information finns i Hantera användare med Min personal [(förhandsversion)](../roles/my-staff-configure.md) och [Delegera användarhantering med Min personal (förhandsversion)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>En uppgraderad slutanvändarupplevelse i åtkomstgranskningar

**Typ:** Funktionen har ändrats

**Tjänstkategori:** Åtkomstgranskningar

**Produktfunktion:** Identitetsstyrning

Vi har uppdaterat granskarupplevelsen för Azure AD-åtkomstgranskningar i Mina appar portalen. I slutet av april visas en banderoll för granskare av dina granskare av Azure AD-åtkomst som gör att de kan testa den uppdaterade upplevelsen i Min åtkomst. Observera att den uppdaterade upplevelsen för åtkomstgranskningar har samma funktioner som den aktuella upplevelsen, men med ett förbättrat användargränssnitt ovanpå nya funktioner som gör det möjligt för användarna att vara produktiva. [Du kan läsa mer om den uppdaterade upplevelsen här.](../governance/perform-access-review.md) Den här offentliga förhandsversionen varar fram till slutet av juli 2020. I slutet av juli dirigeras granskare som inte har valt att använda förhandsversionen automatiskt till att Min åtkomst att utföra åtkomstgranskningar. Om du vill att granskarna ska växla över permanent till förhandsversionen i Min åtkomst kan [du skicka en begäran här.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday-appar för inkommande användareablering och tillbakaskrivning har nu stöd för de senaste versionerna av Workday Web Services-API:et

**Typ:** Funktionen har ändrats

**Tjänstkategori:** Appetablering

**Produktfunktion:** 

Baserat på kundfeedback har vi nu uppdaterat workday-apparna för inkommande användareablering och tillbakaskrivning i företagsappgalleriet för att stödja de senaste versionerna av WORKday Web Services (WWS)-API:et. Med den här ändringen kan kunder ange den WWS API-version som de vill använda i anslutningssträngen. Detta ger kunderna möjlighet att hämta fler HR-attribut som är tillgängliga i workday-versionerna. Workday-tillbakaskrivningsappen använder nu den rekommenderade Change_Work_Contact_Info Workday-webbtjänsten för att lösa begränsningarna med Maintain_Contact_Info.

Om ingen version anges i anslutningssträngen fortsätter workday-etableringsapparna som standard att använda WWS v21.1 För att växla till de senaste Workday-API:erna för inkommande användareablering måste kunderna uppdatera anslutningssträngen enligt dokumenten i självstudien och även uppdatera XPATHs som används för Workday-attribut enligt bladets referensguide för [Workday-attribut.](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) 

Om du vill använda det nya API:et för tillbakaskrivning krävs inga ändringar i workday-appen för tillbakaskrivning. På Workday-sidan ser du till att kontot workday Integration System User (ISU) har behörighet att anropa Change_Work_Contact-affärsprocessen enligt dokumenten i självstudieavsnittet Konfigurera behörigheter för [affärsprocesssäkerhetsprincip.](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) 

Vi har uppdaterat vår [självstudieguide så att](../saas-apps/workday-inbound-tutorial.md) den återspeglar stödet för den nya API-versionen.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Användare med standardåtkomstrollen är nu i omfånget för etablering

**Typ:** Ändrad funktion

**Tjänstkategori:** Appetablering

**Produktfunktion:** Hantering av identitetslivscykel

Tidigare har användare med standardåtkomstrollen inte kunnat etableras. Vi har hört feedback om att kunder vill att användare med den här rollen ska vara i omfånget för etablering. Från och med 16 april 2020 tillåter alla nya etableringskonfigurationer att användare med standardåtkomstrollen etableras. Gradvis ändrar vi beteendet för befintliga etableringskonfigurationer för att stödja etablering av användare med den här rollen. [Läs mer.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Uppdaterat etableringsgränssnitt

**Typ:** Ändrad funktion

**Tjänstkategori:** Appetablering

**Produktfunktion:** Livscykelhantering för identitet

Vi har uppdaterat etableringsupplevelsen för att skapa en mer fokuserad hanteringsvy. När du navigerar till etableringsbladet för ett företagsprogram som redan har konfigurerats kan du enkelt övervaka förloppet för etablering och hantering av åtgärder som att starta, stoppa och starta om etableringen. [Läs mer.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Validering av dynamiska gruppregel är nu tillgängligt för offentlig förhandsversion

**Typ:** Ändrad funktion

**Tjänstkategori:** Grupphantering

**Produktfunktion:** Samarbete

Azure Active Directory (Azure AD) är nu ett sätt att verifiera regler för dynamiska grupper. På fliken **Verifiera regler** kan du verifiera din dynamiska regel mot exempelgruppmedlemmar för att bekräfta att regeln fungerar som förväntat. När administratörer skapar eller uppdaterar dynamiska gruppregler vill de veta om en användare eller enhet kommer att vara medlem i gruppen. På så sätt kan du utvärdera om en användare eller enhet uppfyller regelkriterierna och hjälper till vid felsökning när medlemskap inte förväntas.

Mer information finns i Verifiera [en regel för dynamiskt gruppmedlemskap (förhandsversion).](../enterprise-users/groups-dynamic-rule-validation.md)

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identitetssäkerhetspoäng – uppdateringar av säkerhetsstandarder och MFA-förbättringsåtgärder

**Typ:** Ändrad funktion

**Tjänstkategori:** Ej a-A

**Produktfunktion:** Identity Security & Protection

**Stöd för säkerhetsstandarder för Azure AD-förbättringsåtgärder:** Microsoft Secure Score kommer att uppdatera förbättringsåtgärder för att stödja standardinställningar för säkerhet i [Azure AD,](./concept-fundamentals-security-defaults.md)vilket gör det enklare att skydda din organisation med förkonfigurerade säkerhetsinställningar för vanliga attacker. Detta påverkar följande förbättringsåtgärder:

- Se till att alla användare kan slutföra multifaktorautentisering för säker åtkomst
- Kräv MFA för administrativa roller
- Aktivera princip för att blockera äldre autentisering
 
**Uppdatering av MFA-förbättringsåtgärd:** Microsoft Secure Score har tagit bort tre förbättringsåtgärder som är centrerade kring multifaktorautentisering och lagt till två för att återspegla behovet av att företag säkerställer säkerheten längst upp och tillämpar principer som fungerar med verksamheten.

Förbättringar har tagits bort:

- Registrera alla användare för multifaktorautentisering
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Förbättringsåtgärder har lagts till:

- Se till att alla användare kan slutföra multifaktorautentisering för säker åtkomst
- Kräv MFA för administrativa roller

De här nya förbättringsåtgärderna kräver att du registrerar dina användare eller administratörer för multifaktorautentisering (MFA) i katalogen och upprättar rätt uppsättning principer som passar organisationens behov. Huvudmålet är att ha flexibilitet samtidigt som du säkerställer att alla användare och administratörer kan autentisera med flera faktorer eller frågor om riskbaserad identitetsverifiering. Det kan ske i form av att ha flera principer som tillämpar begränsade beslut eller anger standardinställningar för säkerhet (från och med den 16 mars) som låter Microsoft bestämma när de ska uppmana användarna att använda MFA. [Läs mer om vad som är nytt i Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Mars 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Ohanterade Azure Active Directory I B2B-uppdatering för mars 2021

**Typ:** Planera för förändring  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
**Från och med den 31 mars 2021** kommer Microsoft inte längre att stödja inlösning av inbjudningar genom att skapa ohanterade Azure Active Directory-konton (Azure AD) och klienter för B2B-samarbetsscenarier. Som förberedelse för detta rekommenderar vi att du väljer att använda autentisering [med e-post för lösenord en gång.](../external-identities/one-time-passcode.md)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Användare med standardåtkomstrollen kommer att omfattas av etablering

**Typ:** Planera för förändring  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Livscykelhantering för identitet
 
Tidigare har användare med standardåtkomstrollen inte kunnat etableras. Vi har hört feedback om att kunder vill att användare med den här rollen ska vara i omfånget för etablering. Vi arbetar med att distribuera en ändring så att alla nya etableringskonfigurationer tillåter att användare med standardåtkomstrollen etableras. Gradvis ändrar vi beteendet för befintliga etableringskonfigurationer för att stödja etablering av användare med den här rollen. Ingen kundåtgärd krävs. Vi publicerar en uppdatering i vår dokumentation [när](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) ändringen är på plats.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samarbete kommer att vara tillgängligt i Microsoft Azure som drivs av 21Vianet-klienter (Azure China 21Vianet)

**Typ:** Planera för förändring  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Azure AD B2B-samarbetsfunktionerna kommer att göras tillgängliga i Microsoft Azure som drivs av 21Vianet-klienter (Azure China 21Vianet), vilket gör det möjligt för användare i en Azure China 21Vianet-klientorganisation att samarbeta sömlöst med användare i andra Azure China 21Vianet-klienter. [Läs mer om Azure AD B2B-samarbete](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Utformning av e-B2B Collaboration Azure AD-B2B Collaboration inbjudan

**Typ:** Planera för förändring  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
[E-postmeddelandena](../external-identities/invitation-email-elements.md) som skickas av Azure AD B2B Collaboration Invitation Service för att bjuda in användare till katalogen kommer att göras om så att inbjudningsinformationen och användarens nästa steg blir tydligare.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Principändringar för HomeRealmDiscovery visas i granskningsloggarna

**Typ:** Fast  
**Tjänstkategori:** Revision  
**Produktfunktion:** Övervakning & rapportering
 
Vi har åtgärdat en bugg där ändringar i [Principen HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) inte inkluderades i granskningsloggarna. Nu kan du se när och hur principen ändrades och av vem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – mars 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I mars 2020 lade vi till dessa 51 nya appar med federationsstöd i appgalleriet: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One China](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md), [iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), contexxt.ai [SPHERE](https://contexxt-sphere.com/login) [,Ställ av Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [Digital Signage](https://spark-dev.pixelnebula.com/login), Logz.io – Cloud [Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://www.bizzcontact.app/), [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision Compas](../saas-apps/netvision-compas-tutorial.md), [FCM HUB](../saas-apps/fcm-hub-tutorial.md), EA [A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](../saas-apps/golinks-tutorial.md), [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [LIFT](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](../saas-apps/skills-workflow-tutorial.md), Node [Insight](https://admin.nodeinsight.com/AADLogin.aspx), [IP Platform](../saas-apps/ip-platform-tutorial.md), [InVision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Grafive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md), Campus [Catchpoint](../saas-apps/campus-cafe-tutorial.md), [Code42](../saas-apps/code42-tutorial.md) [](../saas-apps/catchpoint-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration tillgängligt i Azure Government klienter

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** B2B/B2C
 
Samarbetsfunktionerna i Azure AD B2B är nu tillgängliga mellan vissa Azure Government klienter.  Om du vill ta reda på om din klientorganisation kan använda dessa funktioner följer du anvisningarna i Hur vet jag om [B2B-samarbete](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)är tillgängligt i min Azure US Government-klient? .

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integrering för Azure Logs är nu tillgänglig i Azure Government

**Typ:** Ny funktion  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Azure Monitor integrering med Azure AD-loggar är nu tillgänglig i Azure Government. Du kan dirigera Azure AD-loggar (gransknings- och inloggningsloggar) till ett lagringskonto, en händelsehubb och en Log Analytics. Läs den detaljerade dokumentationen [samt distributionsplaner](../reports-monitoring/concept-activity-logs-azure-monitor.md) [för rapportering och övervakning av](../reports-monitoring/plan-monitoring-and-reporting.md) Azure AD-scenarier.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identity Protection-uppdatering i Azure Government

**Typ:** Ny funktion  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection

Vi är glada över att kunna dela att vi nu har distribuerat den [uppdaterade Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)   i [Microsoft Azure Government portalen.](https://portal.azure.us/) Mer information finns i vårt [blogginlägg](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Haveriberedskap: Ladda ned och lagra etableringskonfigurationen

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Livscykelhantering för identitet
 
Azure AD-etableringstjänsten tillhandahåller en omfattande uppsättning konfigurationsfunktioner. Kunder måste kunna spara sin konfiguration så att de kan referera till den senare eller återställa till en känd bra version. Vi har lagt till möjligheten att ladda ned etableringskonfigurationen som en JSON-fil och ladda upp den när du behöver den. [Läs mer](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (självbetjäning av lösenordsåterställning) kräver nu två portar för administratörer i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet) 

**Typ:** Ändrad funktion  
**Tjänstkategori:** Self-Service lösenordsåterställning  
**Produktfunktion:** Identity Security & Protection
 
Tidigare i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet) behövde administratörer som använder lösenordsåterställning via självbetjäning (SSPR) för att återställa sina egna lösenord bara en "grind" (utmaning) för att bevisa sin identitet. I offentliga och andra nationella moln måste administratörer vanligtvis använda två portar för att bevisa sin identitet när de använder SSPR. Men eftersom vi inte hade stöd för SMS eller telefonsamtal i Azure China 21Vianet tillät vi lösenordsåterställning via en port av administratörer.

Vi skapar SSPR-funktionsparitet mellan Azure China 21Vianet och det offentliga molnet. Framöver måste administratörer använda två portar när de använder SSPR. SMS, telefonsamtal och Authenticator-appmeddelanden och koder kommer att stödjas. [Läs mer](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Lösenordslängden är begränsad till 256 tecken

**Typ:** Ändrad funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
För att säkerställa tillförlitligheten för Azure AD-tjänsten är användarlösenorden nu begränsade till 256 tecken långa. Användare med lösenord som är längre än detta uppmanas att ändra sina lösenord vid efterföljande inloggning, antingen genom att kontakta administratören eller genom att använda funktionen för lösenordsåterställning via självbetjäning.

Den här ändringen aktiverades den 13 mars 2020, kl. 10 PST (18:00 UTC) och felet är AADSTS 50052, InvalidPasswordExceedsMaxLength. Mer information [finns i meddelandet om](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) större ändringar.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD-inloggningsloggar är nu tillgängliga för alla kostnadsfria klienter via Azure Portal

**Typ:** Ändrad funktion  
**Tjänstkategori:** Rapportering  
**Produktfunktion:** Övervakning & rapportering
 
Från och med nu kan kunder som har kostnadsfria klienter komma åt [Azure AD-inloggningsloggarna från Azure Portal](../reports-monitoring/concept-sign-ins.md) i upp till 7 dagar. Tidigare var inloggningsloggar endast tillgängliga för kunder med Azure Active Directory Premium licenser. Med den här ändringen kan alla klienter komma åt dessa loggar via portalen.

> [!NOTE]
> Kunderna behöver fortfarande en Premium-licens (Azure Active Directory Premium P1 eller P2) för att få åtkomst till inloggningsloggarna via Microsoft Graph API och Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Alternativ för utfasning av katalogomfattande grupper från Grupp allmänna inställningar på Azure Portal

**Typ:** Deprecated  
**Tjänstkategori:** Grupphantering  
**Produktfunktion:** Samarbete

För att ge kunderna ett mer flexibelt sätt att skapa katalogomfattande grupper  som bäst uppfyller deras behov har vi ersatt alternativet Katalogomfattande grupper från inställningarna För grupper i Azure Portal med en länk till dokumentationen för dynamiska  >   [grupper.](../enterprise-users/groups-dynamic-membership.md) Vi har förbättrat vår dokumentation med fler instruktioner så att administratörer kan skapa användargrupper för alla användare som inkluderar eller exkluderar gästanvändare.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Kommande ändringar i anpassade kontroller

**Typ:** Planera för förändring  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 
Vi planerar att ersätta den aktuella förhandsversionen av anpassade kontroller med en metod som gör det möjligt för autentiseringsfunktioner från partner att fungera sömlöst med Azure Active Directory och slutanvändarupplevelsen. Idag har partner MFA-lösningar följande begränsningar: de fungerar bara när ett lösenord har angetts. de fungerar inte som MFA för stegvis autentisering i andra viktiga scenarier; och de integrerar inte med funktioner för hantering av slutanvändare eller administrativa autentiseringsuppgifter. Med den nya implementeringen kan autentiseringsfaktorer från partner fungera tillsammans med inbyggda faktorer för nyckelscenarier, inklusive registrering, användning, MFA-anspråk, stega upp autentisering, rapportering och loggning. 

Anpassade kontroller kommer att fortsätta att stödjas i förhandsversionen tillsammans med den nya designen tills den blir allmänt tillgänglig. Då ger vi kunderna tid att migrera till den nya designen. På grund av begränsningarna i den aktuella metoden kommer vi inte att registrera nya leverantörer förrän den nya designen är tillgänglig. Vi har ett nära samarbete med kunder och leverantörer och kommer att förmedla tidslinjen när vi närmar oss. [Läs mer](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score – uppdatering av MFA-förbättringsåtgärd

**Typ:** Planera för förändring  
**Tjänstkategori:** Mfa  
**Produktfunktion:** Identity Security & Protection
 
För att återspegla behovet för företag att säkerställa högsta säkerhet samtidigt som principer som fungerar med verksamheten tillämpas, tar Microsoft Secure Score bort tre förbättringsåtgärder som är centrerade kring multifaktorautentisering (MFA) och lägger till två.

Följande förbättringsåtgärder tas bort:

- Registrera alla användare för MFA
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Följande förbättringsåtgärder kommer att läggas till:

- Se till att alla användare kan slutföra MFA för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringsåtgärder kräver att du registrerar användare eller administratörer för MFA i katalogen och upprättar rätt uppsättning principer som passar organisationens behov. Huvudmålet är att ha flexibilitet samtidigt som du säkerställer att alla användare och administratörer kan autentisera med flera faktorer eller riskbaserade frågor om identitetsverifiering. Detta kan ske i form av att ange standardvärden för säkerhet som låter Microsoft bestämma när de ska uppmana användare att använda MFA, eller att ha flera principer som tillämpar begränsade beslut. Som en del av dessa förbättringar tas principer för baslinjeskydd inte längre med i bedömningsberäkningar. [Läs mer om vad som kommer i Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU-val

**Typ:** Ny funktion  
**Tjänstkategori:** Azure AD Domain Services  
**Produktfunktion:** Azure AD Domain Services
 
Vi har hört feedback om att Azure AD Domain Services vill ha mer flexibilitet när det gäller att välja prestandanivåer för sina instanser. Från och med 1 februari 2020 växlade vi från en dynamisk modell (där Azure AD avgör prestanda och prisnivå baserat på antal objekt) till en självvalsmodell. Nu kan kunderna välja en prestandanivå som matchar deras miljö. Den här ändringen gör det också möjligt för oss att aktivera nya scenarier som Resursskogar och Premium-funktioner som dagliga säkerhetskopieringar. Antalet objekt är nu obegränsat för alla SKU:er, men vi fortsätter att erbjuda förslag på antal objekt för varje nivå.

**Ingen omedelbar kundåtgärd krävs.** För befintliga kunder avgör den dynamiska nivå som används den 1 februari 2020 den nya standardnivån. Det finns ingen pris- eller prestandapåverkan på grund av den här ändringen. Framöver kommer Azure AD DS kunder att behöva utvärdera prestandakrav när katalogstorleken och arbetsbelastningens egenskaper ändras. Att växla mellan tjänstnivåer fortsätter att vara en driftstoppsåtgärd, och vi kommer inte längre automatiskt att flytta kunder till nya nivåer baserat på katalogtillväxten. Dessutom kommer det inte att ske några prisökningar, och nya priser kommer att anpassas till vår aktuella faktureringsmodell. Mer information finns i dokumentationen [Azure AD DS SKU:er](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) och [Azure AD Domain Services sidan med prisinformation.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – februari 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I februari 2020 har vi lagt till dessa 31 nya appar med federationsstöd i appgalleriet: 

[IamIP Patent Platform](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [NS1 SSO for Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), i händelse av en kris – [onlineportalen,](../saas-apps/in-case-of-crisis-online-portal-tutorial.md) [IQ Cloud Design](../saas-apps/bic-cloud-design-tutorial.md), [Beekeeper Azure AD Data Connector,](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada-kommandot](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), New Relic [(Begränsad version)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](../saas-apps/ticketmanager-tutorial.md), Mall-chooser för [Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](../saas-apps/health-support-system-tutorial.md), [MRELEASE](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [HiveDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md), [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nya etableringsanslutningsprogram i Azure AD-programgalleriet – februari 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-stöd för FIDO2-säkerhetsnycklar i hybridmiljöer

**Typ:** Ny funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
Vi presenterar den offentliga förhandsversionen av Azure AD-stöd för FIDO2-säkerhetsnycklar i hybridmiljöer. Användare kan nu använda FIDO2-säkerhetsnycklar för att logga in på sina Hybrid Azure AD-anslutna Windows 10-enheter och få sömlös inloggning till sina lokala och molnbaserade resurser. Stöd för hybridmiljöer har varit den mest efterfrågade funktionen från våra lösenordslösa kunder sedan vi först lanserade den offentliga förhandsversionen för FIDO2-stöd i Azure AD-anslutna enheter. Lösenordsfri autentisering med hjälp av avancerade tekniker som biometri och kryptering med offentliga/privata nycklar ger bekvämlighet och användarvänlighet samtidigt som den är säker. Med den här offentliga förhandsversionen kan du nu använda modern autentisering som FIDO2-säkerhetsnycklar för att få åtkomst till traditionella Active Directory-resurser. Mer information finns i [SSO to on-premises resources (Enkel inloggning till lokala resurser).](../authentication/howto-authentication-passwordless-security-key-on-premises.md) 

Kom igång genom att gå [till aktivera FIDO2-säkerhetsnycklar för din](../authentication/howto-authentication-passwordless-security-key.md) klient för stegvisa instruktioner. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Den nya upplevelsen Mitt konto är nu allmänt tillgänglig

**Typ:** Funktionen har ändrats  
**Tjänstkategori:** Min profil/mitt konto  
**Produktfunktion:** Slutanvändarupplevelser
 
Mitt konto, en enda butik för alla slutanvändarkontohanteringsbehov, är nu allmänt tillgängligt! Slutanvändare kan komma åt den här nya webbplatsen via URL eller i rubriken för den Mina appar upplevelsen. Läs mer om alla självbetjäningsfunktioner som den nya upplevelsen erbjuder i [Översikt över Min kontoportal.](../user-help/my-account-portal-overview.md)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Webbadressen för mitt konto uppdateras till myaccount.microsoft.com

**Typ:** Ändrad funktion  
**Tjänstkategori:** Min profil/mitt konto  
**Produktfunktion:** Slutanvändarupplevelser
 
Den nya slutanvändarupplevelsen för Mitt konto kommer att uppdatera dess URL `https://myaccount.microsoft.com` till nästa månad. Hitta mer information om upplevelsen och alla självbetjäningsfunktioner för kontot som slutanvändarna får via [portalen Mitt konto.](../user-help/my-account-portal-overview.md)

---

## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Den nya Mina appar-portalen är nu allmänt tillgänglig

**Typ:** Planera för förändring  
**Tjänstkategori:** Mina appar  
**Produktfunktion:** Slutanvändarupplevelser
 
Uppgradera din organisation till den nya Mina appar-portalen som nu är allmänt tillgänglig! Mer information om den nya portalen och samlingar finns i [Skapa samlingar på Mina appar portalen.](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbetsytor i Azure AD har bytt namn till samlingar

**Typ:** Ändrad funktion  
**Tjänstkategori:** Mina appar   
**Produktfunktion:** Slutanvändarupplevelser
 
Arbetsytor, som filteradministratörerna kan konfigurera för att organisera sina användares appar, kallas nu samlingar. Mer information om hur du konfigurerar dem finns [i Skapa samlingar på Mina appar portalen.](../manage-apps/access-panel-collections.md)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C registrering och inloggning för Phone med anpassad princip (allmänt förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C
 
Med registrering och inloggning via telefonnummer kan utvecklare och företag tillåta sina kunder att registrera sig och logga in med ett lösenord som skickas till användarens telefonnummer via SMS. Med den här funktionen kan kunden också ändra sitt telefonnummer om de förlorar åtkomst till sin telefon. Tack vare anpassade principer och registrering och inloggning via telefon kan utvecklare och företag kommunicera sitt varumärke via sidanpassning. Lär dig hur [du ställer in registrering och inloggning via telefon med anpassade principer i Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nya etableringsanslutningar i Azure AD-programgalleriet – januari 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk användarkontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – januari 2020

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part
 
I januari 2020 har vi lagt till dessa 33 nya appar med federationsstöd i appgalleriet: 

[JOSA](../saas-apps/josa-tutorial.md), [Fastly Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](../saas-apps/upshotly-tutorial.md), [LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SmartWork](https://www.intumit.com/teams-smartwork/), [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, och JDE,](../saas-apps/ssogen-tutorial.md) [Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management Platform,](../saas-apps/yuhu-property-management-platform-tutorial.md) [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB för Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO,](../saas-apps/contractsafe-saml2-sso-tutorial.md) [Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), Squelch Cloud Office365 Connector, [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Alogi Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Två nya Identity Protection-identifieringar

**Typ:** Ny funktion  
**Tjänstkategori:** Identity Protection  
**Produktfunktion:** Identity Security & Protection
 
Vi har lagt till två nya typer av länkade identifieringstyper för inloggning i Identity Protection: Misstänkta regler för inkorgsmanipulering och Omöjlig resa. Offlineidentifieringarna identifieras av Microsoft Cloud App Security (MCAS) och påverkar risken för användare och inloggning i Identity Protection. Mer information om dessa identifieringar finns i våra [typer av inloggningsrisker.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Stor ändring: URI-fragment kommer inte att skickas via inloggningsomdirigeringen

**Typ:** Ändrad funktion  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering
 
Från och med 8 februari 2020 kommer tjänsten att lägga till ett tomt fragment i begäran när en begäran skickas till login.microsoftonline.com för att logga in en användare.  Detta förhindrar en klass av omdirigeringsattacker genom att webbläsaren rensar alla befintliga fragment i begäran. Inget program bör vara beroende av det här beteendet. Mer information finns i Breaking [changes (Större ändringar)](../develop/reference-breaking-changes.md#february-2020) i dokumentationen för Microsoft Identity Platform.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrera SAP SuccessFactors-etablering i Azure AD och lokal AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Appetablering  
**Produktfunktion:** Hantering av identitetslivscykel

Du kan nu integrera SAP SuccessFactors som en auktoritativ identitetskälla i Azure AD. Den här integreringen hjälper dig att automatisera livscykeln för identiteter från start till slut, inklusive användning av HR-baserade händelser, till exempel nyanställda eller uppsägningar, för att styra etableringen av Azure AD-konton.

Mer information om hur du konfigurerar ingående etablering för SAP SuccessFactors till Azure AD finns i självstudien Konfigurera [automatisk etablering för SAP SuccessFactors.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Stöd för anpassade e-postmeddelanden i Azure AD B2C (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** B2C – Konsumentidentitetshantering  
**Produktfunktion:** B2B/B2C

Nu kan du använda Azure AD B2C för att skapa anpassade e-postmeddelanden när användarna registrerar sig för att använda dina appar. Genom att använda DisplayControls (för närvarande i förhandsversion) och en e-postleverantör från tredje part (till exempel [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)eller en anpassad REST API) kan du använda din egen e-postmall,  Från adress och ämnestext, samt stöd för lokalisering och anpassade engångslösenord (OTP).

Mer information finns i Anpassad [e-postverifiering i Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Ersätta baslinjeprinciper med standardinställningar för säkerhet

**Typ:** Ändrad funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Identitetssäkerhet och -skydd

Som en del av en modell som är säker som standard för autentisering tar vi bort de befintliga baslinjeskyddsprinciperna från alla klienter. Den här borttagningen är avsedd för slutförande i slutet av februari. Ersättning av dessa baslinjeskyddsprinciper är standardvärden för säkerhet. Om du har använt principer för baslinjeskydd måste du planera att gå över till den nya standardprincipen för säkerhet eller till Villkorlig åtkomst. Om du inte har använt dessa principer behöver du inte göra något.

Mer information om de nya standardinställningarna för säkerhet finns i [Vad är standardinställningar för säkerhet?](./concept-fundamentals-security-defaults.md) Mer information om principer för villkorlig åtkomst finns i [Vanliga principer för villkorlig åtkomst.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för attributet SameSite och Chrome 80

**Typ:** Planera för förändring  
**Tjänstkategori:** Autentiseringar (inloggningar)  
**Produktfunktion:** Användarautentisering

Som en del av en modell som är säker som standard för cookies ändrar webbläsaren Chrome 80 hur den behandlar cookies utan `SameSite` attributet . En cookie som inte anger attributet behandlas som om den var inställd på , vilket resulterar i att Chrome blockerar vissa scenarier för delning av cookies mellan domäner som din `SameSite` app kan vara beroende `SameSite=Lax` av. Om du vill behålla det äldre Chrome-beteendet kan du använda attributet och lägga till ytterligare ett attribut, så att cookies mellan webbplatser endast kan `SameSite=None` `Secure` nås via HTTPS-anslutningar. Chrome är schemalagt att slutföra den här ändringen senast den 4 februari 2020.

Vi rekommenderar att alla våra utvecklare testar sina appar med hjälp av den här vägledningen:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja.**

- Ange standardvärdet för **attributet SameSite** till **Ingen.**

- Lägg till ytterligare `SameSite` ett attribut för **Secure**.

Mer information finns i Kommande ändringar av [SameSite-cookie](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) i ASP.NET och ASP.NET Core och Potentiella avbrott på kundwebbplatser och Microsofts produkter och tjänster i [Chrome version 79 och senare.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Ny snabbkorrigering för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Fast  
**Tjänstkategori:** Microsoft Identity Manager  
**Produktfunktion:** Hantering av identitetslivscykel

Ett paket för snabbkorrigeringar (version 4.6.34.0) är tillgängligt för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här samlade paketet löser problem och lägger till förbättringar som beskrivs i avsnittet "Problem har åtgärdats och förbättringar har lagts till i den här uppdateringen".

Mer information och hur du laddar ned snabbkorrigeringspaketet finns [i samlad uppdatering Microsoft Identity Manager 2016 Service Pack 2 (version 4.6.34.0) är tillgänglig.](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Ny AD FS-appaktivitetsrapport som hjälper dig att migrera appar till Azure AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso

Använd den nya Active Directory Federation Services (AD FS)-AD FS-appaktivitetsrapporten i Azure Portal för att identifiera vilka av dina appar som kan migreras till Azure AD. Rapporten utvärderar alla AD FS för kompatibilitet med Azure AD, kontrollerar eventuella problem och ger vägledning om hur du förbereder enskilda appar för migrering.

Mer information finns i Använda [rapporten för AD FS för att migrera program till Azure AD.](../manage-apps/migrate-adfs-application-activity.md)

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nytt arbetsflöde för användare att begära administratörsmedgivande (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Access Control

Det nya arbetsflödet för administratörsmedgivande ger administratörer ett sätt att bevilja åtkomst till appar som kräver administratörsgodkännande. Om en användare försöker komma åt en app, men inte kan ge sitt medgivande, kan de nu skicka en begäran om administratörsgodkännande. Begäran skickas via e-post och placeras i en kö som är tillgänglig från Azure Portal för alla administratörer som har angetts som granskare. När en granskare vidtar åtgärder för en väntande begäran meddelas de begärande användarna om åtgärden.

Mer information finns i Konfigurera [arbetsflödet för administratörsmedgivande (förhandsversion).](../manage-apps/configure-admin-consent-workflow.md)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Ny Azure AD App konfigurationsupplevelse för registreringstoken för att hantera valfria anspråk (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Utvecklarupplevelse

Det nya **konfigurationsbladet Azure AD App** Registreringstoken på Azure Portal visar nu apputvecklare en dynamisk lista över valfria anspråk för sina appar. Den här nya upplevelsen hjälper till att effektivisera azure AD-appmigreringar och minimera valfria felkonfigurationer av anspråk.

Mer information finns i Ange [valfria anspråk till din Azure AD-app.](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nytt arbetsflöde för godkännande i två steg i Azure AD-berättigandehantering (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Berättigandehantering

Vi har introducerat ett nytt arbetsflöde för godkännande i två steg som gör att du kan kräva att två godkännare godkänner en användares begäran till ett åtkomstpaket. Du kan till exempel ange den så att den begärande användarens chef först måste godkänna, och sedan kan du även kräva att en resursägare godkänner. Om en av godkännarna inte godkänner beviljas inte åtkomst.

Mer information finns i Ändra [inställningar för begäran och godkännande för ett åtkomstpaket i Azure AD-berättigandehantering.](../governance/entitlement-management-access-package-request-policy.md)

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdateringar av Mina appar tillsammans med nya arbetsytor (allmänt tillgängliga förhandsversion)

**Typ:** Ny funktion  
**Tjänstkategori:** Mina appar  
**Produktfunktion:** Integrering från tredje part

Nu kan du anpassa hur organisationens användare visar och får åtkomst till den uppdaterade Mina appar upplevelsen. Den här nya upplevelsen innehåller även den nya arbetsytefunktionen, som gör det enklare för användarna att hitta och organisera appar.

Mer information om den nya Mina appar och hur du skapar arbetsytor finns i [Skapa arbetsytor på Mina appar portalen.](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Googles stöd för sociala ID:n för Azure AD B2B-samarbete (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstkategori:** B2b  
**Produktfunktion:** Användarautentisering

Nytt stöd för användning av Googles sociala ID:n (Gmail-konton) i Azure AD hjälper till att göra samarbetet enklare för dina användare och partner. Dina partner behöver inte längre skapa och hantera ett nytt Microsoft-specifikt konto. Microsoft Teams har nu fullständigt stöd för Google-användare på alla klienter och i vanliga och klientrelaterade autentiseringsslutpunkter.

Mer information finns i Lägga [till Google som identitetsprovider för B2B-gästanvändare.](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge mobile-stöd för villkorlig åtkomst och enkel inloggning (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstkategori:** Villkorlig åtkomst  
**Produktfunktion:** Identity Security & Protection

Azure AD för Microsoft Edge på iOS och Android har nu stöd för enkel Sign-On Azure AD och villkorlig åtkomst:

- **Microsoft Edge enkel inloggning (SSO):** Enkel inloggning är nu tillgängligt för interna klienter (till exempel Microsoft Outlook och Microsoft Edge) för alla Azure AD-anslutna appar.

- **Microsoft Edge villkorlig åtkomst:** Via programbaserade principer för villkorlig åtkomst måste användarna använda Microsoft Intune webbläsare, till exempel Microsoft Edge.

Mer information om villkorlig åtkomst och enkel inloggning med Microsoft Edge finns i [blogginlägget Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) (Mobilt stöd för villkorlig åtkomst och enkel inloggning nu allmänt tillgängligt). Mer information om hur du ställer in klientappar [](../conditional-access/require-managed-devices.md)med [appbaserad](../conditional-access/app-based-conditional-access.md) villkorlig åtkomst eller enhetsbaserad villkorlig åtkomst finns i Hantera webbåtkomst med en [Microsoft Intune principskyddad webbläsare.](/intune/apps/app-configuration-managed-browser)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Hantering av Azure AD-rättigheter (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstkategori:** Andra  
**Produktfunktion:** Berättigandehantering

Azure AD-rättighetshantering är en ny identitetsstyrningsfunktion som hjälper organisationer att hantera identitets- och åtkomstlivscykeln i stor skala. Den här nya funktionen hjälper till genom att automatisera arbetsflöden för åtkomstbegäran, åtkomsttilldelningar, granskningar och förfallotid mellan grupper, appar och SharePoint Online-webbplatser.

Med Azure AD-berättigandehantering kan du mer effektivt hantera åtkomst både för anställda och för användare utanför organisationen som behöver åtkomst till dessa resurser.

Mer information finns i Vad [är berättigandehantering i Azure AD?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användarkontoetablering för dessa Nyligen stödda SaaS-appar

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part  

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md), [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – november 2019

**Typ:** Ny funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Integrering från tredje part

I november 2019 lade vi till dessa 21 nya appar med federationsstöd i appgalleriet:

[Airtable](../saas-apps/airtable-tutorial.md), [Spantsuite](../saas-apps/hootsuite-tutorial.md), [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Span](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nytt och förbättrat Azure AD-programgalleri

**Typ:** Ändrad funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso

Vi har uppdaterat Azure AD-programgalleriet så att det blir enklare för dig att hitta förintegrerade appar som stöder etablering, OpenID Connect och SAML på din Azure Active Directory klientorganisation.

Mer information finns i Lägga [till ett program i din Azure Active Directory klientorganisation.](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Höjd längd för approlldefinition från 120 till 240 tecken

**Typ:** Ändrad funktion  
**Tjänstkategori:** Företagsappar  
**Produktfunktion:** Sso

Vi har hört från kunder att längdgränsen för approlldefinitionsvärdet i vissa appar och tjänster är för kort med 120 tecken. Som svar har vi ökat den maximala längden för definitionen av rollvärde till 240 tecken.

Mer information om hur du använder programspecifika rolldefinitioner finns i [Lägga till approller i ditt program och ta emot dem i token](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Utfasning av identityRiskEvent-API:et för Azure AD Identity Protection riskidentifiering

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för Identitetsskydd:** Identity Security & Protection

Som svar på feedback från utvecklare kan Azure AD Premium P2-prenumeranter nu köra komplexa frågor på Azure AD Identity Protection:s riskidentifieringsdata med hjälp av det nya RISKDetection-API:et för Microsoft Graph. Den befintliga [betaversionen av identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API slutar returnera data **runt den 10 januari 2020.** Om din organisation använder API:et identityRiskEvent bör du övergå till det nya riskDetection-API:et.

Mer information om det nya riskDetection-API:et finns i [referensdokumentationen för API:et för riskidentifiering.](/graph/api/resources/riskdetection)

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Programproxy för SameSite-attributet och Chrome 80

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för appproxy:** Access Control

Några veckor före webbläsarutgågåndet av Chrome 80 planerar vi att uppdatera hur Programproxy cookies behandlar **SameSite-attributet.** I och med versionen av Chrome 80 behandlas alla cookies som inte anger **attributet SameSite** som om den hade angetts till `SameSite=Lax` .

För att undvika potentiellt negativ påverkan på grund av den här ändringen uppdaterar vi Programproxy åtkomst och sessionscookies genom att:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja.**

- Ange standardvärdet för **attributet SameSite** till **Ingen.**

    >[!NOTE]
    > Programproxy åtkomstcookies har alltid överförts exklusivt via säkra kanaler. Dessa ändringar gäller endast sessionscookies.

Mer information om Programproxy finns i [Cookieinställningar](../manage-apps/application-proxy-configure-cookie-settings.md)för åtkomst till lokala program i Azure Active Directory .

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Appregistreringar (äldre) och apphantering i programregistreringsportalen (apps.dev.microsoft.com) är inte längre tillgängliga

**Typ:** Plan för ändring **av tjänstkategori:** Produktfunktion: **Utvecklarupplevelse**

Användare med Azure AD-konton kan inte längre registrera eller hantera program med hjälp av programregistreringsportalen (apps.dev.microsoft.com) eller registrera och hantera program i Appregistreringar-upplevelsen (äldre) i Azure Portal.

Mer information om den nya Appregistreringar finns i [Appregistreringar i Azure Portal utbildningsguiden](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Användarna behöver inte längre omregistrera sig under migreringen från MFA per användare till MFA baserad på villkorsstyrd åtkomst

**Typ:** Fast **tjänstkategori:** **MFA-produktfunktion:** Identity Security & Protection

Vi har åtgärdat ett känt problem där när användare skulle omregistreras om de inaktiverades för multifaktorautentisering per användare (MFA) och sedan aktiverades för MFA via en princip för villkorsstyrd åtkomst.

Om du vill kräva att användarna registrerar sig på ett annat sätt kan du välja alternativet **Obligatoriskt omregistrera MFA** från användarens autentiseringsmetoder i Azure AD-portalen. Mer information om hur du migrerar användare från MFA per användare till MFA baserad på villkorsstyrd åtkomst finns i Konvertera användare från MFA per användare till villkorsstyrd [åtkomstbaserad MFA.](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nya funktioner för att transformera och skicka anspråk i din SAML-token

**Typ:** Ny **funktionstjänstkategori:** Produktkapacitet **för företagsappar:** enkel inloggning

Vi har lagt till ytterligare funktioner som hjälper dig att anpassa och skicka anspråk i din SAML-token. Dessa nya funktioner är:

- Ytterligare funktioner för anspråksomvandling som hjälper dig att ändra det värde som du skickar i anspråket.

- Möjlighet att tillämpa flera omvandlingar på ett enda anspråk.

- Möjlighet att ange anspråkskällan, baserat på användartyp och grupp som användaren tillhör.

Detaljerad information om dessa nya funktioner, inklusive hur du använder dem, finns i Anpassa anspråk som utfärdats i [SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Ny sida för mina inloggningar för slutanvändare i Azure AD

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för autentiseringar **(inloggningar): Övervakning** & rapportering

Vi har lagt till en ny sida för Mina inloggningar ( så att organisationens användare kan se sin senaste **inloggningshistorik** för att söka https://mysignins.microsoft.com) efter ovanliga aktiviteter. På den här nya sidan kan användarna se:

- Om någon försöker gissa sitt lösenord.

- Om en angripare har loggat in på sitt konto och från vilken plats.

- Vilka appar som angriparen försökte få åtkomst till.

Mer information finns i bloggen [Användare kan nu kontrollera sin inloggningshistorik för ovanlig](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) aktivitet.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrering av Azure AD Domain Services (Azure AD DS) från klassiska till Azure Resource Manager virtuella nätverk

**Typ:** Ny **funktionskategori:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

För våra kunder som har fastnat i klassiska virtuella nätverk – har vi fantastiska nyheter för dig! Nu kan du utföra en enda migrering från ett klassiskt virtuellt nätverk till ett befintligt Resource Manager virtuellt nätverk. När du har flyttat Resource Manager virtuella nätverket kan du dra nytta av de ytterligare och uppgraderade funktionerna, till exempel, avancerade lösenordsprinciper, e-postaviseringar och granskningsloggar.

Mer information finns i [Förhandsversion – Migrera Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Uppdateringar av Azure AD B2C sidkontraktlayout

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Vi har introducerat några nya ändringar i version 1.2.0 av sidkontraktet för Azure AD B2C. I den här uppdaterade versionen kan du nu styra inläsningsordningen för dina element, vilket också kan hjälpa till att stoppa reglaget som inträffar när formatmallen (CSS) läses in.

En fullständig lista över de ändringar som gjorts i sidkontraktet finns i [loggen För versionsändring.](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdatera till Mina appar tillsammans med nya arbetsytor (offentlig förhandsversion)

**Typ:** Ny **tjänstkategori: Mina appar** **produktfunktion:** Access Control

Nu kan du anpassa hur organisationens användare visar och får åtkomst till den helt nya Mina appar-upplevelsen, inklusive att använda den nya arbetsytefunktionen för att göra det enklare för dem att hitta appar. De nya arbetsytefunktionerna fungerar som ett filter för de appar som organisationens användare redan har åtkomst till.

Mer information om hur du distribuerar den nya Mina appar-upplevelsen och skapar arbetsytor finns i [Skapa arbetsytor på Mina appar -portalen (förhandsversion).](../manage-apps/access-panel-collections.md)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Stöd för den månatliga aktiva användarbaserade faktureringsmodellen (allmän tillgänglighet)

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Azure AD B2C har nu stöd för fakturering av månatliga aktiva användare (MAU). MAU-fakturering baseras på antalet unika användare med autentiseringsaktivitet under en kalendermånad. Befintliga kunder kan när som helst byta till den här nya faktureringsmetoden.

Från och med den 1 november 2019 debiteras alla nya kunder automatiskt med den här metoden. Den här faktureringsmetoden ger kunderna fördelar genom kostnadsfördelar och möjligheten att planera i förväg.

Mer information finns i Uppgradera [till faktureringsmodellen för månatliga aktiva användare.](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – oktober 2019

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I oktober 2019 lade vi till dessa 35 nya appar med federationsstöd i appgalleriet:

[I händelse av kris – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant) [,Capita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornel](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning,](https://bindtuning.com/login) [HireVue Coordinate – Europe](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/) [,MappyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Point](https://pi.ompnt.com/), Speaking Email [for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konsoliderat menyalternativ för säkerhet i Azure AD-portalen

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Produktfunktion för** Identity Protection: Identity Security & Protection

Nu kan du komma åt alla tillgängliga Azure  AD-säkerhetsfunktioner från  det nya menyalternativet Säkerhet och från sökfältet i Azure Portal. Dessutom innehåller den nya **landningssidan** Säkerhet **–** Komma igång länkar till vår offentliga dokumentation, säkerhetsvägledning och distributionsguider.

Den nya **menyn** Säkerhet innehåller:

- Villkorlig åtkomst
- Identity Protection
- Security Center
- Identitets-säkerhetspoäng
- Autentiseringsmetoder
- Multifaktorautentisering
- Riskrapporter – riskfyllda användare, riskfyllda inloggningar, riskidentifiering
- Och mycket mer...

Mer information finns i [Säkerhet – Komma igång.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Förfalloprincip för Office 365-grupper har förbättrats med autorenewal

**Typ:** Tjänstkategori **för ändrade funktioner:** Produktfunktion för **grupphantering: Hantering** av identitetslivscykel

Förfalloprincipen för Office 365-grupper har förbättrats för att automatiskt förnya grupper som aktivt används av dess medlemmar. Grupper kommer att automatiskt förnyas baserat på användaraktivitet i alla Office 365-appar, inklusive Outlook, SharePoint och Teams.

Den här förbättringen minskar antalet meddelanden om gruppförfallotid och ser till att aktiva grupper fortsätter att vara tillgängliga. Om du redan har en aktiv förfalloprincip för dina Office 365-grupper behöver du inte göra något för att aktivera den här nya funktionen.

Mer information finns i Konfigurera [förfalloprincipen för Office 365-grupper.](../enterprise-users/groups-lifecycle.md)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Uppdaterad Azure AD Domain Services (Azure AD DS) för skapande

**Typ:** **Tjänstkategorin Ändrades:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

Vi har uppdaterat Azure AD Domain Services (Azure AD DS) med en ny och förbättrad skapandeupplevelse, vilket hjälper dig att skapa en hanterad domän med bara tre klick! Dessutom kan du nu ladda upp och distribuera Azure AD DS från en mall.

Mer information finns i [Självstudie: Skapa och konfigurera en Azure Active Directory Domain Services instans](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Planera för förändring: Utfasning av Power BI innehållspaket

**Typ:** Planera för ändring **av tjänstkategori:** **Rapportera produktfunktion:** Övervakning & rapportering

Från och med den 1 oktober 2019 börjar Power BI att ta bort alla innehållspaket, inklusive Azure AD Power BI-innehållspaketet. Som ett alternativ till det här innehållspaketet kan du använda Azure AD-arbetsböcker för att få insikter om dina Azure AD-relaterade tjänster. Ytterligare arbetsböcker kommer, inklusive arbetsböcker om principer för villkorsstyrd åtkomst i endast rapportläge, appmedgivandebaserade insikter med mera.

Mer information om arbetsböckerna finns i [Så här använder du Azure Monitor arbetsböcker för Azure Active Directory rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Mer information om utfasningen av innehållspaketen finns i blogginlägget [Announcing Power BI template apps general availability (Tillkännagivande](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) om allmän tillgänglighet för mallappar).

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Min profil byter namn och integrerar med Microsoft Office-kontosidan

**Typ:** Planera för ändring **av tjänstkategori:** Funktionen Min profil/Kontoprodukt: Samarbete 

Från och med oktober blir upplevelsen Min profil Mitt konto. Som en del av den ändringen kommer Min profil att ändras **till Mitt** konto överallt där det står **.** Utöver namnändringen och vissa designförbättringar kommer den uppdaterade upplevelsen att erbjuda ytterligare integrering med Microsoft Office kontosidan. Mer specifikt kommer du att kunna komma åt  Office-installationer och prenumerationer från sidan Översiktskonto, tillsammans med Office-relaterade kontaktinställningar på **sidan** Sekretess.

Mer information om upplevelsen Min profil (förhandsversion) finns i [Översikt över portalen Min profil (förhandsversion).](../user-help/my-account-portal-overview.md)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Masshantera grupper och medlemmar med hjälp av CSV-filer i Azure AD-portalen (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** Produktfunktion för **grupphantering:** Samarbete

Vi är glada över att kunna presentera en allmänt tillgänglig förhandsversion av grupphanteringsupplevelserna i Azure AD-portalen. Du kan nu använda en CSV-fil och Azure AD-portalen för att hantera grupper och medlemslistor, inklusive:

- Lägga till eller ta bort medlemmar från en grupp.

- Ladda ned listan över grupper från katalogen.

- Ladda ned listan över gruppmedlemmar för en specifik grupp.

Mer information finns i [Massnedladdning av medlemmar,](../enterprise-users/groups-bulk-import-members.md) [Massborttagning](../enterprise-users/groups-bulk-remove-members.md)av medlemmar, [Lista över massnedladdningsmedlemmar](../enterprise-users/groups-bulk-download-members.md)och [Massnedladdning av grupper.](../enterprise-users/groups-bulk-download.md)

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiskt medgivande stöds nu via en ny slutpunkt för administratörsmedgivande

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för autentiseringar **(inloggningar):** Användarautentisering

Vi har skapat en ny slutpunkt för administratörsmedgivande för att stödja dynamiskt medgivande, vilket är användbart för appar som vill använda modellen för dynamiskt medgivande på Microsoft Identity-plattformen.

Mer information om hur du använder den här nya slutpunkten finns i [Använda slutpunkten för administratörsmedgivande.](../develop/v2-admin-consent.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – september 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I september 2019 lade vi till dessa 29 nya appar med federationsstöd i appgalleriet:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for Ethidex Compliance Office &trade; - Single sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md) `https://apps.yeeflow.com/` , ARC [Facilities](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENR SEAL](../saas-apps/renraku-tutorial.md), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), , , `https://app.penneo.com/` `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [Hosted Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md) [,LegHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), [Coo Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), Discovery [Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Ny global läsarroll för Azure AD

**Typ:** Ny **funktionskategori:** Azure **AD-roller Produktkapacitet:** Access Control

Från och med den 24 september 2019 kommer vi att börja distribuera en ny roll Azure Active Directory (AD) som heter Global läsare. Den här utrullningen börjar med produktions- och globala molnkunder (GCC), som avslutas globalt i oktober.

Rollen Global läsare är den skrivskyddade motsvarigheten till global administratör. Användare i den här rollen kan läsa inställningar och administrativ information Microsoft 365 tjänster, men kan inte vidta hanteringsåtgärder. Vi har skapat rollen Global läsare för att minska antalet globala administratörer i din organisation. Eftersom globala administratörskonton är kraftfulla och sårbara för angrepp rekommenderar vi att du har färre än fem globala administratörer. Vi rekommenderar att du använder rollen Global läsare för planering, granskningar eller utredningar. Vi rekommenderar också att du använder rollen Global läsare i kombination med andra begränsade administratörsroller, till exempel Exchange-administratör, för att få arbetet gjort utan att den globala administratörsrollen krävs.

Rollen Global läsare fungerar med det nya administrationscentret för Microsoft 365, Administrationscenter för Exchange, Administrationscenter för Teams, Security Center, Efterlevnadscenter, Administrationscenter för Azure AD och Enhetshantering Administrationscenter.

>[!NOTE]
> I början av den offentliga förhandsversionen fungerar inte rollen Global läsare med: SharePoint, Privileged Access Management, Customer Lockbox, känslighetsetiketter, Teams Lifecycle, Teams Reporting & Call Analytics, Teams IP Phone Enhetshantering och Teams App Catalog.

Mer information finns i Behörigheter [för administratörsroll i Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Få åtkomst till en lokal rapportserver från din Power BI Mobile med hjälp av Azure Active Directory Programproxy

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Access Control

Med den nya integreringen mellan Power BI-mobilappen och Azure AD Programproxy kan du på ett säkert sätt logga in i Power BI-mobilappen och visa någon av organisationens rapporter som finns på den lokala Power BI-rapportserver.

Information om appen Power BI Mobile, inklusive var du kan ladda ned appen, finns på [Power BI webbplats.](https://powerbi.microsoft.com/mobile/) Mer information om hur du ställer in Power BI-mobilappen med Azure AD Programproxy finns i Aktivera fjärråtkomst för [att Power BI Mobile med Azure AD Programproxy](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Ny version av PowerShell-modulen AzureADPreview är tillgänglig

**Typ:** **Tjänstkategorin Har ändrats:** **Annan produktfunktion:** Katalog

Nya cmdlets har lagts till i modulen AzureADPreview för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Ny version av Azure AD Connect

**Typ:** **Tjänstkategorin Har ändrats:** **Annan produktfunktion:** Katalog

Vi har släppt en uppdaterad version av Azure AD Connect för kunder som uppgraderar automatiskt. Den här nya versionen innehåller flera nya funktioner, förbättringar och felkorrigeringar. Mer information om den här nya versionen finns i [Azure AD Connect: Versionshistorik](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server, version 8.0.2 är nu tillgänglig

**Typ:** Fast **tjänstkategori:** **MFA-produktfunktion:** Identity Security & Protection

Om du är en befintlig kund som aktiverade MFA Server före den 1 juli 2019 kan du nu hämta den senaste versionen av MFA Server (version 8.0.2). I den här nya versionen gör vi följande:

- Ett problem har åtgärdats så att ett e-postmeddelande skickas till användaren när Azure AD-synkroniseringen ändrar en användare från Inaktiverad till Aktiverad.

- Åtgärdat ett problem så att kunder kan uppgradera samtidigt som de fortsätter att använda funktionen Taggar.

- Lade till landskoden Fördr (+383).

- Granskningsloggning för att kringgå engång har lagts till i MultiFactorAuthSvc.log.

- Förbättrad prestanda för webbtjänst-SDK.

- Åtgärdat andra mindre buggar.

Från och med 1 juli 2019 slutade Microsoft att erbjuda MFA Server för nya distributioner. Nya kunder som behöver multifaktorautentisering bör använda molnbaserad Azure AD Multi-Factor Authentication. Mer information finns i Planera [en molnbaserad Azure AD Multi-Factor Authentication-distribution.](../authentication/howto-mfa-getstarted.md)

---

## <a name="august-2019"></a>Augusti 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Förbättrad sökning, filtrering och sortering för grupper är tillgängliga i Azure AD-portalen (allmänt tillgänglig förhandsversion)

**Typ:** Ny **funktionskategori:** Produktfunktion för **grupphantering:** Samarbete

Vi är glada över att kunna presentera den allmänt tillgängliga förhandsversionen av de förbättrade grupprelaterade upplevelserna i Azure AD-portalen. Dessa förbättringar hjälper dig att bättre hantera grupper och medlemslistor genom att tillhandahålla:

- Avancerade sökfunktioner, till exempel delsträngssökning i grupplistor.
- Avancerade filtrerings- och sorteringsalternativ för medlems- och ägarlistor.
- Nya sökfunktioner för medlems- och ägarlistor.
- Mer exakta gruppantal för stora grupper.

Mer information finns i [Hantera grupper i Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nya anpassade roller är tillgängliga för appregistreringshantering (allmänt tillgänglig förhandsversion)

**Typ:** Ny **funktionskategori:** Azure **AD-roller Produktkapacitet:** Access Control

Anpassade roller (som är tillgängliga med en Azure AD P1- eller P2-prenumeration) kan nu ge dig mer information genom att du kan skapa rolldefinitioner med specifika behörigheter och sedan tilldela dessa roller till specifika resurser. För närvarande skapar du anpassade roller med hjälp av behörigheter för att hantera appregistreringar och sedan tilldela rollen till en specifik app. Mer information om anpassade roller finns i [Anpassade administratörsroller i Azure Active Directory (förhandsversion)](../roles/custom-overview.md).

Om du behöver ytterligare behörigheter eller resurser som stöds, vilket du inte ser för närvarande, kan du skicka feedback till vår [Azure-feedbackwebbplats](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) så lägger vi till din begäran i vår uppdaterade färdplan.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nya etableringsloggar kan hjälpa dig att övervaka och felsöka distributionen av appetablering (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktfunktion** för **appetablering: Hantering** av identitetslivscykel

Nya etableringsloggar är tillgängliga som hjälper dig att övervaka och felsöka distributionen av användare och gruppetablering. Dessa nya loggfiler innehåller information om:

- Vilka grupper har skapats i [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Vilka roller importerades från [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Vilka anställda som inte importerades från [Workday](../saas-apps/workday-inbound-tutorial.md)

Mer information finns i [Etableringsrapporter i Azure Active Directory portalen (förhandsversion).](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nya säkerhetsrapporter för alla Azure AD-administratörer (allmän tillgänglighet)

**Typ:** Ny **funktionskategori för tjänsten:** Produktkapacitet **för Identity Protection:** Identity Security & Protection

Som standard kommer alla Azure AD-administratörer snart att kunna komma åt moderna säkerhetsrapporter i Azure AD. Fram till slutet av september kommer du att kunna använda banderollen överst i de moderna säkerhetsrapporterna för att återgå till de gamla rapporterna.

De moderna säkerhetsrapporterna ger ytterligare funktioner från de äldre versionerna, inklusive:

- Avancerad filtrering och sortering
- Massåtgärder, till exempel att avvisa användarrisker
- Bekräftelse av komprometterade eller säkra entiteter
- Risktillstånd, omfattar: Risk, Avvisad, Åtgärdad och Bekräftad komprometterad
- Nya riskrelaterade identifieringar (tillgängliga för Azure AD Premium prenumeranter)

Mer information finns i [Riskfyllda användare,](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) [Riskfyllda inloggningar](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)och [Riskidentifiering.](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Användar tilldelad hanterad identitet är tillgänglig för Virtual Machines och Virtual Machine Scale Sets (allmän tillgänglighet)

**Typ:** Ny **funktionskategori: Hanterade** identiteter för Azure-resurser **Produktfunktion:** Utvecklarupplevelse

Användar tilldelade hanterade identiteter är nu allmänt tillgängliga för Virtual Machines och Virtual Machine Scale Sets. Som en del av detta kan Azure skapa en identitet i Azure AD-klientorganisationen som är betrodd av prenumerationen som används och som kan tilldelas till en eller flera Azure-tjänstinstanser. Mer information om användar tilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Användare kan återställa sina lösenord med hjälp av en mobilapp eller maskinvarutoken (allmän tillgänglighet)

**Typ:** **Funktionskategorin Tjänst har ändrats:** Produktfunktion för lösenordsåterställning **via självbetjäning:** Användarautentisering

Användare som har registrerat en mobilapp i din organisation kan nu återställa sina egna lösenord genom att godkänna ett meddelande från Microsoft Authenticator-appen eller genom att ange en kod från mobilappen eller maskinvarutoken.

Mer information finns i Så [här fungerar det: Lösenordsåterställning via självbetjäning i Azure AD.](../authentication/concept-sspr-howitworks.md) Mer information om användarupplevelsen finns i Återställa ditt eget lösenord för arbetet eller [skolan.](../user-help/active-directory-passwords-reset-register.md)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignorerar MSAL.NET cacheminnet för on-behalf-of-scenarier

**Typ:** Fast **tjänstkategori:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Från och med Azure AD-autentiseringsbiblioteket (ADAL.NET) version 5.0.0-preview måste apputvecklare serialisera en cache per konto för webbappar och [webb-API:er.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) I annat fall kan vissa scenarier som använder [on-behalf-of-flödet](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) för Java, tillsammans med vissa specifika användningsfall av , resultera `UserAssertion` i en rättighetsökning. För att undvika den här ADAL.NET du nu ignorerar Microsoft Authentication Library for dotnet (MSAL.NET) shared cache for on-behalf-of scenarios (Microsoft Authentication Library for MSAL.NET) shared cache for on-behalf-of scenarios (Microsoft Authentication Library for MSAL.NET) shared cache for on-behalf-of scenarios (Microsoft Authentication Library for MSAL.NET) shared cache for on-behalf-of scenarios (Microsoft Authentication Library for MSAL.NET) shared cache for on-behalf-of

Mer information om det här problemet finns i [Azure Active Directory authentication library elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – augusti 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I augusti 2019 lade vi till dessa 26 nya appar med federationsstöd i appgalleriet:

[Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Matrix](../saas-apps/academy-attendance-tutorial.md), Priority [Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargetrapp](../saas-apps/chargebee-tutorial.md), [deliver.media &trade; Portal](https://portal.deliver.media), [Frontline Education](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr](../saas-apps/sigstr-tutorial.md) [,Box](../saas-apps/darwinbox-tutorial.md), [Watch by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Nya versioner av PowerShell- och AzureADPreview PowerShell-modulerna är tillgängliga

**Typ:** **Tjänstkategorin Har ändrats:** **Annan produktfunktion:** Katalog

Nya uppdateringar av PowerShell-modulerna för förhandsversionen av AzureAD och AzureAD är tillgängliga:

- En ny `-Filter` parameter har lagts till i `Get-AzureADDirectoryRole` parametern i AzureAD-modulen. Den här parametern hjälper dig att filtrera på de katalogroller som returneras av cmdleten .
- Nya cmdlets har lagts till i modulen AzureADPreview för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Förbättringar av användargränssnittet för den dynamiska gruppregelbyggaren i Azure Portal

**Typ:** **Tjänstkategorin Har ändrats:** **Produktfunktion för grupphantering:** Samarbete

Vi har gjort några förbättringar i användargränssnittet för den dynamiska gruppregelbyggaren, som finns i Azure Portal, för att hjälpa dig att enklare konfigurera en ny regel eller ändra befintliga regler. Med den här designförbättringen kan du skapa regler med upp till fem uttryck, i stället för bara ett. Vi har också uppdaterat enhetsegenskapslistan för att ta bort inaktuella enhetsegenskaper.

Mer information finns i Hantera [regler för dynamiskt medlemskap.](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Ny Microsoft Graph appbehörighet som är tillgänglig för användning med åtkomstgranskningar

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Åtkomstgranskningar Produktfunktion:** Identitetsstyrning

Vi har introducerat en ny Microsoft Graph appbehörighet, , som gör att appar automatiskt kan skapa och hämta åtkomstgranskningar för `AccessReview.ReadWrite.Membership` gruppmedlemskap och apptilldelningar. Den här behörigheten kan användas av dina schemalagda jobb eller som en del av din automatisering, utan att det krävs en inloggad användarkontext.

Mer information finns i Exempel på hur du skapar Azure AD-åtkomstgranskningar med [hjälp Microsoft Graph med PowerShell-bloggen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga för myndighetsmolninstanser i Azure Monitor

**Typ:** **Funktionskategorin Har ändrats:** **Rapportera produktfunktion:** Övervakning & rapportering

Vi är glada över att kunna meddela att Azure AD-aktivitetsloggar nu är tillgängliga för myndighetsmolninstanser i Azure Monitor. Nu kan du skicka Azure AD-loggar till ditt lagringskonto eller till en händelsehubb för att integrera med SIEM-verktyg som [Sumologic,](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md) [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md) [och ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Mer information om hur du Azure Monitor finns i [Azure AD-aktivitetsloggar i Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Uppdatera användarna till den nya, förbättrade säkerhetsinformationsupplevelsen

**Typ:** **Funktionskategorin Tjänst har ändrats:**  Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Den 25 september 2019 kommer vi att stänga av den gamla, icke-förbättrade säkerhetsinformationsupplevelsen för registrering och hantering av användarsäkerhetsinformation och bara aktivera den nya, förbättrade [versionen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Det innebär att användarna inte längre kommer att kunna använda den gamla upplevelsen.

Mer information om den förbättrade säkerhetsinformationsupplevelsen finns i vår [administratörsdokumentation](../authentication/concept-registration-mfa-sspr-combined.md) och i [vår användardokumentation.](../user-help/security-info-setup-signin.md)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Om du vill aktivera den här nya upplevelsen måste du:

1. Logga in på Azure Portal som global administratör eller användaradministratör.

2. Gå till **Azure Active Directory > användarinställningar > Hantera inställningar för förhandsgranskningsfunktioner i åtkomstpanelen.**

3. I Området **Användare** kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhetsinformation – utökat område väljer du **Valda** och väljer sedan antingen en grupp med användare eller Väljer **alla** för att aktivera den här funktionen för alla användare i klientorganisationen.

4. I området **Användare kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhet **info*** väljer du **Ingen.**

5. Spara inställningarna.

    När du har sparat inställningarna har du inte längre åtkomst till den gamla säkerhetsinformationsupplevelsen.

>[!Important]
>Om du inte slutför de här stegen före den 25 september 2019 kommer din Azure Active Directory att aktiveras automatiskt för den förbättrade upplevelsen. Om du har frågor kan du kontakta oss på registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Autentiseringsbegäranden som använder POST-inloggningar verifieras striktare

**Typ:** Tjänstkategori **för ändrade funktioner:** Autentiseringar (inloggningar) **Produktfunktion:** Standarder

Från och med den 2 september 2019 kommer autentiseringsbegäranden som använder POST-metoden att verifieras striktare mot HTTP-standarderna. Mer specifikt tas blanksteg och dubbla citattecken (") inte längre bort från begärandeformulärvärden. De här ändringarna förväntas inte bryta några befintliga klienter och hjälper till att säkerställa att begäranden som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång.

Mer information finns i meddelanden om [större ändringar i Azure AD.](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för förändring: Programproxy tjänstuppdatering för att endast stödja TLS 1.2

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för appproxy:** Access Control

För att hjälpa dig med vår starkaste kryptering kommer vi att börja begränsa Programproxy till endast TLS 1.2-protokoll. Den här begränsningen kommer först att distribueras till kunder som redan använder TLS 1.2-protokoll, så du kommer inte att se effekten. Fullständig utfasning av TLS 1.0- och TLS 1.1-protokollen slutförs den 31 augusti 2019. Kunder som fortfarande använder TLS 1.0 och TLS 1.1 får ett avancerat meddelande för att förbereda för den här ändringen.

För att upprätthålla anslutningen till Programproxy-tjänsten under den här ändringen rekommenderar vi att du ser till att kombinationerna klient-server och webbläsare-server uppdateras så att TLS 1.2 används. Vi rekommenderar också att du inkluderar alla klientsystem som används av dina anställda för att få åtkomst till appar som publicerats via Programproxy tjänsten.

Mer information finns i [Lägga till ett lokalt program för fjärråtkomst via Programproxy i Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planera för förändring: Designuppdateringar kommer för programgalleriet

**Typ:** Planera för ändring **av tjänstkategori:** Produktkapacitet **för Enterprise-appar:** enkel inloggning

Nya ändringar i användargränssnittet kommer till utformningen av lägg **till från galleriområdet** på **bladet Lägg till ett** program. De här ändringarna hjälper dig att enklare hitta appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och enkel inloggning med lösenord (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planera för ändring: Borttagning av MFA-serverns IP-adress från Office 365-IP-adressen

**Typ:** Planera för ändring **av tjänstkategori:** MFA-produktfunktion: Identity Security & Protection 

Vi tar bort MFA-serverns IP-adress från [Office 365-IP-adressen och URL-webbtjänsten](/office365/enterprise/office-365-ip-web-service). Om du för närvarande förlitar dig på dessa sidor för att uppdatera brandväggsinställningarna måste du även se till att du inkluderar listan över IP-adresser som dokumenteras i avsnittet om brandväggskrav för **Azure Multi-Factor Authentication-server** i artikeln Komma igång med [Azure Multi-Factor Authentication-server.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Appbaserade token kräver nu att klientappen finns i resursklientorganisationen

**Typ:** Fast **tjänstkategori:** Produktfunktion för autentiseringar **(inloggningar):** Användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller appbaserade token via de klientautentiseringsuppgifter [som beviljar](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Tidigare kunde appar hämta token för att anropa andra appar, oavsett om klientappen fanns i klientorganisationen eller inte. Vi har uppdaterat det här beteendet så att resurser för en enskild klient, som ibland kallas webb-API:er, endast kan anropas av klientappar som finns i resursklientorganisationen.

Om din app inte finns i resursklientorganisationen får du ett felmeddelande som säger Att åtgärda det här problemet måste du skapa klientapptjänstens huvudnamn i klientorganisationen med hjälp av antingen slutpunkten för administratörsmedgivande eller `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` [via PowerShell](../develop/howto-authenticate-service-principal-powershell.md), vilket säkerställer att din klientorganisation har gett appen behörighet att arbeta i klientorganisationen. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

Mer information finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Befintligt medgivande mellan klienten och API:et krävs fortfarande inte. Appar bör fortfarande utföra sina egna auktoriseringskontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Ny lösenordsfri inloggning i Azure AD med FIDO2-säkerhetsnycklar

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för autentiseringar **(inloggningar):** Användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2-säkerhetsnycklar för organisationens användare och grupper. Slutanvändarna kan också själv registrera sina säkerhetsnycklar, använda nycklarna för att logga in på sina Microsoft-konton på webbplatser på FIDO-kompatibla enheter, samt logga in på sina Azure AD-anslutna Windows 10 enheter.

Mer information finns i Aktivera lösenordslös inloggning för [Azure AD (förhandsversion)](../authentication/concept-authentication-passwordless.md) för administratörsrelaterad information och Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel [(förhandsversion)](../user-help/security-info-setup-security-key.md) för slutanvändarrelaterad information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar tillgängliga i Azure AD App galleriet – juli 2019

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I juli 2019 lade vi till dessa 18 nya appar med federationsstöd i appgalleriet:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), [Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), Clever [Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance &trade; Office](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md) [,Uda](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineEla](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [UdaID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFtage Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användarkontoetablering för dessa Nyligen stödda SaaS-appar

**Typ:** Ny **funktionskategori:** Produktfunktion för **Företagsappar: Övervakning** & rapportering

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federerad katalog](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk etablering av användarkonton finns i Automatisera användareablering till [SaaS-program med Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ny Azure AD Domain Services för nätverkssäkerhetsgrupp

**Typ:** Ny **funktionskategori:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

Om du är less på att hantera långa listor med IP-adresser och intervall kan du använda nätverkstjänsttaggen **AzureActiveDirectoryDomainServices** i din Azure-nätverkssäkerhetsgrupp för att skydda inkommande trafik till ditt Azure AD Domain Services virtuella nätverksundernät.

Mer information om den här nya tjänsttaggen finns i [Nätverkssäkerhetsgrupper för Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhetsgranskningar för Azure AD Domain Services (offentlig förhandsversion)

**Typ:** Ny **tjänstkategori:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

Vi är glada över att kunna meddela att granskning av Azure AD Domain Service-säkerhet har släppts som offentlig förhandsversion. Säkerhetsgranskning hjälper dig att få viktig insikt i dina autentiseringstjänster genom att strömma säkerhetsgranskningshändelser till målresurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub, med hjälp av Azure AD Domain Service-portalen.

Mer information finns i [Aktivera säkerhetsgranskningar för Azure AD Domain Services (förhandsversion)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nya autentiseringsmetoder för användning & insikter (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktfunktion för** lösenordsåterställning via **självbetjäning:** Övervakning & rapportering

De nya rapporterna om autentiseringsmetoder för användning av & Insights kan hjälpa dig att förstå hur funktioner som Azure AD Multi-Factor Authentication och lösenordsåterställning via självbetjäning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta lösenordsåterställning via självbetjäning används för att återställa lösenord och med vilken metod återställningen sker.

Mer information finns i Användning [av autentiseringsmetoder & insights (förhandsversion)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhetsrapporter är tillgängliga för alla Azure AD-administratörer (allmänt tillgänglig förhandsversion)

**Typ:** Ny **funktionskategori för tjänsten:** Produktkapacitet **för Identity Protection:** Identity Security & Protection

Alla Azure **AD-administratörer** kan nu välja banderollen överst i befintliga säkerhetsrapporter, till exempel rapporten Användare som  flaggats för **risk,** för att börja använda den nya säkerhetsupplevelsen som visas i rapporterna riskfyllda användare och riskfyllda inloggningar. Med tiden flyttas alla säkerhetsrapporter från de äldre versionerna till de nya versionerna. De nya rapporterna ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Massåtgärder, till exempel att avvisa användarrisk

- Bekräftelse av komprometterade eller säkra entiteter

- Risktillstånd, omfattar: Risk, Avvisad, Åtgärdad och Bekräftad komprometterad

Mer information finns i [rapporten om riskfyllda användare](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) och rapporten för [riskfyllda inloggningar.](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhetsgranskningar för Azure AD Domain Services (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

Vi är glada över att kunna meddela att granskning av Azure AD Domain Service-säkerhet har släppts som offentlig förhandsversion. Säkerhetsgranskning hjälper dig att få viktig insikt i dina autentiseringstjänster genom att strömma säkerhetsgranskningshändelser till målresurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub, med hjälp av Azure AD Domain Service-portalen.

Mer information finns i [Aktivera säkerhetsgranskningar för Azure AD Domain Services (förhandsversion).](../../active-directory-domain-services/security-audit-events.md)

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B-direktfederation med SAML/WS-Fed (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C

Direkt federation gör det enklare för dig att arbeta med partner vars IT-hanterade identitetslösning inte är Azure AD, genom att arbeta med identitetssystem som stöder SAML- eller WS-Fed-standarder. När du har ställt in en direkt federationsrelation med en partner kan alla nya gästanvändare som du bjuder in från den domänen samarbeta med dig med hjälp av deras befintliga organisationskonto, vilket gör användarupplevelsen för dina gäster smidigare.

Mer information finns i [Direkt federation med AD FS och tredjepartsleverantörer för gästanvändare (förhandsversion)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användarkontoetablering för dessa Nyligen stödda SaaS-appar

**Typ:** Ny **funktionskategori: Produktfunktion** för **företagsappar: Övervakning** & rapportering

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federerad katalog](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ny kontroll av duplicerade gruppnamn i Azure AD-portalen

**Typ:** Ny **funktionskategori: Produktfunktion** för **grupphantering:** Samarbete

När du nu skapar eller uppdaterar ett gruppnamn från Azure AD-portalen ska vi utföra en kontroll för att se om du duplicerar ett befintligt gruppnamn i resursen. Om vi fastställer att namnet redan används av en annan grupp uppmanas du att ändra ditt namn.

Mer information finns i [Hantera grupper i Azure AD-portalen.](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svars-URI:er (omdirigering)

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för autentiseringar **(inloggningar):** Användarautentisering

Azure AD-appar kan nu registrera och använda svars-URI:er (omdirigering) med statiska frågeparametrar (till exempel ) för `https://contoso.com/oauth2?idp=microsoft` OAuth 2.0-begäranden. Den statiska frågeparametern omfattas av strängmatchning för svars-URI:er, precis som andra delar av svars-URI:et. Om det inte finns någon registrerad sträng som matchar den URL-avkodade omdirigerings-URI:n avvisas begäran. Om svars-URI:en hittas används hela strängen för att omdirigera användaren, inklusive den statiska frågeparametern.

Dynamiska svars-URI:er är fortfarande förbjudna eftersom de utgör en säkerhetsrisk och inte kan användas för att behålla tillståndsinformation i en autentiseringsbegäran. För detta ändamål använder du `state` parametern .

För närvarande blockerar appregistreringsskärmarna för Azure Portal fortfarande frågeparametrar. Du kan dock manuellt redigera appmanifestet för att lägga till och testa frågeparametrar i din app. Mer information finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitetsloggar (MS Graph API:er) för Azure AD är nu tillgängliga via PowerShell-cmdlets

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Vi är glada över att kunna meddela att Azure AD-aktivitetsloggar (gransknings- och inloggningsrapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API slutpunkter, och nu har vi utökat den funktionen till PowerShell-cmdlets.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdlets för rapportering.](../reports-monitoring/reference-powershell-reporting.md)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filterkontroller för gransknings- och inloggningsloggar i Azure AD

**Typ:** **Funktionskategorin Har ändrats:** **Rapportera produktfunktion:** Övervakning & rapportering

Vi har uppdaterat gransknings- och inloggningsloggrapporterna så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner på rapportskärmarna. Dessutom kan du nu bestämma hur många filter du vill visa på skärmen. Alla dessa uppdateringar fungerar tillsammans för att göra dina rapporter enklare att läsa och mer begränsade till dina behov.

Mer information om de här uppdateringarna finns i [Filtrera granskningsloggar](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) [och Filtrera inloggningsaktiviteter.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nytt riskDetections-API för Microsoft Graph (offentlig förhandsversion)

**Typ:** Ny **funktionskategori för tjänst:** Produktfunktion **för Identitetsskydd:** Identity Security & Protection

Vi är glada över att kunna presentera det nya riskDetections-API:et för Microsoft Graph nu är i offentlig förhandsversion. Du kan använda det här nya API:et för att visa en lista över organisationens Identity Protection-relaterade användar- och inloggningsriskidentifieringar. Du kan också använda det här API:et för att mer effektivt köra frågor mot riskidentifieringarna, inklusive information om identifieringstyp, status, nivå med mera.

Mer information finns i [referensdokumentationen för API:et för riskidentifiering.](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – juni 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I juni 2019 lade vi till dessa 22 nya appar med federationsstöd i appgalleriet:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsnod Fonel (sidan塚商 toolkit)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN](https://portal.azure.com/)Client , [ExpenseIn](../saas-apps/expensein-tutorial.md), [Helper Helper](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Segment-Oracle In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager för Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS för JD Edwards

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användarkontoetablering för dessa Nyligen stödda SaaS-appar

**Typ:** Ny **funktionskategori: Produktfunktion** för **företagsappar: Övervakning** & rapportering

Nu kan du automatisera skapandet, uppdateringen och borttagningen av användarkonton för dessa nyligen integrerade appar:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatiserad användarkontoetablering finns i [Automatisera användareablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa förloppet i realtid för Azure AD-etableringstjänsten

**Typ:** **Funktionskategorin Tjänst har ändrats:** Produktfunktion **för appetablering: Hantering** av identitetslivscykel

Vi har uppdaterat Azure AD-etableringsupplevelsen så att den innehåller en ny förloppsstapel som visar hur långt du befinner dig i användaretableringsprocessen. Den här uppdaterade upplevelsen innehåller också information om antalet användare som har etablerats under den aktuella cykeln, samt hur många användare som har etablerats hittills.

Mer information finns i [Kontrollera status för användareablering.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företags varumärke visas nu på ut- och felskärmarna

**Typ:** **Funktionskategorin Tjänst har ändrats:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Vi har uppdaterat Azure AD så att din företags varumärke nu visas på ut- och felskärmarna samt på inloggningssidan. Du behöver inte göra något för att aktivera den här funktionen. Azure AD använder  bara de tillgångar som du redan har ställt in i företags varumärkesområdet för Azure Portal.

Mer information om hur du ställer in varumärkesmärket finns i Lägga till varumärkesyttning på [organisationens Azure Active Directory sidor.](./customize-branding.md)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication -servern (MFA) är inte längre tillgänglig för nya distributioner

**Typ:** Inaktuell **tjänstkategori:** **MFA-produktfunktion:** Identity Security & Protection

Från och med 1 juli 2019 erbjuder Microsoft inte längre MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering i organisationen måste nu använda molnbaserad Azure AD Multi-Factor Authentication. Kunder som aktiverade MFA Server före den 1 juli ser ingen ändring. Du kommer fortfarande att kunna ladda ned den senaste versionen, hämta framtida uppdateringar och generera autentiseringsuppgifter för aktivering.

Mer information finns i [Komma igång med Azure Multi-Factor Authentication-server](../authentication/howto-mfaserver-deploy.md). Mer information om molnbaserad Azure AD Multi-Factor Authentication finns i Planera en molnbaserad Distribution av [Azure AD Multi-Factor Authentication.](../authentication/howto-mfa-getstarted.md)

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänständring: Framtida stöd endast för TLS 1.2-protokoll på Programproxy tjänsten

**Typ:** Planera för ändring **av tjänstkategori:** **Produktfunktion för appproxy:** Access Control

För att ge bästa möjliga kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1.2-protokoll på Programproxy tjänsten. Den här ändringen distribueras gradvis till kunder som redan använder TLS 1.2-protokoll, så du bör inte se några ändringar.

Utfasningen av TLS 1.0 och TLS 1.1 sker den 31 augusti 2019, men vi ger ytterligare avancerad information, så du har tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen ser du till att kombinationerna klient-server och webbläsarserver, inklusive alla klienter som användarna använder för att komma åt appar som publicerats via Programproxy, har uppdaterats för att använda TLS 1.2-protokollet för att upprätthålla anslutningen till Programproxy-tjänsten. Mer information finns i [Lägga till ett lokalt program för fjärråtkomst via Programproxy i Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd användnings- och insiktersrapporten för att visa dina apprelaterade inloggningsdata

**Typ:** Ny **funktionskategori: Produktfunktion** för **företagsappar: Övervakning** & rapportering

Nu kan du använda användnings- och  insiktersrapporten i området Företagsprogram i Azure Portal för att få en programcentrerad vy över dina inloggningsdata, inklusive information om:

- Appar som används mest för din organisation

- Appar med flest misslyckade inloggningar

- De främsta inloggningsfelen för varje app

Mer information om den här funktionen finns i [Användnings- och insiktersrapporten i Azure Active Directory portalen](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera användareablering till molnappar med Hjälp av Azure AD

**Typ:** Ny **funktionskategori: Produktfunktion** för **företagsappar: Övervakning** & rapportering

Följ de här nya självstudierna om du vill använda Azure AD Provisioning Service för att automatisera skapande, borttagning och uppdatering av användarkonton för följande molnbaserade appar:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Du kan också följa den här nya [Dropbox-självstudien,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)som innehåller information om hur du etablerar gruppobjekt.

Mer information om hur du skyddar din organisation bättre genom automatisk etablering av användarkonton finns i Automatisera användareablering till [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitets-säkerhetspoäng är nu tillgängligt i Azure AD (allmän tillgänglighet)

**Typ:** Ny **funktionskategori:** Funktionen Ej **produkt:** Identity Security & Protection

Nu kan du övervaka och förbättra din identitetssäkerhetsstatus med hjälp av funktionen för identitetssäkerhetspoäng i Azure AD. Funktionen för identitets säkra poäng använder en enda instrumentpanel för att hjälpa dig:

- Mät din identitetssäkerhetsstatus objektivt baserat på en poäng mellan 1 och 223.

- Planera för dina identitetssäkerhetsförbättringar

- Granska framgången för dina säkerhetsförbättringar

Mer information om funktionen för identitetssäkerhetspoäng finns [i Vad är identitetssäkerhetspoängen i Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Den Appregistreringar upplevelsen är nu tillgänglig (allmän tillgänglighet)

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Utvecklarupplevelse

Den nya [Appregistreringar](https://aka.ms/appregistrations) är nu allmänt tillgänglig. Den här nya upplevelsen innehåller alla viktiga funktioner som du är bekant med från Azure Portal och portalen för programregistrering och förbättrar dem genom:

- **Bättre apphantering.** I stället för att se dina appar i olika portaler kan du nu se alla dina appar på en enda plats.

- **Förenklad appregistrering.** Från den förbättrade navigeringsupplevelsen till den förbättrade behörighetsvalsupplevelsen är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabbstartsguider med mera.

Mer information finns i [Microsoft Identity Platform och](../develop/index.yml) Appregistreringar nu är allmänt [tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) bloggmeddelande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API:et för riskfyllda användare för Identity Protection

**Typ:** Ny **funktionskategori för tjänsten:** Produktkapacitet för **Identity Protection:** Identity Security & Protection

Vi är glada över att kunna meddela att du nu kan använda API:et för riskfyllda användare för att hämta användarnas riskhistorik, stänga riskfyllda användare och bekräfta att användarna har komprometterats. Den här ändringen hjälper dig att mer effektivt uppdatera riskstatusen för dina användare och förstå deras riskhistorik.

Mer information finns i [referensdokumentationen för API för riskfyllda användare.](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – maj 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I maj 2019 lade vi till dessa 21 nya appar med federationsstöd i appgalleriet:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [Real Links](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Displayr](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), Quantum [Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade funktioner för att skapa och hantera grupper i Azure AD-portalen

**Typ:** Ny **funktionskategori för tjänst:** Produktfunktion **för grupphantering:** Samarbete

Vi har gjort förbättringar av de grupprelaterade upplevelserna i Azure AD-portalen. De här förbättringarna gör det möjligt för administratörer att bättre hantera grupplistor, medlemslistor och tillhandahålla ytterligare alternativ för skapande.

Här är några av förbättringarna:

- Grundläggande filtrering efter medlemskapstyp och grupptyp.

- Tillägg av nya kolumner, till exempel Källa och E-postadress.

- Möjlighet att välja flera grupper, medlemmar och ägarlistor för enkel borttagning.

- Möjlighet att välja en e-postadress och lägga till ägare när gruppen skapas.

Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namngivningsprincip för Office 365-grupper i Azure AD-portalen (allmän tillgänglighet)

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Produktfunktion för grupphantering:** Samarbete

Administratörer kan nu konfigurera en namngivningsprincip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivningsprincip för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, som inte tillåts i gruppnamn (till exempel "VD, lönechef, PERSONAL").

Mer information finns i [Framtvinga en namngivningsprincip för Office 365-grupper.](../enterprise-users/groups-naming-policy.md)

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitetsloggar (allmän tillgänglighet)

**Typ:** **Funktionskategorin Har ändrats:** **Rapportera produktfunktion:** Övervakning & rapportering

Vi är glada över att kunna meddela allmän tillgänglighet för Microsoft Graph API-slutpunkter för Azure AD-aktivitetsloggar. Med den här versionen kan du nu använda version 1.0 av både Azure AD-granskningsloggarna och API:erna för inloggningsloggar.

Mer information finns i Översikt [över API:et för Azure AD-granskningsloggen.](/graph/api/resources/azure-ad-auditlog-overview)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registreringsprocessen (offentlig förhandsversion)

**Typ:** Ny **tjänstkategori: Produktfunktion** för **villkorsstyrd åtkomst:** Identity Security & Protection

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registreringssidan. Detta inkluderar att tillämpa principer för att tillåta registrering om:

- Användare finns i ett betrott nätverk.

- Användare är en låg inloggningsrisk.

- Användare finns på en hanterad enhet.

- Användarna godkänner organisationens användningsvillkor (TOU).

Mer information om villkorlig åtkomst och lösenordsåterställning finns i blogginlägget Conditional Access for the Azure AD combined MFA and password reset registration experience (Villkorlig åtkomst för Azure AD kombinerad registrering med MFA och [lösenordsåterställning).](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Mer information om principer för villkorlig åtkomst för den kombinerade registreringsprocessen finns i [Principer för villkorlig åtkomst för kombinerad registrering.](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration) Mer information om funktionen för användningsvillkor för Azure AD finns [i Azure Active Directory för användningsvillkor.](../conditional-access/terms-of-use.md)

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Ny Azure AD-hotinformation identifiering är nu tillgänglig som en del av Azure AD Identity Protection

**Typ:** Ny **tjänstkategori: Azure AD Identity Protection** **produktfunktion:** Identity Security & Protection

Azure AD-hotinformation identifiering är nu tillgänglig som en del av den uppdaterade Azure AD Identity Protection funktionen. Den här nya funktionen hjälper till att indikera ovanlig användaraktivitet för en viss användare eller aktivitet som är konsekvent med kända angreppsmönster baserat på Microsofts interna och externa hotinformationskällor.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i Bloggen Fyra större [Azure AD Identity Protection-förbättringar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) finns nu i den offentliga förhandsversionen och [Vad är Azure Active Directory Identity Protection (uppdateras)?](../identity-protection/overview-identity-protection.md) . Mer information om Azure AD-hotinformation finns i artikeln [Azure Active Directory Identity Protection om riskidentifiering.](../identity-protection/concept-identity-protection-risks.md)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD-berättigandehantering är nu tillgängligt (offentlig förhandsversion)

**Typ:** Ny **funktionskategori för tjänst:** Produktfunktion för **identitetsstyrning:** Identitetsstyrning

Azure AD-rättighetshantering, som nu är i offentlig förhandsversion, hjälper kunder att delegera hanteringen av åtkomstpaket, som definierar hur anställda och affärspartner kan begära åtkomst, vem som måste godkänna och hur länge de har åtkomst. Åtkomstpaket kan hantera medlemskap i Azure AD- och Office 365-grupper, rolltilldelningar i företagsprogram och rolltilldelningar för SharePoint Online-webbplatser. Läs mer om berättigandehantering i [översikten över Azure AD-berättigandehantering.](../governance/entitlement-management-overview.md) Mer information om funktionerna i Azure AD Identity Governance, inklusive Privileged Identity Management, åtkomstgranskningar och användningsvillkor finns i [Vad är Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namngivningsprincip för Office 365-grupper i Azure AD-portalen (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** Produktfunktion för **grupphantering:** Samarbete

Administratörer kan nu konfigurera en namngivningsprincip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivningsprincip för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, som inte tillåts i gruppnamn (till exempel "VD, lönechef, PERSONAL").

Mer information finns i [Framtvinga en namngivningsprincip för Office 365-grupper.](../enterprise-users/groups-naming-policy.md)

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitetsloggar är nu tillgängliga i Azure Monitor (allmän tillgänglighet)

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

För att hjälpa dig att hantera din feedback om visualiseringar med Azure AD-aktivitetsloggar introducerar vi en ny insights-funktion i Log Analytics. Den här funktionen hjälper dig att få insikter om dina Azure AD-resurser med hjälp av våra interaktiva mallar, som kallas Arbetsböcker. Dessa färdiga arbetsböcker kan ge information för appar eller användare, och inkluderar:

- **Inloggningar.** Innehåller information för appar och användare, inklusive inloggningsplats, det operativsystem eller den webbläsarklient och version som används samt antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och villkorlig åtkomst.** Innehåller information för appar och användare som använder äldre autentisering, inklusive multifaktorautentiseringsanvändning som utlöses av principer för villkorsstyrd åtkomst, appar som använder principer för villkorsstyrd åtkomst och så vidare.

- **Analys av inloggningsfel.** Hjälper dig att avgöra om inloggningsfel inträffar på grund av en användaråtgärd, principproblem eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbetsböcker för att anpassa insights-funktionen för din organisation.

Mer information finns i Så [här använder du Azure Monitor arbetsböcker för Azure Active Directory rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – april 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I april 2019 lade vi till dessa 21 nya appar med federationsstöd i appgalleriet:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [Mcontrol](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [Shibumi](../saas-apps/shibumi-tutorial.md) [,Avsett](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS,](../saas-apps/edubrite-lms-tutorial.md) [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibiq Cloud (rollbaserad enkel inloggning)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent-hantering,](../saas-apps/certent-equity-management-tutorial.md) [Sectigo Certificate Manager,](../saas-apps/sectigo-certificate-manager-tutorial.md) [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nytt alternativ för frekvens för åtkomstgranskningar och val av flera roller

**Typ:** Ny **funktionskategori: Åtkomstgranskningar** **Produktfunktion:** Identitetsstyrning

Med nya uppdateringar i Azure AD-åtkomstgranskningar kan du:

- Ändra frekvensen för dina åtkomstgranskningar till **halvårsvis**, förutom de tidigare befintliga alternativen för varje vecka, varje månad, varje kvartal och varje år.

- Välj flera Azure AD- och Azure-resursroller när du skapar en enda åtkomstgranskning. I så fall konfigureras alla roller med samma inställningar och alla granskare meddelas samtidigt.

Mer information om hur du skapar en åtkomstgranskning finns i Skapa en åtkomstgranskning av grupper eller [program i Azure AD-åtkomstgranskningar.](../governance/create-access-review.md)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-postaviseringssystem håller på att övergå och skickar ny e-postavsändarinformation till vissa kunder

**Typ:** **Funktionskategorin Tjänst har ändrats:** AD Sync **produktfunktion:** Plattform

Azure AD Connect är under övergång av våra e-postaviseringssystem, vilket potentiellt kan visa en del kunder en ny e-postavsändare. För att åtgärda detta måste du lägga till i organisationens lista över tillåtna, annars kan du inte fortsätta att ta emot viktiga aviseringar från `azure-noreply@microsoft.com` dina Office 365-, Azure- eller Sync-tjänster.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN-suffixändringar lyckas nu mellan federerade domäner i Azure AD Connect

**Typ:** Fast **tjänstkategori:** AD Sync **produktfunktion:** plattform

Du kan nu ändra en användares UPN-suffix från en federerad domän till en annan federerad domän i Azure AD Connect. Den här korrigeringen innebär att du inte längre får felmeddelandet FederatedDomainChangeError under synkroniseringscykeln eller får ett e-postmeddelande med meddelandet "Det går inte att uppdatera det här objektet i Azure Active Directory, eftersom attributet [FederatedUser.UserPrincipalName] inte är giltigt. Uppdatera värdet i dina lokala katalogtjänster".

Mer information finns i [Felsöka fel under synkroniseringen.](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av den appskyddsbaserade principen för villkorsstyrd åtkomst i Azure AD (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktfunktion** för **villkorsstyrd åtkomst:** Identity Security & Protection

Appskydd villkorlig åtkomst är nu tillgänglig med hjälp av **Kräv appskyddsprincip.** Den här nya principen hjälper till att öka organisationens säkerhet genom att förhindra:

- Användare får åtkomst till appar utan en Microsoft Intune licens.

- Användare som inte kan hämta Microsoft Intune en appskyddsprincip.

- Användare får åtkomst till appar utan någon konfigurerad Microsoft Intune appskyddsprincip.

Mer information finns i Så [här kräver du appskyddsprincip för åtkomst till molnapp med villkorlig åtkomst.](../conditional-access/app-protection-based-conditional-access.md)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nytt stöd för enkel inloggning med Azure AD och villkorlig åtkomst i Microsoft Edge (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktfunktion** för **villkorsstyrd åtkomst:** Identity Security & Protection

Vi har förbättrat vårt Azure AD-stöd för Microsoft Edge, inklusive att tillhandahålla nytt stöd för enkel inloggning med Azure AD och villkorsstyrd åtkomst. Om du tidigare har använt Microsoft Intune Managed Browser kan du nu använda Microsoft Edge stället.

Mer information om hur du ställer in och [](../conditional-access/require-managed-devices.md) hanterar dina enheter och appar med villkorlig åtkomst finns i Kräv hanterade enheter för åtkomst till molnappar med villkorlig åtkomst och Kräv godkända klientappar för åtkomst till molnappar med [villkorlig åtkomst.](../conditional-access/app-based-conditional-access.md) Mer information om hur du hanterar åtkomst med Microsoft Edge med Microsoft Intune-principer finns i Hantera Internetåtkomst med en [Microsoft Intune en principskyddad webbläsare.](/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework och anpassat principstöd i Azure Active Directory B2C är nu tillgängligt (GA)

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Nu kan du skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i stor skala och under vårt Serviceavtal för Azure:

- Skapa och ladda upp anpassade användarresor för autentisering med hjälp av anpassade principer.

- Beskriv användarresor steg för steg som utbyten mellan anspråksproviders.

- Definiera villkorsstyrd förgrening i användarresor.

- Omvandla och mappa anspråk för användning i realtidsbeslut och kommunikation.

- Använd REST API-aktiverade tjänster i dina anpassade användarresor för autentisering. Till exempel med e-postleverantörer, CCR:er och upphovsrättsskyddade auktoriseringssystem.

- Federera med identitetsproviders som är kompatibla med OpenIDConnect-protokollet. Till exempel med Azure AD för flera innehavare, leverantörer av sociala konton eller tvåfaktorsverifieringsproviders.

Mer information om hur du skapar anpassade principer finns i Utvecklaranteckningar för anpassade [principer i Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) och i Alex [Simons blogginlägg, inklusive fallstudier.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – mars 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I mars 2019 lade vi till dessa 14 nya appar med federationsstöd i appgalleriet:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fullcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), Power school [Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Oidde](https://cinode.com/), [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler- och Atlassian-etableringsanslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för appetablering:** Integrering från tredje part

Automatisera skapande, uppdatering och borttagning av användarkonton för följande appar:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Mer information om hur du skyddar din organisation bättre med hjälp av automatisk kontoetablering finns i Automatisera användareablering för [SaaS-program med Azure AD.](../app-provisioning/user-provisioning.md)

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera borttagna Office 365-grupper i Azure AD-portalen

**Typ:** Ny **funktionskategori: Produktfunktion** för **grupphantering:** Samarbete

Nu kan du visa och hantera dina borttagna Office 365-grupper från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för återställning, tillsammans med att du permanent kan ta bort alla grupper som inte behövs av din organisation.

Mer information finns i [Återställa grupper som har upphört att gälla eller tagits bort.](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgängligt för Azure AD SAML-skyddade lokala appar via Programproxy (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Access Control

Nu kan du tillhandahålla enkel inloggning (SSO) för lokala SAML-autentiserade appar, tillsammans med fjärråtkomst till dessa appar via Programproxy. Mer information om hur du ställer in SAML SSO med dina lokala appar finns i [SAML single sign-on for on-premises applications with Programproxy (Preview) (SAML enkel](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md)inloggning för lokala program med Programproxy (förhandsversion) .

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientappar i begärandeslingor avbryts för att förbättra tillförlitligheten och användarupplevelsen

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Klientappar kan felaktigt utfärda hundratals av samma inloggningsbegäranden under en kort tidsperiod. Alla dessa begäranden, oavsett om de lyckas eller inte, bidrar till en dålig användarupplevelse och förhöjda arbetsbelastningar för IDP, ökar svarstiden för alla användare och minskar tillgängligheten för IDP.

Den här uppdateringen skickar ett fel: till klientappar som utfärdar duplicerade begäranden flera gånger under en kort tidsperiod, utanför `invalid_grant` `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` omfånget för normal drift. Klientappar som stöter på det här problemet bör visa en interaktiv uppmaning som kräver att användaren loggar in igen. Mer information om den här ändringen och om hur du åtgärdar din app om den stöter på det här felet finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Ny användarupplevelse för granskningsloggar är nu tillgänglig

**Typ:** **Funktionskategorin Har ändrats:** **Rapportera produktfunktion:** Övervakning & rapportering

Vi har skapat en ny sida för Azure **AD-granskningsloggar** för att förbättra både läsbarheten och hur du söker efter din information. Om du vill se **den nya sidan** Granskningsloggar väljer **du** Granskningsloggar **i avsnittet** Aktivitet i Azure AD.

![Ny sida för granskningsloggar med exempelinformation](media/whats-new/audit-logs-page.png)

Mer information om den nya sidan **Granskningsloggar** finns i [Granskningsaktivitetsrapporter i Azure Active Directory portalen.](../reports-monitoring/concept-audit-logs.md#audit-logs)

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och vägledning för att förhindra oavsiktlig administratörslåsning från felkonfigurerade principer för villkorsstyrd åtkomst

**Typ:** **Tjänstkategorin Har ändrats:** **Produktfunktion för villkorsstyrd åtkomst:** Identity Security & Protection

För att förhindra att administratörer oavsiktligt låser sig ut från sina egna klienter via felkonfigurerade principer för villkorlig åtkomst har vi skapat nya varningar och uppdaterad vägledning i Azure Portal. Mer information om den nya vägledningen finns i [Vad är tjänstberoenden i Azure Active Directory villkorlig åtkomst.](../conditional-access/service-dependencies.md)

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrade användningsvillkor för slutanvändare på mobila enheter

**Typ:** **Tjänstkategorin Har ändrats:** Användningsvillkor **produktfunktion:** Styrning

Vi har uppdaterat våra befintliga användningsvillkor för att förbättra hur du granskar och godkänner användningsvillkoren på en mobil enhet. Nu kan du zooma in och ut, gå tillbaka, ladda ned informationen och välja hyperlänkar. Mer information om de uppdaterade användningsvillkoren finns [i Azure Active Directory för användningsvillkor.](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users)

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Ny nedladdningsupplevelse för Azure AD-aktivitetsloggar är tillgänglig

**Typ:** Tjänstkategori **för ändrade funktioner:** **Rapportera produktfunktion:** Övervakning & rapportering

Du kan nu ladda ned stora mängder aktivitetsloggar direkt från Azure Portal. Med den här uppdateringen kan du:

- Ladda ned upp till 250 000 rader.

- Bli meddelad när nedladdningen är klar.

- Anpassa filnamnet.

- Fastställ ditt utdataformat, antingen JSON eller CSV.

Mer information om den här funktionen finns i [Snabbstart: Ladda ned en granskningsrapport med hjälp av Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Stor ändring: Uppdateringar av villkorsutvärdering av Exchange ActiveSync (EAS)

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för villkorsstyrd åtkomst:** Access Control

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användarens plats, baserat på land, region eller IP-adress

- Inloggningsrisk

- Enhetsplattform

Om du tidigare har använt dessa villkor i principerna för villkorsstyrd åtkomst bör du vara medveten om att villkorsbeteendet kan ändras. Om du till exempel tidigare använde villkoret för användarplats i en princip kan det hända att principen nu hoppas över baserat på användarens plats.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML-tokenkryptering (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar:** Enkel inloggning

Du kan nu konfigurera alla SAML-appar som stöds för att ta emot krypterade SAML-token. När Azure AD konfigureras och används med en app krypterar det utgivna SAML-intyget med hjälp av en offentlig nyckel som hämtas från ett certifikat som lagras i Azure AD.

Mer information om hur du konfigurerar din SAML-tokenkryptering finns [i Konfigurera Azure AD SAML-tokenkryptering.](../manage-apps/howto-saml-token-encryption.md)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning för grupper eller appar med Hjälp av Azure AD-åtkomstgranskningar

**Typ:** Ny **funktionskategori: Åtkomstgranskningar** **Produktfunktion:** Styrning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD-åtkomstgranskning för gruppmedlemskap eller apptilldelning. Åtkomstgranskningar med flera grupper eller appar konfigureras med samma inställningar och alla inkluderade granskare meddelas samtidigt.

Mer information om hur du skapar en åtkomstgranskning med Azure AD-åtkomstgranskningar finns i Skapa en åtkomstgranskning av grupper eller program [i Azure AD-åtkomstgranskningar](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – februari 2019

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I februari 2019 lade vi till dessa 27 nya appar med federationsstöd i appgalleriet:

[Euromonitor Passport,](../saas-apps/euromonitor-passport-tutorial.md) [MindTickle,](../saas-apps/mindtickle-tutorial.md) [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive,](../saas-apps/idrive-tutorial.md) [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO,](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)Permission Click, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [Seismic](../saas-apps/seismic-tutorial.md), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), Knowledge Anywhere [LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerad MFA/SSPR-registrering

**Typ:** Funktionskategorin **Har ändrats: Produktfunktion** för lösenordsåterställning **via självbetjäning:** Användarautentisering

Som svar på kundfeedback har vi förbättrat den kombinerade upplevelsen för förhandsversionen av MFA/SSPR-registrering, vilket hjälper dina användare att snabbare registrera sin säkerhetsinformation för både MFA och SSPR.

**Följ dessa steg om du vill aktivera den förbättrade upplevelsen för användarnas idag:**

1. Som global administratör eller användaradministratör loggar du in på Azure Portal och går till Azure Active Directory > Användarinställningar > Hantera inställningar för förhandsgranskningsfunktioner **på åtkomstpanelen.**

2. I alternativet **Användare som** kan använda förhandsgranskningsfunktionerna för registrering och hantering av säkerhetsinformation – uppdatering väljer du att aktivera funktionerna för en **vald** grupp av användare eller för **Alla användare.**

Under de kommande veckorna kommer vi att ta bort möjligheten att aktivera den gamla kombinerade förhandsversionen av MFA/SSPR-registrering för klienter som inte redan har den aktiverad.

**Följ dessa steg för att se om kontrollen kommer att tas bort för din klientorganisation:**

1. Som global administratör eller användaradministratör loggar du in på Azure Portal och går till Azure Active Directory > Användarinställningar > Hantera inställningar för förhandsgranskningsfunktioner **på åtkomstpanelen.**

2. Om alternativet **Användare som kan** använda förhandsgranskningsfunktionerna för registrering och hantering av säkerhetsinformation är inställt på **Ingen** tas alternativet bort från klientorganisationen.

Oavsett om du tidigare har aktiverat den gamla kombinerade förhandsversionen av MFA/SSPR-registrering för användare eller inte, kommer den gamla upplevelsen att stängas av vid ett framtida datum. Därför rekommenderar vi starkt att du flyttar till den nya, förbättrade upplevelsen så snart som möjligt.

Mer information om den förbättrade registreringsupplevelsen finns i Cool [enhancements to the Azure AD combined MFA and password reset registration experience](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterad principhanteringsupplevelse för användarflöden

**Typ:** **Funktionskategorin Har ändrats:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Vi har uppdaterat processen för att skapa och hantera principer för användarflöden (kallades tidigare inbyggda principer) enklare. Den här nya upplevelsen är nu standard för alla dina Azure AD-klienter.

Du kan ge ytterligare feedback och förslag genom att använda ikonerna leende eller bägaren i området **Skicka feedback** överst på portalens skärm.

Mer information om den nya principhanteringsupplevelsen finns i Azure AD B2C nu har [JavaScript-anpassning och många fler nya funktioner blogg.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifika versioner av sidelement som tillhandahålls av Azure AD B2C

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Nu kan du välja en specifik version av de sidelement som tillhandahålls av Azure AD B2C. Genom att välja en specifik version kan du testa dina uppdateringar innan de visas på en sida och du kan få förutsägbart beteende. Dessutom kan du nu välja att framtvinga specifika sidversioner för att tillåta JavaScript-anpassningar. Om du vill aktivera den här funktionen går du till **sidan Egenskaper** i dina användarflöden.

Mer information om hur du väljer specifika versioner av sidelement finns i Azure AD B2C nu har [JavaScript-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösenordskrav för slutanvändare för B2C (GA)

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Nu kan du konfigurera organisationens lösenordskomplexitet för dina slutanvändare, i stället för att behöva använda din interna Azure AD-lösenordsprincip. På **bladet** Egenskaper för dina användarflöden (tidigare kallade inbyggda principer) kan du välja lösenordskomplexiteten  **Enkel** eller **Stark,** eller så kan du skapa en anpassad uppsättning krav.

Mer information om konfiguration av lösenordskomplexitetskrav finns [i Konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade anpassade autentiseringsupplevelser

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Du kan använda våra nya standardmallar som finns på bladet Sidlayouter i dina användarflöden (tidigare kallade inbyggda principer) för att skapa en anpassad anpassad **autentiseringsupplevelse** för dina användare.

Mer information om hur du använder mallar finns i [Azure AD B2C nu har JavaScript-anpassning och många fler nya funktioner.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete med autentisering med ett lösenord (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C

Vi har introducerat engångsautentisering med lösenord (OTP) för B2B-gästanvändare som inte kan autentiseras på andra sätt, till exempel Azure AD, en Microsoft-konto (MSA) eller Google-federation. Den här nya autentiseringsmetoden innebär att gästanvändare inte behöver skapa en ny Microsoft-konto. När du löser in en inbjudan eller får åtkomst till en delad resurs kan en gästanvändare i stället begära att en tillfällig kod skickas till en e-postadress. Med den här tillfälliga koden kan gästanvändaren fortsätta att logga in.

Mer information finns i E-postautentisering med ett lösenord [(förhandsversion)](../external-identities/one-time-passcode.md) och bloggen. Azure AD gör delning och samarbete sömlöst för alla användare [med alla konton.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya inställningar för Azure AD Programproxy-cookie

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Access Control

Vi har introducerat tre nya cookie-inställningar som är tillgängliga för dina appar som publiceras via Programproxy:

- **Använd HTTP-Only cookie.** Anger **HTTPOnly-flaggan** på din Programproxy för åtkomst och sessionscookies. Att aktivera den här inställningen ger ytterligare säkerhetsfördelar, till exempel att förhindra kopiering eller ändring av cookies via skript på klientsidan. Vi rekommenderar att du aktiverar den här flaggan (välj **Ja)** för de ytterligare fördelarna.

- **Använd säker cookie.** Anger flaggan **Säker** för dina Programproxy och sessionscookies. Om du använder den här inställningen får du ytterligare säkerhetsfördelar genom att se till att cookies endast överförs via TLS-säkra kanaler, till exempel HTTPS. Vi rekommenderar att du aktiverar den här flaggan (välj **Ja)** för de ytterligare fördelarna.

- **Använd beständig cookie.** Förhindrar att cookies upphör att gälla när webbläsaren stängs. Dessa cookies varar under åtkomsttokens livslängd. Cookies återställs dock om förfallotiden uppnås eller om användaren tar bort cookien manuellt. Vi rekommenderar att du behåller **standardinställningen Nej**, och endast aktivera inställningen för äldre appar som inte delar cookies mellan processer.

Mer information om de nya cookies finns i [Cookieinställningar för åtkomst till lokala program i Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – januari 2019

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I januari 2019 lade vi till dessa 35 nya appar med federationsstöd i appgalleriet:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), Expiration [Reminder](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya Azure AD Identity Protection förbättringar (offentlig förhandsversion)

**Typ:** **Tjänstkategorin Har ändrats:** **Produktfunktion för** Identity Protection: Identity Security & Protection

Vi är glada över att kunna meddela att vi har lagt till följande förbättringar Azure AD Identity Protection förhandsversion, inklusive:

- Ett uppdaterat och mer integrerat användargränssnitt

- Ytterligare API:er

- Förbättrad riskbedömning via maskininlärning

- Produktomfattande anpassning mellan riskfyllda användare och riskfyllda inloggningar

Mer information om förbättringarna finns i [Vad är Azure Active Directory Identity Protection (uppdaterat)?](../identity-protection/overview-identity-protection.md) för att lära dig mer och dela med dig av dina tankar genom uppmaningarna i produkten.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny Applås för appen Microsoft Authenticator iOS- och Android-enheter

**Typ:** Ny **funktionskategori: Microsoft Authenticator** för **appprodukt:** Identity Security & Protection

Om du vill skydda dina lösenord, appinformation och appinställningar för en gång kan du aktivera funktionen Applås i Microsoft Authenticator appen. Att aktivera Applås innebär att du uppmanas att autentisera med din PIN-kod eller biometrik varje gång du öppnar Microsoft Authenticator appen.

Mer information finns i vanliga frågor [Microsoft Authenticator appen](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrade Azure AD Privileged Identity Management exportfunktioner (PIM)

**Typ:** Ny **funktionskategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Privileged Identity Management (PIM) kan nu exportera alla aktiva och berättigade rolltilldelningar för en specifik resurs, vilket innefattar rolltilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de var tvungna att exportera rolltilldelningar för varje specifik resurs.

Mer information finns i Visa [aktivitets- och granskningshistorik för Azure-resursroller i PIM.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som har tagits bort från synkroniseringsomfånget växlar inte längre till endast molnbaserade konton

**Typ:** Fast **tjänstkategori:** Funktion för **användarhanteringsprodukt:** Katalog

>[!Important]
>Vi har hört och förstått din frustration på grund av den här korrigeringen. Därför har vi återställt den här ändringen tills vi har gjort korrigeringen enklare för dig att implementera i din organisation.

Vi har åtgärdat en bugg där DirSyncEnabled-flaggan för en  användare felaktigt växlas till Falskt när Active Directory Domain Services-objektet (AD DS) exkluderades från synkroniseringsomfånget och sedan flyttades till Papperskorgen i Azure AD under följande synkroniseringscykel. Till följd av den här korrigeringen gäller att om användaren undantas från synkroniseringsomfånget och sedan återställs från Azure AD Papperskorgen förblir användarkontot synkroniserat från lokala AD som förväntat och kan inte hanteras i molnet eftersom dess auktoritetskälla (SoA) förblir som lokal AD.

Innan den här korrigeringen fanns det ett problem när DirSyncEnabled-flaggan växlades till Falskt. Det gav fel intrycket att dessa konton konverterades till endast molnobjekt och att kontona kunde hanteras i molnet. Kontona behåller dock fortfarande sin SoA som lokal och alla synkroniserade egenskaper (skuggattribut) kommer från lokala AD. Det här tillståndet orsakade flera problem i Azure AD och andra molnarbetsbelastningar (till exempel Exchange Online) som förväntade sig att hantera dessa konton som synkroniserade från AD men som nu beter sig som endast molnbaserade konton.

Det enda sättet att konvertera ett synkroniserat -från AD-konto till endast molnkonto är att inaktivera DirSync på klientorganisationsnivå, vilket utlöser en backend-åtgärd för att överföra SoA. Den här typen av SoA-ändring kräver (men är inte begränsad till) rensning av alla lokala relaterade attribut (till exempel LastDirSyncTime och skuggattribut) och att skicka en signal till andra molnarbetsbelastningar för att få sina respektive objekt konverterade till ett endast molnkonto.

Den här korrigeringen förhindrar därför direkta uppdateringar av attributet ImmutableID för en användare som synkroniserats från AD, vilket i vissa tidigare scenarier krävdes. Som namnet antyder är ImmutableID för ett objekt i Azure AD avsett att vara oföränderligt. Nya funktioner som implementeras i Azure AD Connect Health och Azure AD Connect Synchronization-klienten är tillgängliga för att hantera sådana scenarier:

- **Storskalig OföränderligID-uppdatering för många användare i en mellannivå**

  Du måste till exempel göra en lång MIGRERING mellan skogar i AD DS. Lösning: Använd Azure AD Connect för  att konfigurera källankare och kopiera befintliga ImmutableID-värden från Azure AD till den lokala AD DS-användarens ms-DS-Consistency-Guid-attribut för den nya skogen när användaren migrerar. Mer information finns i [Använda ms-DS-ConsistencyGuid som sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Storskaliga ImmutableID-uppdateringar för många användare i ett enda försök**

  När du implementerar Azure AD Connect du till exempel göra ett misstag och nu måste du ändra attributet SourceAnchor. Lösning: Inaktivera DirSync på klientorganisationsnivå och avmarkera alla ogiltiga ImmutableID-värden. Mer information finns i Inaktivera [katalogsynkronisering för Office 365.](/office365/enterprise/turn-off-directory-synchronization)

- **Matcha om lokal användare med en befintlig användare i Azure AD** En användare som har återskapats i AD DS genererar till exempel en dubblett i Azure AD-kontot i stället för att matcha den med ett befintligt Azure AD-konto (överblivet objekt). Lösning: Använd Azure AD Connect Health i Azure Portal för att mappa om källankare/ImmutableID. Mer information finns i [scenariot med överblivna objekt.](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Icke-bakåtsnande ändring: Uppdateringar av schemat för gransknings- och inloggningsloggar via Azure Monitor

**Typ:** **Funktionskategorin Har ändrats:** **Rapportera produktfunktion:** Övervakning & rapportering

Vi publicerar för närvarande både gransknings- och inloggningsloggarna via Azure Monitor, så att du sömlöst kan integrera loggfilerna med dina SIEM-verktyg eller med Log Analytics. Baserat på din feedback och som förberedelse för den här funktionens meddelande om allmän tillgänglighet gör vi följande ändringar i schemat. Dessa schemaändringar och relaterade dokumentationsuppdateringar sker den första veckan i januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nya fält i granskningsschemat
Vi lägger till ett nytt **fält för Åtgärdstyp** för att ange vilken typ av åtgärd som utförs på resursen. Till exempel Lägg **till,** **Uppdatera** eller Ta **bort**.

#### <a name="changed-fields-in-the-audit-schema"></a>Ändrade fält i granskningsschemat
Följande fält ändras i granskningsschemat:

|Fältnamn|Vad som har ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|Kategori|Det här var **fältet Tjänstnamn.** Nu är det fältet **Granskningskategorier.** **Namnet på** tjänsten har bytt namn till fältet **loggedByService.**|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Lösenordsåterställning via självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Apphantering</li></ul>|
|targetResources|Innehåller **TargetResourceType** på den översta nivån.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Användare</li><li>Group</li></ul>|
|loggedByService|Anger namnet på den tjänst som genererade granskningsloggen.|Null|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord för självbetjäning</li></ul>|
|Resultat|Visar resultatet av granskningsloggarna. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Klart</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggningsschemat
Följande fält ändras i inloggningsschemat:

|Fältnamn|Vad som har ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Det här var **fältet conditionalaccessPolicies.** Nu är det **fältet appliedConditionalAccessPolicies.**|Ingen ändring|Ingen ändring|
|conditionalAccessStatus|Visar resultatet av status för principen för villkorsstyrd åtkomst vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Tillämpas inte</li><li>Inaktiverad</li></ul>|
|appliedConditionalAccessPolicies: result|Visar resultatet av den enskilda statusen för principen för villkorsstyrd åtkomst vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Tillämpas inte</li><li>Inaktiverad</li></ul>|

Mer information om schemat finns i Tolka schemat [för Azure AD-granskningsloggar i Azure Monitor (förhandsversion)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection-förbättringar av den övervakade maskininlärningsmodellen och riskpoängmotorn

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Produktkapacitet för Identity Protection:** Riskpoäng

Förbättringar av den Identity Protection-relaterade riskutvärderingsmotorn för användare och inloggning kan hjälpa till att förbättra användarnas riskprecision och täckning. Administratörer kan märka att användarrisknivån inte längre är direkt kopplad till risknivån för specifika identifieringar och att antalet och nivån för riskfyllda inloggningshändelser ökar.

Riskidentifieringarna utvärderas nu av den övervakade maskininlärningsmodellen, som beräknar användarrisken med hjälp av ytterligare funktioner i användarens inloggningar och ett identifieringsmönster. Baserat på den här modellen kan administratören hitta användare med höga riskpoäng, även om identifieringar som är associerade med den användaren har låg eller medelhög risk.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sina egna lösenord med hjälp Microsoft Authenticator app (offentlig förhandsversion)

**Typ:** Funktionskategorin **Har ändrats: Produktfunktion** för lösenordsåterställning **via självbetjäning:** Användarautentisering

Azure AD-administratörer kan nu återställa sina egna lösenord med hjälp Microsoft Authenticator-meddelanden eller en kod från valfri mobil autentiseringsapp eller maskinvarutoken. Administratörer kan nu använda två av följande metoder för att återställa sina egna lösenord:

- Microsoft Authenticator appmeddelande

- Annan mobil autentiseringsapp/Kod för maskinvarutoken

- E-post

- Telefonsamtal

- Textmeddelande

Mer information om hur du använder Microsoft Authenticator för att återställa lösenord finns i Självåterställning av lösenord för Azure AD – mobilapp [och SSPR (förhandsversion)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny administratörsroll för Azure AD-molnenhet (offentlig förhandsversion)

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för **enhetsregistrering och hantering:** Åtkomstkontroll

Administratörer kan tilldela användare till den nya rollen Molnenhetsadministratör för att utföra administratörsuppgifter för molnenhet. Användare som har tilldelats rollen Molnenhetsadministratörer kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med att kunna läsa Windows 10 BitLocker-nycklar (om de finns) i Azure Portal.

Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med hjälp av den nya aktivitetsstämpeln i Azure AD (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Produktkapacitet** för enhetsregistrering **och hantering: Hantering** av enhetens livscykel

Vi inser att du med tiden måste uppdatera och dra tillbaka organisationens enheter i Azure AD för att undvika att ha inaktuella enheter i din miljö. Som hjälp med den här processen uppdaterar Azure AD nu dina enheter med en ny aktivitetsstämpel, vilket hjälper dig att hantera enhetens livscykel.

Mer information om hur du hämtar och använder den här tidsstämpeln finns i How To: Manage the stale devices in Azure AD (Så här gör du: Hantera [inaktuella enheter i Azure AD)](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användarna godkänner användningsvillkoren på varje enhet

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Styrning

Administratörer kan nu aktivera alternativet **Kräv att** användarna godkänner varje enhet för att kräva att användarna godkänner användningsvillkoren på varje enhet som de använder i din klientorganisation.

Mer information finns i avsnittet Användningsvillkor per enhet i Azure Active Directory [för användningsvillkor.](../conditional-access/terms-of-use.md#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användningsvillkor för att upphöra att gälla baserat på ett återkommande schema

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Styrning


Administratörer kan nu aktivera alternativet **Förfalla** medgivanden för att göra så att användningsvillkoren upphör att gälla för alla användare baserat på ditt angivna återkommande schema. Schemat kan vara varje år, varannan år, kvartalsvis eller månadsvis. När användningsvillkoren har upphört att gälla måste användarna uppfylla villkoren på ett annat sätt.

Mer information finns i avsnittet [Lägg till användningsvillkor i Azure Active Directory för användningsvillkor.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera användningsvillkoren så att de upphör att gälla baserat på varje användares schema

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Styrning

Administratörer kan nu ange en varaktighet som användaren måste uppfylla ett användningsvillkor på nytt. Administratörer kan till exempel ange att användarna måste uppfylla användningsvillkoren var 90:e dag.

Mer information finns i avsnittet [Lägg till användningsvillkor i Azure Active Directory för användningsvillkor.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>E-Azure AD Privileged Identity Management E-post (PIM) för Azure Active Directory roller

**Typ:** Ny **tjänstkategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Kunder som använder Azure AD Privileged Identity Management (PIM) kan nu få ett veckovis sammanfattat e-postmeddelande, inklusive följande information för de senaste sju dagarna:

- Översikt över de främsta berättigade och permanenta rolltilldelningarna

- Antal användare som aktiverar roller

- Antal användare som tilldelats roller i PIM

- Antal användare som tilldelats roller utanför PIM

- Antal användare som "gjorts permanenta" i PIM

Mer information om PIM och tillgängliga e-postaviseringar finns i [E-postmeddelanden i PIM.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="group-based-licensing-is-now-generally-available"></a>Gruppbaserad licensiering är nu allmänt tillgänglig

**Typ:** **Tjänstkategorin Har ändrats:** **Annan produktfunktion:** Katalog

Gruppbaserad licensiering är inte längre en offentlig förhandsversion och är nu allmänt tillgänglig. Som en del av den här allmänna versionen har vi gjort den här funktionen mer skalbar och har lagt till möjligheten att bearbeta om gruppbaserade licensieringstilldelningar för en enskild användare och möjligheten att använda gruppbaserad licensiering med Office 365 E3/A3-licenser.

Mer information om gruppbaserad licensiering finns i [Vad är gruppbaserad licensiering i Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – november 2018

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I november 2018 lade vi till dessa 26 nya appar med federationsstöd i appgalleriet:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [Drive Hubblar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Algivande](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Hubegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up) [,Moderti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), MEN - [Plex Records, Olic, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-loggar fungerar nu med Azure Log Analytics (offentlig förhandsversion)

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Vi är glada över att kunna meddela att du nu kan vidarebefordra dina Azure AD-loggar till Azure Log Analytics! Den här mest efterfrågade funktionen ger dig ännu bättre åtkomst till analyser för ditt företag, din verksamhet och din säkerhet, samt ett sätt att övervaka din infrastruktur. Mer information finns i bloggen [Azure Active Directory Aktivitetsloggar i Azure Log Analytics.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – oktober 2018

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I oktober 2018 lade vi till dessa 14 nya appar med federationsstöd i appgalleriet:

[My Award Points](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [LånaBox,](../saas-apps/borrowbox-tutorial.md)Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfink](../saas-apps/glassfrog-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-postaviseringar

**Typ:** Ny **tjänstkategori:** Azure AD Domain Services **produktfunktion:** Azure AD Domain Services

Azure AD Domain Services innehåller aviseringar på Azure Portal om felkonfigurationer eller problem med din hanterade domän. De här aviseringarna innehåller stegvisa guider så att du kan försöka åtgärda problemen utan att behöva kontakta supporten.

Från och med oktober kommer du att kunna anpassa meddelandeinställningarna för din hanterade domän. När nya aviseringar inträffar skickas ett e-postmeddelande till en angiven grupp med personer, vilket eliminerar behovet av att hela tiden söka efter uppdateringar på portalen.

Mer information finns i [Meddelandeinställningar i Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD-portalen stöder användning av ForceDelete-domän-API:et för att ta bort anpassade domäner

**Typ:** **Tjänstkategorin Har ändrats:** **Kataloghanteringsproduktkapacitet:** Katalog

Vi är glada över att kunna meddela att du nu kan använda ForceDelete-domän-API:et för att ta bort dina anpassade domännamn genom att asynkront byta namn på referenser som användare, grupper och appar från ditt anpassade domännamn (contoso.com) tillbaka till det ursprungliga standarddomännamnet (contoso.onmicrosoft.com).

Den här ändringen hjälper dig att snabbare ta bort dina anpassade domännamn om din organisation inte längre använder namnet, eller om du behöver använda domännamnet med en annan Azure AD.

Mer information finns i Ta [bort ett anpassat domännamn.](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade administratörsrollbehörigheter för dynamiska grupper

**Typ:** Fast **tjänstkategori:** Funktion för **grupphanteringsprodukt:** Samarbete

Vi har åtgärdat ett problem så att specifika administratörsroller nu kan skapa och uppdatera regler för dynamiskt medlemskap utan att behöva vara ägare till gruppen.

Rollerna är:

- Global administratör

- Intune-administratör

- Användaradministratör

Mer information finns i [Skapa en dynamisk grupp och kontrollera status](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Förenklade konfigurationsinställningar Sign-On enkel inloggning (SSO) för vissa appar från tredje part

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar:** Enkel inloggning

Vi inser att det kan vara svårt att konfigurera En Sign-On (SSO) för SaaS-appar (Programvara som en tjänst) på grund av varje apps konfigurations unika egenskaper. Vi har skapat en förenklad konfigurationsupplevelse för att automatiskt fylla i konfigurationsinställningarna för enkel inloggning för följande SaaS-appar från tredje part:

- Zendesk

- ArcGis Online

- Jamf Pro

Om du vill börja använda den här upplevelsen med ett klick **går du Azure Portal**  >  **konfigurationssidan för** enkel inloggning för appen. Mer information finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Var finns dina data? Sida

**Typ:** Ny **funktionskategori:** Annan **produktfunktion:** GoLocal

Välj företagets region från sidan Azure Active Directory – Var finns dina data för att se vilka **Azure-datacenter** som innehåller dina Azure AD-data i vila för alla Azure AD-tjänster. Du kan filtrera informationen efter specifika Azure AD-tjänster för företagets region.

Mer information om den här funktionen finns i [Azure Active Directory – Var finns dina data?](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Ny distributionsplan tillgänglig för Mina appar åtkomstpanelen

**Typ:** Ny **funktionskategori: Mina appar** **produktfunktion:** enkel inloggning

Kolla in den nya distributionsplanen som är tillgänglig Mina appar åtkomstpanelen ( https://aka.ms/deploymentplans) .
Panelen Mina appar Access ger användarna en enda plats där de kan hitta och komma åt sina appar. Den här portalen ger även användare självbetjäningsmöjligheter, till exempel att begära åtkomst till appar och grupper eller hantera åtkomst till dessa resurser för andras räkning.

Mer information finns i [Vad är Mina appar portalen?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Ny felsöknings- och supportflik på sidan Inloggningsloggar i Azure Portal

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Den nya fliken Felsökning  och support på sidan Inloggningar i Azure Portal är avsedd att hjälpa administratörer och **supporttekniker** att felsöka problem som rör Azure AD-inloggningar. Den här nya fliken innehåller felkod, felmeddelande och reparationsrekommendationer (om några) för att lösa problemet. Om du inte kan lösa problemet ger vi dig också ett nytt sätt att skapa en supportbegäran med hjälp av upplevelsen Kopiera till **Urklipp,** som fyller i fälten **Id** för begäran och **Datum (UTC)** för loggfilen i supportbegäran.

![Inloggningsloggar som visar den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrat stöd för anpassade tilläggsegenskaper som används för att skapa regler för dynamiskt medlemskap

**Typ:** **Tjänstkategorin Har ändrats:** **Produktfunktion för grupphantering:** Samarbete

Med den här uppdateringen kan  du nu klicka på länken Hämta egenskaper för anpassade tillägg från det dynamiska verktyget för användargruppsregel, ange ditt unika app-ID och få en fullständig lista över egenskaper för anpassade tillägg som ska användas när du skapar en regel för dynamiskt medlemskap för användare. Den här listan kan också uppdateras för att hämta nya anpassade tilläggsegenskaper för appen.

Mer information om hur du använder anpassade tilläggsegenskaper för regler för dynamiskt medlemskap finns i [Tilläggsegenskaper och anpassade tilläggsegenskaper](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för appbaserad villkorsstyrd åtkomst i Azure AD

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Följande appar finns i listan över godkända [klientappar:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft To-Do

- Microsoft Stream

Mer information finns i:

- [Appbaserad villkorlig åtkomst i Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nytt stöd för Self-Service lösenordsåterställning från låsskärmen i Windows 7/8/8.1

**Typ:** Ny **funktionskategori:** **SSPR-produktfunktion:** Användarautentisering

När du har ställt in den här nya funktionen ser  användarna en länk för att återställa sitt lösenord från låsskärmen på en enhet som kör Windows 7, Windows 8 eller Windows 8.1. Genom att klicka på länken vägleds användaren genom samma flöde för lösenordsåterställning som via webbläsaren.

Mer information finns i [Så här aktiverar du lösenordsåterställning från Windows 7, 8 och 8.1](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning

**Typ:** Planera för ändring **av tjänstkategori:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Från och med den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringskoder för appar. Den här säkerhetsändringen hjälper till att hålla Azure AD i linje med OAuth-specifikationen och tillämpas på både v1- och v2-slutpunkterna.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser rekommenderar vi att du använder koden för att hämta en uppdateringstoken och sedan använder denna uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger över flera resurser. En app som försöker återanvända en autentiseringskod under OAuth-kodflödet får ett invalid_grant fel.

För den här och andra protokollrelaterade ändringar, [se den fullständiga listan över vad som är nytt för autentisering](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – september 2018

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I september 2018 lade vi till dessa 16 nya appar med federationsstöd i appgalleriet:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), join.me, [Ze dfsrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlok SSO for Azure, SurveyMonkey, [Convene](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Stöd för ytterligare metoder för anspråksomvandling

**Typ:** Ny **funktionstjänstkategori:** Produktkapacitet **för företagsappar:** enkel inloggning

Vi har introducerat nya metoder för anspråksomvandling, ToLower() och ToUpper(), som kan tillämpas på SAML-token från sidan SAML-baserad **enkel Sign-On-konfiguration.**

Mer information finns i Anpassa [anspråk som utfärdats i SAML-token för företagsprogram i Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat SAML-baserat appkonfigurationsgränssnitt (förhandsversion)

**Typ:** **Funktionskategorin För företagsprogram** har **ändrats: Produktfunktion för företagsappar:** enkel inloggning

Som en del av vårt uppdaterade SAML-baserade appkonfigurationsgränssnitt får du:

- En uppdaterad genomgång för att konfigurera dina SAML-baserade appar.

- Mer insyn i vad som saknas eller är felaktigt i konfigurationen.

- Möjlighet att lägga till flera e-postadresser för meddelande om utgångscertifikat.

- Nya metoder för anspråksomvandling, ToLower() och ToUpper() med mera.

- Ett sätt att ladda upp ditt eget certifikat för tokensignering för dina företagsappar.

- Ett sätt att ange NameID-format för SAML-appar och ett sätt att ange NameID-värdet som Katalogtillägg.

Om du vill aktivera den här uppdaterade vyn **klickar du på länken Testa** vår nya upplevelse överst på sidan **Enkel** inloggning. Mer information finns i [Självstudie: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar Azure Active Directory IP-adressintervall

**Typ:** Planera för ändring **av tjänstkategori:** **Annan produktfunktion:** Plattform

Vi introducerar större IP-intervall för Azure AD, vilket innebär att om du har konfigurerat Azure AD IP-adressintervall för dina brandväggar, routrar eller nätverkssäkerhetsgrupper måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra konfigurationerna för brandvägg, router eller nätverkssäkerhetsgruppers IP-intervall igen när Azure AD lägger till nya slutpunkter.

Nätverkstrafiken flyttas till dessa nya intervall under de kommande två månaderna. Om du vill fortsätta med oavbruten tjänst måste du lägga till dessa uppdaterade värden till dina IP-adresser före den 10 september 2018:

- 20.190.128.0/18

- 40.126.0.0/18

Vi rekommenderar starkt att du inte tar bort de gamla IP-adressintervallen förrän all nätverkstrafik har flyttats till de nya intervallen. Uppdateringar om flytten och när du kan ta bort gamla intervall finns i URL:er och IP-adressintervall för [Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning

**Typ:** Planera för ändring **av tjänstkategori:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Från och med den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringskoder för appar. Den här säkerhetsändringen hjälper till att hålla Azure AD i linje med OAuth-specifikationen och tillämpas på både v1- och v2-slutpunkterna.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser rekommenderar vi att du använder koden för att hämta en uppdateringstoken och sedan använder denna uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger över flera resurser. En app som försöker återanvända en autentiseringskod under OAuth-kodflödet får ett invalid_grant fel.

För den här och andra protokollrelaterade ändringar, [se den fullständiga listan över vad som är nytt för autentisering](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergerad säkerhetsinformationshantering för självbetjäningslösenord (SSPR) och multifaktorautentisering (MFA)

**Typ:** Ny **funktionstjänstkategori:** **SSPR-produktfunktion:** Användarautentisering

Den här nya funktionen hjälper användare att hantera sin säkerhetsinformation (till exempel telefonnummer, mobilapp och så vidare) för SSPR och MFA på en enda plats och i samma upplevelse. jämfört med tidigare, där det gjordes på två olika platser.

Den här konvergerade upplevelsen fungerar även för personer som använder antingen SSPR eller MFA. Om din organisation inte tillämpar MFA- eller SSPR-registrering kan personer fortfarande registrera MFA- eller SSPR-säkerhetsinformationsmetoder som tillåts av din organisation från Mina appar portalen.

Det här är en offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en vald grupp eller för alla användare i en klientorganisation. Mer information om konvergerad upplevelse finns i bloggen [om konvergerad upplevelse](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Ny HTTP-Only för cookies i Azure AD-programproxyappar

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Access Control

Det finns en ny inställning som kallas **HTTP-endast cookies** i dina Programproxy appar. Den här inställningen ger extra säkerhet genom att inkludera HTTPOnly-flaggan i HTTP-svarshuvudet för både Programproxy-åtkomst och sessionscookies, stoppa åtkomsten till cookien från ett skript på klientsidan och ytterligare förhindra åtgärder som att kopiera eller ändra cookien. Även om den här flaggan inte har använts tidigare har dina cookies alltid krypterats och överförts med hjälp av en TLS-anslutning för att skydda mot felaktiga ändringar.

Den här inställningen är inte kompatibel med appar som använder ActiveX-kontroller, till exempel Fjärrskrivbord. Om du är i den här situationen rekommenderar vi att du inaktiverar den här inställningen.

Mer information om inställningen HTTP-Only cookies finns i [Publicera program med Azure AD Programproxy](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) för Azure-resurser stöder resurstyper för hanteringsgrupp

**Typ:** Ny **funktionskategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Inställningar för just-in-time-aktivering och tilldelning kan nu tillämpas på resurstyper för hanteringsgrupper, precis som du redan gör för prenumerationer, resursgrupper och resurser (till exempel virtuella datorer, App Services med mera). Dessutom kan alla med en roll som ger administratörsåtkomst för en hanteringsgrupp identifiera och hantera resursen i PIM.

Mer information om PIM och Azure-resurser finns i [Identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Programåtkomst (förhandsversion) ger snabbare åtkomst till Azure AD-portalen

**Typ:** Ny **funktionskategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

När du aktiverar en roll med hjälp av PIM idag kan det ta över 10 minuter innan behörigheterna börja gälla. Om du väljer att använda Programåtkomst, som för närvarande är i offentlig förhandsversion, kan administratörer komma åt Azure AD-portalen så snart aktiveringsbegäran har slutförts.

För närvarande har programåtkomst endast stöd för Azure AD-portalen och Azure-resurser. Mer information om PIM och programåtkomst finns i [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet – augusti 2018

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I augusti 2018 lade vi till dessa 16 nya appar med federationsstöd i appgalleriet:

[Hornbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), [Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), Meta Networks [Connector](../saas-apps/metanetworksconnector-tutorial.md), Way [We Do](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), Bridge , [N2F -](../saas-apps/n2f-expensereports-tutorial.md) [Expense](../saas-apps/dossier-tutorial.md)reports , [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [FaqQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Inbyggt Tableau-stöd är nu tillgängligt i Azure AD Programproxy

**Typ:** **Funktionskategorin Har ändrats:** Produktfunktion **för appproxy:** Access Control

Med vår uppdatering från OpenID Connect till OAuth 2.0 Code Grant-protokollet för vårt förautentiseringsprotokoll behöver du inte längre göra någon ytterligare konfiguration för att använda Tableau med Programproxy. Den här protokolländringen hjälper Programproxy att ge bättre stöd för modernare appar genom att endast använda HTTP-omdirigeringar, som ofta stöds i JavaScript- och HTML-taggar.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nytt stöd för att lägga till Google som identitetsprovider för B2B-gästanvändare i Azure Active Directory (förhandsversion)

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C

Genom att konfigurera federation med Google i din organisation kan du låta inbjudna Gmail-användare logga in på dina delade appar och resurser med sitt befintliga Google-konto, utan att behöva skapa ett personligt Microsoft-konto (MSA) eller ett Azure AD-konto.

Det här är en offentlig förhandsversion. Mer information om Google-federation finns i [Lägga till Google som en identitetsprovider för B2B-gästanvändare.](../external-identities/google-federation.md)

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av Azure Active Directory-postaviseringar

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Annan produktfunktion: Hantering** av identitetslivscykel

Azure Active Directory e-post (Azure AD) har nu en uppdaterad design, samt ändringar i avsändarens e-postadress och visningsnamn när de skickas från följande tjänster:

- Azure AD-åtkomstgranskningar
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Certifikatmeddelanden för företagsapp som upphör att gälla
- Meddelanden om Enterprise App Provisioning-tjänsten

E-postmeddelandena skickas från följande e-postadress och visningsnamn:

- E-postadress: azure-noreply@microsoft.com
- Visningsnamn: Microsoft Azure

Ett exempel på några av de nya e-postdesignerna och mer information finns i E-postmeddelanden [i Azure AD PIM.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga via Azure Monitor

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Azure AD-aktivitetsloggarna är nu tillgängliga i offentlig förhandsversion för Azure Monitor (Azures plattformsomfattande övervakningstjänst). Azure Monitor erbjuder långsiktig kvarhållning och sömlös integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning genom att dirigera loggfilerna till ditt eget Azure Storage-konto.

- Sömlös SIEM-integrering utan att du behöver skriva eller underhålla anpassade skript.

- Smidig integrering med dina egna anpassade lösningar, analysverktyg eller incidenthanteringslösningar.

Mer information om de här nya funktionerna finns i vår blogg Azure AD-aktivitetsloggar [i Azure Monitor diagnostics nu](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) är i offentlig förhandsversion och vår [dokumentation, Azure Active Directory aktivitetsloggar i Azure Monitor (förhandsversion)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information om villkorsstyrd åtkomst har lagts till i Azure AD-inloggningsrapporten

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion:** Identity Security & Protection

Med den här uppdateringen kan du se vilka principer som utvärderas när en användare loggar in tillsammans med principresultatet. Dessutom innehåller rapporten nu den typ av klientapp som används av användaren, så att du kan identifiera äldre protokolltrafik. Rapportposter kan nu också sökas efter ett korrelations-ID som finns i det användarriktade felmeddelandet och kan användas för att identifiera och felsöka matchande inloggningsbegäran.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggningsaktivitetsloggar

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Med introduktionen av **fältet Klientapp** i inloggningsaktivitetsloggarna kan kunder nu se användare som använder äldre autentiseringar. Kunder kommer att kunna komma åt den här informationen med hjälp av API:et för Microsoft Graph-inloggningar  eller inloggningsaktivitetsloggarna i Azure AD-portalen där du kan använda klientappkontrollen för att filtrera på äldre autentiseringar. Mer information finns i dokumentationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet – juli 2018

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I juli 2018 lade vi till dessa 16 nya appar med federationsstöd i appgalleriet:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC Staging, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [Screencast-O-Matic](../saas-apps/screencast-tutorial.md), Power School Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimb lex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](../saas-apps/skillsbase-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användareablering av SaaS-appintegreringar – juli 2018

**Typ:** Ny **funktionskategori:** Produktfunktion för **appetablering: Integrering** från tredje part

Med Azure AD kan du automatisera skapande, underhåll och borttagning av användaridentiteter i SaaS-program som Dropbox, Salesforce, ServiceNow med mera. För juli 2018 har vi lagt till stöd för användareablering för följande program i Azure AD-appgalleriet:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

En lista över alla program som stöder användareablering i Azure AD-galleriet finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för synkronisering – Ett enklare sätt att åtgärda överblivna och duplicerade synkroniseringsfel för attribut

**Typ:** Ny **funktionskategori:** AD **Connect-produktfunktion: Övervakning** & rapportering

Azure AD Connect Health med självbetjäningsåtgärder som hjälper dig att markera och åtgärda synkroniseringsfel. Den här funktionen felsöker synkroniseringsfel med duplicerade attribut och åtgärdar objekt som är överblivna från Azure AD. Den här diagnosen har följande fördelar:

- Begränsar synkroniseringsfel för duplicerade attribut, vilket ger specifika korrigeringar

- Tillämpar en korrigering för dedikerade Azure AD-scenarier och löser fel i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [Diagnostisera och åtgärda synkroniseringsfel med duplicerade attribut](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av azure AD- och MSA-inloggningsupplevelser

**Typ:** **Funktionskategorin Tjänst har ändrats:** Azure **AD-produktfunktion:** Användarautentisering

Vi har uppdaterat användargränssnittet för Microsofts onlinetjänster inloggning, till exempel för Office 365 och Azure. Den här ändringen gör skärmarna mindre stökiga och enklare. Mer information om den här ändringen finns i bloggen Kommande förbättringar av [Azure AD-inloggningsupplevelsen.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Ny version av Azure AD Connect – juli 2018

**Typ:** **Funktionskategorin Har ändrats:** Produktfunktion **för appetablering: Hantering** av identitetslivscykel

Den senaste versionen av Azure AD Connect innehåller:

- Felkorrigeringar och supportuppdateringar

- Allmän tillgänglighet för Ping-Federate integrering

- Uppdateringar av den senaste SQL 2012-klienten

Mer information om den här uppdateringen finns i [Azure AD Connect: Versionshistorik](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Uppdateringar av användningsvillkoren för slutanvändargränssnittet

**Typ:** **Tjänstkategorin Har ändrats:** Användningsvillkor **produktfunktion:** Styrning

Vi uppdaterar godkännandesträngen i användargränssnittet för tou-slutanvändaren.

**Aktuell text.** För att få åtkomst till [tenantName]-resurser måste du godkänna användningsvillkoren.<br>**Ny text.** För att få åtkomst till resursen [tenantName] måste du läsa användningsvillkoren.

**Aktuell text:** Om du väljer att godkänna innebär det att du godkänner alla ovanstående användningsvillkor.<br>**Ny text:** Klicka på Acceptera för att bekräfta att du har läst och förstått användningsvillkoren.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Kategori för **funktionstjänsten har ändrats:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Direktautentisering stöder nu äldre protokoll och appar. Följande begränsningar stöds nu fullt ut:

- Användar inloggningar till äldre Office-klientprogram, Office 2010 och Office 2013, utan att kräva modern autentisering.

- Åtkomst till kalenderdelning och kostnadsfri/upptagen information i Exchange-hybridmiljöer endast i Office 2010.

- Användar inloggningar till Skype för företag-klientprogram utan att kräva modern autentisering.

- Användar inloggningar till PowerShell version 1.0.

- Apple Programmet för enhetsregistrering (Apple DEP) med hjälp av installationsassistenten för iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konvergerad säkerhetsinformationshantering för lösenordsåterställning via självbetjäning och multifaktorautentisering

**Typ:** Ny **funktionskategori:** **SSPR-produktfunktion:** Användarautentisering

Med den här nya funktionen kan användarna hantera sin säkerhetsinformation (till exempel telefonnummer, e-postadress, mobilapp och så vidare) för självbetjäning av lösenordsåterställning (SSPR) och multifaktorautentisering (MFA) i en enda upplevelse. Användarna behöver inte längre registrera samma säkerhetsinformation för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har antingen SSPR eller MFA.

Om en organisation inte framtvingar MFA- eller SSPR-registrering  kan användarna registrera sin säkerhetsinformation via Mina appar portalen. Därifrån kan användare registrera alla metoder som är aktiverade för MFA eller SSPR.

Det här är en offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om så önskas) för en vald grupp av användare eller alla användare i en klientorganisation.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd Microsoft Authenticator för att verifiera din identitet när du återställer lösenordet

**Typ:** **Funktionskategorin Tjänst har ändrats:** SSPR-produktfunktion: Användarautentisering 

Med den här funktionen kan icke-administratörer verifiera sin identitet när de återställer ett lösenord med hjälp av ett meddelande eller en kod från Microsoft Authenticator (eller någon annan autentiseringsapp). När administratörer aktiverar den här självbetjäningsmetoden för lösenordsåterställning kan användare som har registrerat en mobilapp via aka.ms/mfasetup eller aka.ms/setupsecurityinfo använda sin mobilapp som verifieringsmetod när de återställer sina lösenord.

Mobilappmeddelanden kan bara aktiveras som en del av en princip som kräver två metoder för att återställa lösenordet.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: Säkerhetskorrigering för det delegerade auktoriseringsflödet för appar med hjälp av API för Azure AD-aktivitetsloggar

**Typ:** Planera för ändring **av tjänstkategori:** **Rapportera produktfunktion:** Övervakning & rapportering

På grund av vårt starkare säkerhetstvingande har vi varit tvungna att göra en ändring i behörigheterna för appar som använder ett delegerat auktoriseringsflöde för att få åtkomst till API:er för [Azure AD-aktivitetsloggar.](../reports-monitoring/concept-reporting-api.md) Den här ändringen sker den **26 juni 2018**.

Om någon av dina appar använder API:er för Azure AD-aktivitetslogg följer du dessa steg för att säkerställa att appen inte bryts efter att ändringen har ändrats.

**Så här uppdaterar du dina appbehörigheter**

1. Logga in på Azure Portal, **välj Azure Active Directory** och välj sedan **Appregistreringar.**
2. Välj din app som använder API:et för Azure AD-aktivitetsloggar, välj **Inställningar,** välj Nödvändiga behörigheter och välj sedan Windows **Azure Active Directory** API.
3. I området **Delegerade** behörigheter på **bladet Aktivera** åtkomst markerar du rutan bredvid **Läsa katalogdata** och väljer sedan **Spara.**
4. Välj **Bevilja behörigheter** och välj sedan **Ja.**

    >[!Note]
    >Du måste vara en Global administratör att bevilja behörigheter till appen.

Mer information finns i området Bevilja [behörigheter i artikeln](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) Krav för att få åtkomst till Azure AD Reporting API.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS-inställningar för att ansluta till Azure AD-tjänster för PCI DSS efterlevnad

**Typ:** Ny **funktionskategori:** **Produktfunktion: Plattform**

Transport Layer Security (TLS) är ett protokoll som ger sekretess och dataintegritet mellan två kommunicerande program och är det mest distribuerade säkerhetsprotokoll som används i dag.

[PCI Security Standards Standards Har](https://www.pcisecuritystandards.org/) fastställt att tidiga versioner av TLS och Secure Sockets Layer (SSL) måste inaktiveras för att aktivera nya och säkrare appprotokoll, med efterlevnad från och med den 30 juni **2018.** Den här ändringen innebär att om du ansluter till Azure AD-PCI DSS kräver efterlevnad måste du inaktivera TLS 1.0. Flera versioner av TLS är tillgängliga, men TLS 1.2 är den senaste versionen som är tillgänglig för Azure Active Directory Services. Vi rekommenderar starkt att du flyttar direkt till TLS 1.2 för både klient-/server- och webbläsar-/serverkombinationer.

In-of-date-webbläsare kanske inte stöder nyare TLS-versioner, till exempel TLS 1.2. Om du vill se vilka versioner av TLS som stöds av webbläsaren går du till [webbplatsen Qualys SSL Labs](https://www.ssllabs.com/) och klickar på Testa **webbläsaren.** Vi rekommenderar att du uppgraderar till den senaste versionen av webbläsaren och aktiverar helst endast TLS 1.2.

**Så här aktiverar du TLS 1.2, per webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med hjälp av Internet Explorer)**

    1. Öppna Internet Explorer och välj **Verktyg**  >  **Internetalternativ**  >  **Avancerat.**
    2. I området **Säkerhet** väljer du **Använd TLS 1.2** och sedan **OK.**
    3. Stäng alla webbläsarfönster och starta om Internet Explorer.

- **Google Chrome**

    1. Öppna Google Chrome, *skriv chrome://settings/* i adressfältet och tryck på **Retur.**
    2. Expandera Avancerade **alternativ,** gå till **området System** och välj **Öppna proxyinställningar.**
    3. I rutan **Internetegenskaper** väljer du **fliken Avancerat,** går till området Säkerhet, väljer **använd TLS 1.2** och sedan **OK.** 
    4. Stäng alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, skriv *about:config* i adressfältet och tryck sedan på **Retur.**
    2. Sök efter termen *TLS* och välj posten **security.tls.version.max.**
    3. Ange värdet till **3 för** att tvinga webbläsaren att använda upp till version TLS 1.2 och välj sedan **OK.**

        >[!NOTE]
        >Firefox version 60.0 stöder TLS 1.3, så du kan också ange värdet security.tls.version.max till **4.**

    4. Stäng alla webbläsarfönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – juni 2018

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I juni 2018 lade vi till dessa 15 nya appar med federationsstöd i appgalleriet:

[Skytap](../saas-apps/skytap-tutorial.md), [Setling music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md), [Supermood](../saas-apps/supermood-tutorial.md), [Autotask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), Vid [mam](../saas-apps/vidyard-tutorial.md), [LobX](../saas-apps/chronicx-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-lösenordsskydd är tillgängligt i offentlig förhandsversion

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för **Identitetsskydd:** Användarautentisering

Använd Azure AD-lösenordsskydd för att eliminera lösenord som är enkla att gissa från din miljö. Genom att eliminera dessa lösenord minskar du risken för angrepp från en lösenordsattack.

Mer specifikt hjälper Azure AD-lösenordsskydd dig att:

- Skydda organisationens konton i både Azure AD och Windows Server Active Directory (AD).
- Hindrar användarna från att använda lösenord i en lista med fler än 500 av de vanligaste lösenorden och över 1 miljon ersättningsvariationer av dessa lösenord.
- Administrera Azure AD-lösenordsskydd från en enda plats i Azure AD-portalen för både Azure AD och lokala Windows Server AD.

Mer information om Azure AD-lösenordsskydd finns [i Eliminera dåliga lösenord i din organisation.](../authentication/concept-password-ban-bad.md)

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny mall för princip för villkorsstyrd åtkomst för "alla gäster" som skapas när användningsvillkoren skapas

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Styrning

När du skapar användningsvillkoren skapas även en ny principmall för villkorlig åtkomst för "alla gäster" och "alla appar". Den här nya principmallen tillämpar den nyligen skapade tou:en, vilket effektiviserar skapande- och tvingandeprocessen för gäster.

Mer information finns i [Azure Active Directory Användningsvillkor funktion](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny mall för "anpassad" princip för villkorsstyrd åtkomst som skapades när användningsvillkor skapades

**Typ:** Ny **funktionskategori: Användningsvillkor** **produktfunktion:** styrning

När du skapar dina användningsvillkor skapas även en ny mall för "anpassad" princip för villkorlig åtkomst. Med den här nya principmallen kan du skapa villkoren och sedan omedelbart gå till bladet för att skapa principen för villkorlig åtkomst, utan att behöva navigera manuellt via portalen.

Mer information finns i [Azure Active Directory Användningsvillkor funktion](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Ny och omfattande vägledning om hur du distribuerar Azure AD Multi-Factor Authentication

**Typ:** Ny **funktionskategori:** Annan **produktfunktion:** Identity Security & Protection

Vi har släppt nya stegvisa anvisningar om hur du distribuerar Azure AD Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa MFA-distributionsguiden går du till [lagringsplatsen Identity Deployment Guides](./active-directory-deployment-plans.md) på GitHub. Om du vill ge feedback om distributionsguiderna använder du [formuläret Feedback om distributionsplan.](https://aka.ms/deploymentplanfeedback) Om du har frågor om distributionsguiderna kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD-delegerade apphanteringsroller finns i offentlig förhandsversion

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar:** Access Control

Administratörer kan nu delegera apphanteringsuppgifter utan att tilldela rollen Global administratör. De nya rollerna och funktionerna är:

- **Nya azure AD-standardadministratörsroller:**

    - **Programadministratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, inställningar för enkel inloggning, apptilldelningar och licensiering, appproxyinställningar och medgivande (förutom För Azure AD-resurser).

    - **Molnprogramadministratör.** Ger alla programadministratörsförmågor, förutom appproxy, eftersom den inte ger lokal åtkomst.

    - **Programutvecklare.** Ger möjlighet att skapa appregistreringar, även om **alternativet Tillåt användare att registrera appar** är inaktiverat.

- **Ägarskap (konfigurera registrering per app och app per företag, liknande processen för gruppägarskap:**

    - **Appregistreringsägare.** Ger möjlighet att hantera alla aspekter av registreringen av ägda appar, inklusive appmanifestet och att lägga till ytterligare ägare.

    - **Ägare av företagsapp.** Ger möjlighet att hantera många aspekter av ägda företagsappar, inklusive inställningar för enkel inloggning, apptilldelningar och medgivande (förutom för Azure AD-resurser).

Mer information om den offentliga förhandsversionen finns i [Azure AD-delegerade programhanteringsroller finns i offentlig förhandsversion!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blogg. Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>Ändringar i ExpressRoute-stöd

**Typ:** Planera för ändring **av tjänstkategori:** Autentiseringar (inloggningar) **Produktfunktion:** Plattform

Programvara som en tjänst, till exempel Azure Active Directory (Azure AD) är utformad för att fungera bäst genom att gå direkt via Internet, utan att expressroute eller andra privata VPN-tunnlar krävs. Därför slutar vi den **1 augusti 2018** att stödja ExpressRoute för Azure AD-tjänster med hjälp av azure offentlig peering och Azure-communities i Microsoft-peering. Alla tjänster som påverkas av den här ändringen kan märka att Azure AD-trafiken gradvis växlar från ExpressRoute till Internet.

Vi ändrar vår support, men vi vet också att det fortfarande finns situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentiseringstrafik. Därför kommer Azure AD att fortsätta att stödja IP-intervallbegränsningar per klient med hjälp av ExpressRoute och tjänster som redan finns på Microsoft-peering med communityn "Andra Office 365 Online-tjänster". Om dina tjänster påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder offentlig Peering i Azure.** Gå till Microsoft-peering och registrera dig för communityn för andra **Office 365 Online-tjänster (12076:5100).** Mer information om hur du flyttar från Azures offentliga peering till Microsoft-peering finns i artikeln Flytta en offentlig [peering till Microsoft-peering.](../../expressroute/how-to-move-peering.md)

- **Om du använder Microsoft-peering.** Registrera dig för **communityn för andra Office 365 Online-tjänster (12076:5100).** Mer information om routningskrav finns i avsnittet [Stöd för BGP-communities i](../../expressroute/expressroute-routing.md#bgp) artikeln ExpressRoute-routningskrav.

Om du måste fortsätta att använda dedikerade kretsar måste du prata med ditt Microsoft-kontoteam om hur du får behörighet att använda communityn för andra **Office 365 Online-tjänster (12076:5100).** MS Office-hanterade granskningsnämnden kontrollerar om du behöver dessa kretsar och ser till att du förstår de tekniska konsekvenserna av att behålla dem. Obehöriga prenumerationer som försöker skapa flödesfilter för Office 365 får ett felmeddelande.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API:er för administrativa scenarier för användningsutfall

**Typ:** Ny **funktionskategori: Användningsvillkor** **produktfunktion: Utvecklarupplevelse**

Vi har lagt till Microsoft Graph API:er för administration av användningsvillkor för Azure AD. Du kan skapa, uppdatera och ta bort användningsvillkoren.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till azure AD-slutpunkt för flera innehavare som identitetsprovider i Azure AD B2C

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Med anpassade principer kan du nu lägga till den gemensamma Azure AD-slutpunkten som en identitetsprovider i Azure AD B2C. På så sätt kan du ha en enda startpunkt för alla Azure AD-användare som loggar in i dina program. Mer information finns i Azure Active Directory B2C: Tillåt användare att logga in på en Azure AD-identitetsprovider för [flera innehavare med hjälp av anpassade principer.](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använd interna URL:er för att komma åt appar från Mina appar med vårt tillägg för inloggning och Azure AD-Programproxy

**Typ:** Ny **funktionskategori för tjänst:** Mina appar **produktfunktion:** enkel inloggning

Användare kan nu komma åt program via interna URL:er även utanför företagets nätverk med hjälp Mina appar secure sign-in extension for Azure AD (Tillägget för säker inloggning för Azure AD). Detta fungerar med alla program som du har publicerat med Azure AD Programproxy, i alla webbläsare som även har Åtkomstpanelen webbläsartillägget installerat. Funktionen för URL-omdirigering aktiveras automatiskt när en användare loggar in i tillägget. Tillägget kan laddas ned på [Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)och [Firefox.](https://go.microsoft.com/fwlink/?linkid=866366)

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Data i Europa för kunder i Europa

**Typ:** Ny **funktionskategori:** Annan **produktfunktion:** GoLocal

Kunder i Europa kräver att deras data stannar i Europa och inte replikeras utanför europeiska datacenter för att uppfylla sekretess och europeiska lagar. Den [här](./active-directory-data-storage-eu.md) artikeln innehåller detaljerad information om vilken identitetsinformation som kommer att lagras i Europa och innehåller även information om information som kommer att lagras utanför europeiska datacenter.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Ny användareablering av SaaS-appintegreringar – maj 2018

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för appetablering:** Integrering från tredje part

Med Azure AD kan du automatisera skapande, underhåll och borttagning av användaridentiteter i SaaS-program som Dropbox, Salesforce, ServiceNow med mera. För maj 2018 har vi lagt till stöd för användareablering för följande program i Azure AD-appgalleriet:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

En lista över alla program som stöder användareablering i Azure AD-galleriet finns i [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-åtkomstgranskningar av grupper och appåtkomst ger nu återkommande granskningar

**Typ:** Ny **funktionskategori: Åtkomstgranskningar** **Produktfunktion:** Styrning

Åtkomstgranskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomstgranskningar av gruppmedlemskap och programtilldelningar så att de automatiskt upprepas med jämna mellanrum, till exempel varje månad eller kvartal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitetsloggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Azure AD-aktivitetsloggar, som innehåller inloggningar och granskningsloggar, är nu tillgängliga via Microsoft Graph-API:et. Vi har exponerat två slutpunkter via Microsoft Graph API för att komma åt dessa loggar. Läs våra dokument [för programmeringsåtkomst](../reports-monitoring/concept-reporting-api.md) till Azure AD Reporting-API:er för att komma igång.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösningsupplevelsen och lämna en organisation

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C

**Just-in-time-inlösning:** När du delar en resurs med en gästanvändare med hjälp av B2B API behöver du inte skicka ett särskilt e-postmeddelande med inbjudan. I de flesta fall kan gästanvändaren komma åt resursen och kommer att gå igenom inlösningsupplevelsen just i tid. Ingen större påverkan på grund av missade e-postmeddelanden. Inga fler frågor till gästanvändarna "Klickade du på den inlösningslänken som systemet skickade dig?". Det innebär att när SPO använder inbjudningshanteraren kan molnbilagor ha samma kanoniska URL för alla användare – interna och externa – i alla inlösningstillstånd.

**Modern inlösningsupplevelse:** Ingen mer landningssida för inlösning av delad skärm. Användarna ser en modern medgivandeupplevelse med den inbjudande organisationens sekretesspolicy, precis som för appar från tredje part.

**Gästanvändare kan lämna organisationen:** När en användares relation till en organisation är över kan användaren själv lämna organisationen. Inga fler samtal till den inbjudande organisationens administratör om att "tas bort", utan att skapa supportärenden mer.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – maj 2018

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I maj 2018 lade vi till dessa 18 nya appar med federationsstöd i vårt appgalleri:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md), [Montage Online](../saas-apps/montageonline-tutorial.md), [まなびポケfyl,](../saas-apps/manabipocket-tutorial.md)OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nya stegvisa distributionsguider för Azure Active Directory

**Typ:** Ny **tjänstkategori:** Annan **produktfunktion:** Katalog

Nya stegvisa anvisningar för hur du distribuerar Azure Active Directory (Azure AD), inklusive lösenordsåterställning via självbetjäning (SSPR), enkel inloggning (SSO), villkorsstyrd åtkomst, appproxy, användareablering, Active Directory Federation Services (AD FS) (ADFS) till direktautentisering (PTA) och ADFS till synkronisering av lösenordshashar (PHS).

Om du vill visa distributionsguiderna går du till [lagringsplatsen Identitetsdistributionsguider](./active-directory-deployment-plans.md) på GitHub. Om du vill ge feedback om distributionsguiderna använder du [formuläret Feedback för distributionsplan.](https://aka.ms/deploymentplanfeedback) Om du har frågor om distributionsguiderna kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Sökning efter företagsprogram – Läs in fler appar

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar:** Enkel inloggning

Har du problem med att hitta dina program/tjänstens huvudnamn? Vi har lagt till möjligheten att läsa in fler program i listan över företagsprogram för alla program. Som standard visar vi 20 program. Du kan nu klicka på Läs **in fler för** att visa fler program.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maj-versionen av AADConnect innehåller en offentlig förhandsversion av integreringen med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya nya felsökningsverktyg.

**Typ:** **Funktionskategorin Tjänst har ändrats:** AD **Connect-produktfunktion:** Hantering av identitetslivscykel

Maj-versionen av AADConnect innehåller en offentlig förhandsversion av integreringen med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya nya felsökningsverktyg. Du hittar den här [versionen.](../hybrid/reference-connect-version-history.md)

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-åtkomstgranskningar: tillämpa automatiskt

**Typ:** **Funktionskategorin Tjänst har ändrats:** Åtkomstgranskningar **Produktfunktion:** Styrning

Åtkomstgranskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera att automatiskt tillämpa granskarens ändringar i gruppen eller appen när åtkomstgranskningen är klar. Administratören kan också ange vad som händer med användarens kontinuerliga åtkomst om granskare inte svarade, tar bort åtkomst, behåller åtkomst eller tar med systemrekommendationer.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-token kan inte längre returneras med hjälp av response_mode för nya appar.

**Typ:** **Funktionskategorin Tjänst har ändrats:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Appar som skapats den 25 april 2018 eller senare kommer inte längre att kunna **begära** en id_token med hjälp **av** response_mode.  Detta ger Azure AD ett direkt samband med OIDC-specifikationerna och hjälper till att minska attackytan för dina appar.  Appar som skapats före den 25 april 2018 blockeras inte från att använda **fråge-response_mode** med en response_type av **id_token**.  Felet som returneras när du begär en id_token från Azure AD är **AADSTS70007: "query" är** inte ett värde för "response_mode" som stöds när du begär en token .

**Fragmentet** **och form_post** response_modes fortfarande att fungera – när du skapar nya programobjekt (till exempel för App Proxy-användning) ska du se till att någon av dessa response_modes innan de skapar ett nytt program.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C åtkomsttoken är ga

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Nu kan du komma åt webb-API:er som skyddas Azure AD B2C med hjälp av åtkomsttoken. Funktionen flyttas från den offentliga förhandsversionen till den allmänt allmänt använda funktionen. Gränssnittet för att konfigurera Azure AD B2C program och webb-API:er har förbättrats och andra mindre förbättringar har gjorts.

Mer information finns i [Azure AD B2C: Begär åtkomsttoken.](../../active-directory-b2c/access-tokens.md)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa konfiguration av enkel inloggning för SAML-baserade program

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion för **företagsappar:** enkel inloggning

När du konfigurerar SAML-baserade SSO-program kan du testa integreringen på konfigurationssidan. Om du stöter på ett fel under inloggningen kan du ange felet i testupplevelsen och Azure AD ger dig lösningssteg för att lösa det specifika problemet.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/view-applications-portal.md)
- [Så här felsöker du SAML-baserad enkel inloggning till program i Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Användningsvillkoren för Azure AD har nu rapportering per användare

**Typ:** Ny **tjänstkategori: Användningsvillkor** **produktfunktion:** Efterlevnad

Administratörer kan nu välja en viss toU och se alla användare som har samtyckt till denna toU och vilket datum/tid den ägde rum.

Mer information finns i [användningsvillkoren för Azure AD.](../conditional-access/terms-of-use.md)

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riskabel IP för AD FS extranätslåsningsskydd

**Typ:** Ny **funktionskategori:** Annan **produktfunktion: Övervakning** & rapportering

Connect Health stöder nu möjligheten att identifiera IP-adresser som överskrider ett tröskelvärde för misslyckade U/P-inloggningar per timme eller dagligen. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antalet misslyckade inloggningar som genereras per timme/dag med anpassningsbart tröskelvärde.
- E-postbaserade aviseringar som visar när en specifik IP-adress har överskridit tröskelvärdet för misslyckade U/P-inloggningar per timme/dag.
- Ett nedladdningsalternativ för att göra en detaljerad analys av data

Mer information finns i [Riskfyllda IP-rapporter.](../hybrid/how-to-connect-health-adfs.md)

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel appkonfiguration med metadatafil eller URL

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar:** Enkel inloggning

På sidan Företagsprogram kan administratörer ladda upp en SAML-metadatafil för att konfigurera SAML-baserad inloggning för Azure AD-galleriet och icke-galleriprogram.

Dessutom kan du använda URL:en för Azure AD-programfederationens metadata för att konfigurera enkel inloggning med målprogrammet.

Mer information finns i [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Användningsvillkor nu allmänt tillgänglig

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Efterlevnad


Användningsvillkoren för Azure AD har flyttats från offentlig förhandsversion till allmänt tillgänglig.

Mer information finns i [användningsvillkoren för Azure AD-funktionen](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C


Nu kan du ange vilka partnerorganisationer som du vill dela och samarbeta med i Azure AD B2B Collaboration. Om du vill göra detta kan du välja att skapa en lista över specifika tillåt- eller neka-domäner. När en domän blockeras med hjälp av dessa funktioner kan anställda inte längre skicka inbjudningar till personer i den domänen.

På så sätt kan du kontrollera åtkomsten till dina resurser, samtidigt som du får en smidig upplevelse för godkända användare.

Den B2B Collaboration funktionen är tillgänglig för alla Azure Active Directory-kunder och kan användas tillsammans med Azure AD Premium-funktioner som villkorsstyrd åtkomst och identitetsskydd för mer detaljerad kontroll över när och hur externa företagsanvändare loggar in och får åtkomst.

Mer information finns i Tillåta [eller blockera inbjudningar till B2B-användare från specifika organisationer.](../external-identities/allow-deny-list.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I april 2018 lade vi till dessa 13 nya appar med federationsstöd i vårt appgalleri:

Criterion HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), [Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), Cisco [Cloud](../saas-apps/ciscocloud-tutorial.md), Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Ge B2B-användare i Azure AD åtkomst till dina lokala program (offentlig förhandsversion)

**Typ:** Ny **funktionskategori:** **B2B-produktfunktion:** B2B/B2C

Som en organisation som använder B2B-samarbetsfunktioner för Azure Active Directory (Azure AD) för att bjuda in gästanvändare från partnerorganisationer till din Azure AD kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller Integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [Ge B2B-användare i Azure AD åtkomst till dina lokala program.](../external-identities/hybrid-cloud-to-on-premises.md)

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Hämta självstudier för enkel inloggning från Azure Marketplace

**Typ:** **Funktionskategorin Har ändrats:** **Annan produktfunktion:** Integrering från tredje part

Om ett program som visas i listan [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) saml-baserad enkel  inloggning, får du integreringsskursen som är associerad med programmet om du klickar på Hämta nu.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för automatisk användareablering i Azure AD till SaaS-program

**Typ:** **Funktionskategorin Har ändrats:** Produktfunktion **för appetablering:** Integrering från tredje part

Tidigare kunde kunder som använder Azure Active Directory-anslutningsappar för användareetablering för SaaS-program (till exempel Salesforce, ServiceNow och Box) uppleva långsamma prestanda om deras Azure AD-klienter innehöll över 100 000 kombinerade användare och grupper och de använde användar- och grupptilldelningar för att avgöra vilka användare som ska etableras.

Den 2 april 2018 distribuerades betydande prestandaförbättringar till Azure AD-etableringstjänsten som avsevärt minskar den tid som krävs för att utföra inledande synkroniseringar mellan Azure Active Directory och SaaS-målprogram.

Därför slutförs nu många kunder som hade inledande synkroniseringar till appar som tog många dagar eller aldrig slutfördes, inom några minuter eller timmar.

Mer information finns i [Vad händer under etableringen?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Lösenordsåterställning via självbetjäning från Windows 10 för Azure AD-hybridanslutningar

**Typ:** **Funktionskategorin Tjänst har ändrats:** Produktfunktion för lösenordsåterställning **via självbetjäning:** Användarautentisering

Vi har uppdaterat SSPR Windows 10 funktionen så att den omfattar stöd för datorer som är Hybrid Azure AD-ansluten. Den här funktionen är tillgänglig i Windows 10 RS4 tillåter användare att återställa sina lösenord från låsskärmen på en Windows 10 dator. Användare som är aktiverade och registrerade för lösenordsåterställning via självbetjäning kan använda den här funktionen.

Mer information finns i [Azure AD-lösenordsåterställning från inloggningsskärmen.](../authentication/howto-sspr-windows.md)

---

## <a name="march-2018"></a>Mars 2018

### <a name="certificate-expire-notification"></a>Meddelande om att certifikatet upphör att gälla

**Typ:** Fast **tjänstkategori:** Enterprise Apps **Product capability:** SSO

Azure AD skickar ett meddelande när ett certifikat för ett galleri- eller icke-galleriprogram snart upphör att gälla.

Vissa användare fick inga meddelanden för företagsprogram som konfigurerats för SAML-baserad enkel inloggning. Det här problemet har lösts. Azure AD skickar ett meddelande om certifikat som går ut om 7, 30 och 60 dagar. Du kan se den här händelsen i granskningsloggarna.

Mer information finns i:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- och GitHub-identitetsproviders i Azure AD B2C

**Typ:** Ny **funktionskategori:** B2C – Produktfunktion för **konsumentidentitetshantering:** B2B/B2C

Nu kan du lägga till Twitter eller GitHub som identitetsprovider i Azure AD B2C. Twitter flyttar från offentlig förhandsversion till ALLMÄN ALLMÄNT. GitHub släpps i offentlig förhandsversion.

Mer information finns i Vad [är Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webbläsaråtkomst med Intune Managed Browser med Azure AD-programbaserad villkorlig åtkomst för iOS och Android

**Typ:** Ny **funktionskategori: Produktfunktion** för **villkorsstyrd åtkomst:** Identity Security & Protection

**Nu i offentlig förhandsversion!**

**Intune Managed Browser enkel inloggning:** Dina anställda kan använda enkel inloggning mellan interna klienter (t.ex. Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Intune Managed Browser för villkorlig åtkomst:** Du kan nu kräva att anställda använder Intune Managed Browser med hjälp av programbaserade principer för villkorlig åtkomst.

Läs mer om detta i [vårt blogginlägg](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns i:

- [Konfigurera programbaserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

- [Konfigurera principer för hanterade webbläsare](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App Proxy-cmdlets i PowerShell GA-modulen

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Access Control

Stöd för Programproxy-cmdlets finns nu i PowerShell GA-modulen! Detta kräver att du håller dig uppdaterad om PowerShell-moduler – om du blir mer än ett år efter kan vissa cmdlets sluta fungera.

Mer information finns i [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Inbyggda Office 365-klienter stöds av sömlös enkel inloggning med hjälp av ett icke-interaktivt protokoll

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Användare som använder inbyggda Office 365-klienter (version 16.0.8730.xxxx och senare) får en tyst inloggning med sömlös enkel inloggning. Det här stödet tillhandahålls av tillägget av ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns i [Hur fungerar inloggning på en inbyggd klient med sömlös enkel inloggning?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användare får en tyst inloggning med sömlös enkel inloggning om ett program skickar inloggningsförfrågningar till Azure AD:s klientslutpunkter

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Användare får en tyst inloggning med sömlös enkel inloggning om ett program (till exempel ) skickar inloggningsbegäranden till Azure AD:s klientslutpunkter – det vill säga eller – i stället för `https://contoso.sharepoint.com` `https://login.microsoftonline.com/contoso.com/<..>` Azure `https://login.microsoftonline.com/<tenant_ID>/<..>` AD:s gemensamma slutpunkt ( `https://login.microsoftonline.com/common/<...>` ).

Mer information finns i [Azure Active Directory sömlös enkel inloggning.](../hybrid/how-to-connect-sso.md)

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Behöver bara lägga till en Azure AD-URL, i stället för två URL:er tidigare, till användarnas inställningar för zonen Intranät för att distribuera sömlös enkel inloggning

**Typ:** Ny **funktionskategori för tjänsten:** Produktfunktion för autentiseringar **(inloggningar):** Användarautentisering

Om du vill distribuera sömlös enkel inloggning till dina användare behöver du bara lägga till en Azure AD-URL till användarnas inställningar för zonen Intranät med hjälp av grupprincip i Active Directory: `https://autologon.microsoftazuread-sso.com` . Tidigare krävdes det att kunder skulle lägga till två URL:er.

Mer information finns i [Azure Active Directory sömlös enkel inloggning.](../hybrid/how-to-connect-sso.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion **för företagsappar:** Integrering från tredje part

I mars 2018 lade vi till dessa 15 nya appar med federationsstöd i vårt appgalleri:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant av FirstAgenda,LegiOne , Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md). [](../saas-apps/yardione-tutorial.md)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i [List your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgängligt

**Typ:** Ny **funktionskategori: Privileged Identity Management** **produktfunktion:** Privileged Identity Management

Om du använder Azure AD Privileged Identity Management för katalogroller kan du nu använda PIM:s tidsbundna åtkomst- och tilldelningsfunktioner för Azure-resursroller som prenumerationer, resursgrupper, Virtual Machines och andra resurser som stöds av Azure Resource Manager. Framtvinga Multi-Factor Authentication när du aktiverar just-in-time-roller och schemalägger aktiveringar tillsammans med godkända ändringsfönster. Dessutom lägger den här versionen till förbättringar som inte är tillgängliga under den offentliga förhandsversionen, inklusive ett uppdaterat användargränssnitt, arbetsflöden för godkännande och möjligheten att utöka roller som snart upphör att gälla och förnya utgångna roller.

Mer information finns i [PIM för Azure-resurser (förhandsversion)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägga till valfria anspråk i dina apptoken (offentlig förhandsversion)

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Din Azure AD-app kan nu begära anpassade eller valfria anspråk i JWT- eller SAML-token.  Det här är anspråk om användaren eller klientorganisationen som inte ingår som standard i token på grund av storleks- eller tillämplighetsbegränsningar.  Detta är för närvarande i offentlig förhandsversion för Azure AD-appar på v1.0- och v2.0-slutpunkterna.  I dokumentationen finns information om vilka anspråk som kan läggas till och hur du redigerar ditt programmanifest för att begära dem.

Mer information finns i [Valfria anspråk i Azure AD.](../develop/active-directory-optional-claims.md)

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny **funktionskategori för tjänsten:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Azure AD-dokument har uppdaterats för att notera stödet för PKCE, vilket möjliggör säkrare kommunikation under beviljandeflödet för OAuth 2.0-auktoriseringskod.  Både S256 och code_challenges stöds på v1.0- och v2.0-slutpunkterna.

Mer information finns i Begär [en auktoriseringskod.](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Stöd för etablering av alla användarattributvärden som är tillgängliga i Workday Get_Workers API

**Typ:** Ny **funktionskategori:** Produktfunktion för **appetablering: Integrering** från tredje part

Den offentliga förhandsversionen av inkommande etablering från Workday till Active Directory och Azure AD stöder nu möjligheten att extrahera och etablera alla attributvärden som är tillgängliga i Workday Get_Workers-API:et. Detta lägger till stöd för hundratals ytterligare standardattribut och anpassade attribut utöver de som levereras med den ursprungliga versionen av workday-anslutningsappen för inkommande etablering.

Mer information finns i: [Anpassa listan över Workday-användarattribut](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändra gruppmedlemskap från dynamisk till statisk och vice versa

**Typ:** Ny **funktionskategori: Produktfunktion** för **grupphantering:** Samarbete

Det går att ändra hur medlemskap hanteras i en grupp. Detta är användbart när du vill behålla samma gruppnamn och ID i systemet, så alla befintliga referenser till gruppen är fortfarande giltiga. Om du skapar en ny grupp måste du uppdatera dessa referenser.
Vi har uppdaterat Azure AD-administrationscentret för att stödja den här funktionen. Nu kan kunder konvertera befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. Befintliga PowerShell-cmdlets är också fortfarande tillgängliga.

Mer information finns i [Regler för dynamiskt medlemskap för grupper i Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat ut logga ut-beteende med sömlös enkel inloggning

**Typ:** Kategori för **funktionstjänsten har ändrats:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Tidigare, även om användare uttryckligen loggade ut från ett program som skyddas av Azure AD, skulle de automatiskt loggas in med sömlös enkel inloggning om de försöker komma åt ett Azure AD-program igen inom sitt företagsnät från sina domän anslutna enheter. Med den här ändringen stöds ut logga ut.  På så sätt kan användarna välja samma eller ett annat Azure AD-konto att logga in med igen, i stället för att loggas in automatiskt med sömlös enkel inloggning.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Programproxy Connector version 1.5.402.0 har släppts

**Typ:** **Funktionskategorin Har ändrats:** Produktfunktion **för appproxy:** Identity Security & Protection

Den här versionen av anslutningsappen distribueras gradvis till november. Den här nya versionen av anslutningsappen innehåller följande ändringar:

- Anslutningsappen anger nu cookies på domännivå i stället för underdomännivå. Detta säkerställer en smidigare SSO-upplevelse och undviker redundanta autentiseringsuppmaning.
- Stöd för chunked-kodningsbegäranden
- Förbättrad hälsoövervakning av anslutningsappar
- Flera felkorrigeringar och stabilitetsförbättringar

Mer information finns i Förstå [Azure AD Programproxy-anslutningsappar.](../manage-apps/application-proxy-connectors.md)

---

## <a name="february-2018"></a>Februari 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Planera för ändring **av tjänstkategori:** Kataloghantering **produktfunktion:** Katalog

Navigeringsupplevelsen för att hantera användare och grupper har effektiviserats. Nu kan du navigera från katalogöversikten direkt till listan över alla användare, med enklare åtkomst till listan över borttagna användare. Du kan också navigera från katalogöversikten direkt till listan över alla grupper, med enklare åtkomst till grupphanteringsinställningar. Från katalogöversiktssidan kan du även söka efter en användare, grupp, ett företagsprogram eller en appregistrering.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggningar och granskningsrapporter i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet)

**Typ:** Ny **funktionskategori: Azure Stack** **produktfunktion: Övervakning** & rapportering

Azure AD-aktivitetsloggrapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet-instanser (Azure China 21Vianet). Följande loggar ingår:

- **Aktivitetsloggar för inloggningar**  – Innehåller alla inloggningsloggar som är associerade med din klientorganisation.

- **Granskningsloggar för lösenord via** självbetjäning – Innehåller alla SSPR-granskningsloggar.

- **Granskningsloggar för** kataloghantering – Innehåller alla kataloghanteringsrelaterade granskningsloggar som användarhantering, apphantering med mera.

Med dessa loggar kan du få insikter om hur din miljö klarar sig. Med dessa data kan du:

- Fastställ hur dina appar och tjänster används av användarna.

- Felsöka problem som hindrar användarna från att få sitt arbete gjort.

Mer information om hur du använder dessa rapporter finns i [Azure Active Directory rapportering](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använd rollen Rapportläsare (icke-administratörsroll) för att visa Azure AD-aktivitetsrapporter

**Typ:** Ny **funktionskategori: Rapportera** **produktfunktion: Övervakning** & rapportering

Som en del av kundernas feedback för att ge icke-administratörsroller åtkomst till Azure AD-aktivitetsloggar har vi aktiverat möjligheten för användare som har rollen "Rapportläsare" att få åtkomst till inloggningar och granskningsaktiviteter inom Azure Portal samt att använda Microsoft Graph-API:et.

Mer information om hur du använder dessa rapporter finns i [Azure Active Directory rapportering](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-anspråk tillgängligt som användarattribut och användaridentifierare

**Typ:** Ny **funktionstjänstkategori:** Produktfunktion för **företagsappar:** enkel inloggning

Du kan konfigurera **EmployeeID** som användaridentifierare och användarattribut för medlemsanvändare och B2B-gäster i SAML-baserade inloggningsprogram från användargränssnittet för företagsprogram.

Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad programhantering med jokertecken i Azure AD Programproxy

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Användarautentisering

För att göra programdistributionen enklare och minska det administrativa arbetet stöder vi nu möjligheten att publicera program med jokertecken. Om du vill publicera ett jokerteckenprogram kan du följa standardflödet för programpublicering, men använda ett jokertecken i de interna och externa URL:erna.

Mer information finns i [Jokerteckenprogram i Azure Active Directory programproxy](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nya cmdlets som stöder konfiguration av Programproxy

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Plattform

Den senaste versionen av AzureAD PowerShell Preview-modulen innehåller nya cmdlets som gör det möjligt för kunder att konfigurera Programproxy-program med Hjälp av PowerShell.

De nya cmdletarna är:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nya cmdlets för att stödja konfiguration av grupper

**Typ:** Ny **funktionskategori: Produktfunktion** för **appproxy:** Plattform

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdlets var tidigare tillgängliga i modulen AzureADPreview och har nu lagts till i AzureAD-modulen

De grupp-cmdlets som nu är tillgängliga för allmän tillgänglighet är:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** Ny **tjänstkategori: AD Sync** **produktfunktion:** Plattform

Azure AD Connect är det bästa verktyget för att synkronisera data mellan Azure AD och lokala datakällor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>Den här versionen introducerar ändringar av schema- och synkroniseringsregeln. Synkroniseringstjänsten Azure AD Connect en fullständig import och fullständig synkronisering efter en uppgradering. Information om hur du ändrar det här beteendet finns i [Skjuta upp fullständig synkronisering efter uppgraderingen.](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)

Den här versionen har följande uppdateringar och ändringar:

**Problem har åtgärdats**

- Korrigera tidsfönster för bakgrundsaktiviteter för sidan Partitionsfiltrering när du växlar till nästa sida.

- En bugg som orsakade åtkomstfel under den anpassade ConfigDB-åtgärden har åtgärdats.

- En bugg för att återställa från sql-anslutningens tidsgräns har åtgärdats.

- En bugg har åtgärdats där certifikat med SAN-jokertecken misslyckas med förhandskontroll.

- En bugg som orsakar en miiserver.exe vid export av Azure AD-anslutningsappen har åtgärdats.

- En bugg där ett felaktigt lösenordsförsök loggade in på domänkontrollanten vid körning har åtgärdats som gjorde att Azure AD-anslutningsguiden ändrade konfigurationen

**Nya funktioner och förbättringar**

- Programtelemetri – Administratörer kan växla den här klassen av data på/av.

- Azure AD Health-data – Administratörer måste besöka hälsoportalen för att kontrollera sina hälsoinställningar. När tjänstprincipen har ändrats läser och framtvingar agenterna den.

- Konfigurationsåtgärder för tillbakaskrivning av enheter och en förloppsfält för sidinitiering har lagts till.

- Förbättrad allmän diagnostik med HTML-rapport och fullständig datainsamling i en ZIP-Text-/HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och ytterligare telemetri har lagts till för att säkerställa att serverns hälsa kan fastställas.

- Begränsa behörigheter som är tillgängliga för privilegierade konton på AD Connector-kontot. För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när du har skapat MSOL-kontot. Ändringarna påverkar expressinstallationer och anpassade installationer med kontot Skapa automatiskt.

- Installationsprogrammet har ändrats så att det inte kräver SA-behörighet vid ren installation av AADConnect.

- Nytt verktyg för att felsöka synkroniseringsproblem för ett specifikt objekt. För närvarande söker -verktyget efter följande saker:

    - UserPrincipalName matchar inte mellan synkroniserade användarobjekt och användarkontot i Azure AD-klientorganisationen.

    - Om objektet filtreras från synkronisering på grund av domänfiltrering

    - Om objektet filtreras från synkronisering på grund av organisationsenhetsfiltrering

- Nytt verktyg för att synkronisera den aktuella lösenordshashar som lagras i lokal Active Directory för ett specifikt användarkonto. Verktyget kräver ingen lösenordsändring.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune-appskydd principer som har lagts till för användning med programbaserad villkorlig åtkomst i Azure AD

**Typ:** **Tjänstkategorin Har ändrats:** **Produktfunktion för villkorsstyrd åtkomst:** Identity Security & Protection

Vi har lagt till fler program som stöder programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av dessa godkända klientappar.

Följande program kommer att läggas till i slutet av februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Mer information finns i:

- [Godkänt krav för klientapp](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Appbaserad villkorlig åtkomst i Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdatering av mobilupplevelsen

**Typ:** **Tjänstkategorin Har ändrats:** Användningsvillkor **produktfunktion:** Efterlevnad

När användningsvillkoren visas kan du nu klicka på Har du **problem med att visa? Klicka här.** Om du klickar på den här länken öppnas användningsvillkoren på enheten. Oavsett teckenstorlek i dokumentet eller enhetens skärmstorlek kan du zooma och läsa dokumentet efter behov.

---

## <a name="january-2018"></a>Januari 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny **funktionskategori:** Produktkapacitet för **Företagsappar: Integrering** från tredje part

I januari 2018 lades följande nya appar med federationsstöd till i appgalleriet:

[IBM OpenPages,](../saas-apps/ibmopenpages-tutorial.md) [OneTrust Privacy Management Software,](../saas-apps/onetrust-tutorial.md) [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk Federated Directory och [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Logga in med ytterligare risk upptäckt

**Typ:** Ny **funktionskategori för tjänst:** **Produktfunktion för Identitetsskydd:** Identity Security & Protection

De insikter du får för en identifierad riskidentifiering är knutna till din Azure AD-prenumeration. Med Azure AD Premium P2 får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte omfattas av din licens som riskidentifiering inloggning med ytterligare risk har identifierats.

Mer information finns i [Azure Active Directory riskidentifiering.](../identity-protection/overview-identity-protection.md)

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomstpaneler

**Typ:** Ny **funktionskategori: Mina appar** **produktfunktion:** enkel inloggning

Nu kan du bättre hantera hur Office 365-program visas på användarens åtkomstpaneler via en ny användarinställning. Det här alternativet är användbart för att minska antalet appar i en användares åtkomstpaneler om du föredrar att bara visa Office-appar i Office-portalen. Inställningen finns i  Användarinställningar och är märkt Användare kan bara se **Office 365-appar i Office 365-portalen.**

Mer information finns i [Dölj ett program från användarupplevelsen i Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning i appar som aktiverats för lösenords-SSO direkt från appens URL

**Typ:** Ny **funktionskategori för tjänst:** Mina appar **produktfunktion:** enkel inloggning

Det Mina appar webbläsartillägget är nu tillgängligt via ett praktiskt verktyg som ger Mina appar enkel inloggning som en genväg i webbläsaren. Efter installationen ser användarna en våffelikon i webbläsaren som ger snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjlighet att logga in direkt i appar som baseras på lösenords-SSO från appens inloggningssida
- Starta en app med snabbsökningsfunktionen
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.

Mer information finns i [Mina appar secure sign-in extension (Tillägg för säker inloggning).](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administrationsupplevelsen i den klassiska Azure-portalen har dragits tillbaka

**Typ:** Inaktuell **tjänstkategori:** Azure **AD-produktfunktion:** Katalog

Från och med den 8 januari 2018 har Azure AD-administrationsupplevelsen i den klassiska Azure-portalen dragits tillbaka. Detta skedde i samband med tillbakagången av den klassiska Azure-portalen. I framtiden bör du använda [Azure AD-administrationscentret](https://aad.portal.azure.com) för all portalbaserad administration av Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor-webbportalen har dragits tillbaka

**Typ:** Inaktuell **tjänstkategori:** Azure **AD-produktfunktion:** Katalog

Från och med den 8 januari 2018 har Webbportalen PhoneFactor dragits tillbaka. Den här portalen användes för administration av MFA-servern, men dessa funktioner har flyttats till Azure Portal på portal.azure.com.

MFA-konfigurationen finns på: **Azure Active Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Inaktuella Azure AD-rapporter

**Typ:** Inaktuell **tjänstkategori: Rapportera** **produktfunktion: Hantering** av identitetslivscykel


Med den allmänna tillgängligheten för den nya Azure Active Directory-administrationskonsolen och nya API:er som nu är tillgängliga för både aktivitetsrapporter och säkerhetsrapporter har rapport-API:erna under slutpunkten "/reports" dragits tillbaka från och med den 31 december 2017.

**Vad är tillgängligt?**

Som en del av övergången till den nya administratörskonsolen har vi gjort två nya API:er tillgängliga för att hämta Azure AD-aktivitetsloggar. Den nya uppsättningen API:er ger bättre filtrerings- och sorteringsfunktioner utöver bättre gransknings- och inloggningsaktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporterna kan nu nås via API:et för riskidentifiering i Identity Protection i Microsoft Graph.

Mer information finns i:

- [Kom igång med API:Azure Active Directory reporting](../reports-monitoring/concept-reporting-api.md)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Användningsvillkor i Åtkomstpanelen

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Efterlevnad

Nu kan du gå till Åtkomstpanelen och visa användningsvillkoren som du godkände tidigare.

Följ de här stegen:

1. Gå till [MyApps-portalen](https://myapps.microsoft.com)och logga in.

2. I det övre högra hörnet väljer du ditt namn och sedan **Profil** i listan.

3. I din **profil** väljer **du Granska användningsvillkoren.**

4. Nu kan du granska användningsvillkoren som du har godkänt.

Mer information finns i [användningsvillkoren för Azure AD (förhandsversion).](../conditional-access/terms-of-use.md)

---

### <a name="new-azure-ad-sign-in-experience"></a>Ny Azure AD-inloggningsupplevelse

**Typ:** Ny **funktionskategori:** Azure **AD-produktfunktion:** Användarautentisering

Api:erna för Azure AD Microsoft-konto identitetssystem har gjorts om så att de får ett konsekvent utseende. Dessutom samlar Azure AD-inloggningssidan in användarnamnet först, följt av autentiseringsnamnet på en andra skärm.

Mer information finns i [Den nya Azure AD-inloggningen är nu i offentlig förhandsversion.](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggningsuppmaning: En ny "håll mig inloggad"-miljö för Azure AD-inloggning

**Typ:** Ny **funktionskategori:** Azure **AD-produktfunktion:** Användarautentisering

Kryssrutan **Håll mig inloggad på** inloggningssidan för Azure AD ersattes med en ny uppmaning som visas när du har autentiserat.

Om du svarar **Ja på** den här frågan ger tjänsten dig en beständig uppdateringstoken. Det här beteendet är detsamma som när du markerade kryssrutan Håll mig **inloggad** i den gamla upplevelsen. För federerade klienter visas den här prompten när du har autentiserat med den federerade tjänsten.

Mer information finns i [Färre inloggningsuppmaning: Den nya "håll mig inloggad"-upplevelsen för Azure AD är i förhandsversion.](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva att användningsvillkoren utökas innan du godkänner

**Typ:** Ny **tjänstkategori: Användningsvillkor** **produktfunktion:** Efterlevnad

Ett alternativ för administratörer kräver att användarna utökar användningsvillkoren innan de godkänner villkoren.

Välj **antingen På** **eller Av** för att kräva att användarna utökar användningsvillkoren. Inställningen **På** kräver att användarna visar användningsvillkoren innan de godkänner dem.

Mer information finns i [användningsvillkoren för Azure AD (förhandsversion)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Omfångsaktivering för berättigade rolltilldelningar

**Typ:** Ny **tjänstkategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Du kan använda begränsad aktivering för att aktivera berättigade Tilldelningar av Azure-resursroller med mindre självständigt ursprung än de ursprungliga standardinställningarna för tilldelning. Ett exempel är om du är tilldelad som ägare till en prenumeration i din klientorganisation. Med begränsad aktivering kan du aktivera ägarrollen för upp till fem resurser som ingår i prenumerationen (till exempel resursgrupper och virtuella datorer). Omfångsomfånget för aktiveringen kan minska risken för att oönskade ändringar av viktiga Azure-resurser körs.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i Azure AD-appgalleriet

**Typ:** Ny **funktionskategori för tjänst:** **Produktfunktion för företagsappar:** Integrering från tredje part

I december 2017 lade vi till dessa nya appar med federationsstöd i vårt appgalleri:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MBUCK](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO for Bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR,Zenegy Azure AD Integration.

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du listar ditt program i Azure AD-appgalleriet finns i List [your application in the Azure Active Directory application gallery](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännandearbetsflöden för Azure AD-katalogroller

**Typ:** **Funktionskategorin Tjänst har ändrats:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Arbetsflödet för godkännande för Azure AD-katalogroller är allmänt tillgängligt.

Med arbetsflödet för godkännande kan privilegierade rolladministratörer kräva medlemmar med rätt roll för att begära rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan delegeras godkännandeansvar. Berättigade rollmedlemmar får meddelanden när godkännandet är klart och deras roll är aktiv.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Skype för företag-support

**Typ:** Tjänstkategori **för ändrade funktioner:** Autentiseringar (inloggningar) **Produktfunktion:** Användarautentisering

Direktautentisering stöder nu användarin inloggningar till Skype för företag-klientprogram som stöder modern autentisering, vilket innefattar online- och hybrid-topologier.

Mer information finns i [Topologier för Skype för företag som stöds med modern autentisering.](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar av Azure AD Privileged Identity Management för Azure RBAC (förhandsversion)

**Typ:** **Tjänstkategorin Ändrades:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Med den offentliga förhandsversionen av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du nu:

* Använd Just Enough Administration.
* Kräv godkännande för att aktivera resursroller.
* Schemalägg en framtida aktivering av en roll som kräver godkännande för både Azure AD- och Azure-roller.

Mer information finns i Privileged Identity Management [för Azure-resurser (förhandsversion).](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Access Control tjänst pensioneras

**Typ:** Planera för ändring **av tjänstkategori:** Access Control **produktfunktion:** Access Control tjänst

Azure Active Directory Access Control (även kallat Access Control service) dras tillbaka i slutet av 2018. Mer information som innehåller ett detaljerat schema och vägledning för migrering på hög nivå kommer att tillhandahållas under de kommande veckorna. Du kan lämna kommentarer på den här sidan med frågor om Access Control tjänsten så svarar en gruppmedlem på dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomsten till Intune Managed Browser

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Du kan begränsa webbläsaråtkomsten till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som en godkänd app.

Nu kan du konfigurera följande villkor för programbaserad villkorlig åtkomst:

**Klientappar:** Webbläsare

**Vad är effekten av ändringen?**

I dag blockeras åtkomsten när du använder det här villkoret. När förhandsversionen är tillgänglig kräver all åtkomst att det hanterade webbläsarprogrammet används.

Leta efter den här funktionen och mer information i kommande bloggar och viktig information.

Mer information finns i [Villkorlig åtkomst i Azure AD.](../conditional-access/overview.md)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för appbaserad villkorsstyrd åtkomst i Azure AD

**Typ:** Planera för ändring **av tjänstkategori:** Produktfunktion **för villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Följande appar finns i listan över godkända [klientappar:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns i:

- [Krav för godkänd klientapp](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Appbaserad villkorlig åtkomst i Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkorsstöd för flera språk

**Typ:** Ny **funktionskategori för tjänst:** Användningsvillkor **produktfunktion:** Efterlevnad

Administratörer kan nu skapa nya användningsvillkor som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med ett motsvarande språk. Användarna ser PDF-filen med matchande språk baserat på deras preferenser. Om det inte finns någon matchning visas standardspråket.

---

### <a name="real-time-password-writeback-client-status"></a>Klientstatus för tillbakaskrivning av lösenord i realtid

**Typ:** Ny **funktionskategori: Produktfunktion** för lösenordsåterställning via **självbetjäning:** Användarautentisering

Nu kan du granska statusen för din lokala klient för tillbakaskrivning av lösenord. Det här alternativet är tillgängligt **i avsnittet Lokal integrering** på sidan Återställning [av](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lösenord.

Om det uppstår problem med anslutningen till den lokala tillbakaskrivningsklienten visas ett felmeddelande som ger dig:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivningsklient.
- En länk till dokumentationen som hjälper dig att lösa problemet.

Mer information finns i [lokal integrering.](../authentication/concept-sspr-howitworks.md#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Appbaserad villkorlig åtkomst i Azure AD

**Typ:** Ny **funktionskategori: Azure** **AD-produktfunktion:** Identitetssäkerhet och skydd

Nu kan du begränsa åtkomsten till Office 365 [](../conditional-access/concept-conditional-access-conditions.md#client-apps) och andra Azure [AD-anslutna](../conditional-access/app-based-conditional-access.md)molnappar till godkända klientappar som stöder Intunes appskyddsprinciper med hjälp av appbaserad villkorsstyrd åtkomst i Azure AD. Intunes appskyddsprinciper används för att konfigurera och skydda företagsdata i dessa klientprogram.

Genom att kombinera [appbaserade med](../conditional-access/app-based-conditional-access.md) [enhetsbaserade principer](../conditional-access/require-managed-devices.md) för villkorsstyrd åtkomst får du flexibiliteten att skydda data för personliga enheter och företagsenheter.

Följande villkor och kontroller är nu tillgängliga för användning med appbaserad villkorlig åtkomst:

**Plattformsvillkor som stöds**

- iOS
- Android

**Villkor för klientappar**

- Mobilappar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp

Mer information finns i [Appbaserad villkorlig åtkomst i Azure AD.](../conditional-access/app-based-conditional-access.md)

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure Portal

**Typ:** Ny **funktionskategori: Enhetsregistrering** och hantering **Produktfunktion: Identitetssäkerhet** och skydd

Nu kan du hitta alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteter på ett och samma ställe. Det finns en ny administrationsupplevelse för att hantera alla enhetsidentiteter och inställningar i Azure Portal. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, inklusive azure AD-anslutna hybridenheter.
- Hitta BitLocker-nycklar för dina Azure AD-anslutna enheter, hantera din enhet med Intune med mera.
- Hantera enhetsrelaterade inställningar i Azure AD.

Mer information finns i [Hantera enheter med hjälp av Azure Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som enhetsplattform för villkorsstyrd åtkomst i Azure AD

**Typ:** Ny **funktionskategori: Produktfunktion** för **villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Nu kan du inkludera (eller exkludera) macOS som ett enhetsplattformsvillkor i din azure AD-princip för villkorsstyrd åtkomst. Med tillägget av macOS till de enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS-enheter med hjälp av Intune.** På samma sätt som för andra plattformar som iOS och Android finns det ett företagsportalprogram som gör att macOS kan göra enhetliga registreringar. Du kan använda den nya företagsportalappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter följer organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure Portal kan du nu konfigurera efterlevnadsprinciper för macOS-enheter.
- **Begränsa åtkomsten till program i Azure AD till endast kompatibla macOS-enheter.** Redigering av principer för villkorlig åtkomst har macOS som ett separat alternativ för enhetsplattformen. Nu kan du skapa macOS-specifika principer för villkorsstyrd åtkomst för målprogrammet som angetts i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Villkorlig åtkomst för Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Network Policy Server-tillägg för Azure AD Multi-Factor Authentication

**Typ:** Ny **funktionskategori:**  Multifaktorautentisering **Produktfunktion:** Användarautentisering

Network Policy Server-tillägget för Azure AD Multi-Factor Authentication lägger till molnbaserade multifaktorautentiseringsfunktioner i din autentiseringsinfrastruktur med hjälp av dina befintliga servrar. Med tillägget Nätverksprincipserver kan du lägga till telefonsamtal, SMS eller telefonappsverifiering i ditt befintliga autentiseringsflöde. Du behöver inte installera, konfigurera och underhålla nya servrar.

Det här tillägget har skapats för organisationer som vill skydda anslutningar till virtuella privata nätverk utan att distribuera Azure Multi-Factor Authentication-server. Servertillägget för nätverksprincip fungerar som ett kort mellan RADIUS och molnbaserad Azure AD Multi-Factor Authentication för att tillhandahålla en andra faktor för autentisering för federerade eller synkroniserade användare.

Mer information finns i Integrera [din befintliga nätverksprincipserverinfrastruktur med Azure AD Multi-Factor Authentication.](../authentication/howto-mfa-nps-extension.md)

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställa eller permanent ta bort borttagna användare

**Typ:** Ny **funktionskategori för tjänst:** **Användarhantering Produktkapacitet:** Katalog

I Azure AD-administrationscentret kan du nu:

- Återställa en borttagna användare.
- Ta bort en användare permanent.

**Så här testar du det:**

1. I Administrationscenter för Azure AD väljer [du Alla](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) användare i **avsnittet** Hantera.

2. I listan **Visa** väljer du **Nyligen borttagna användare.**

3. Välj en eller flera nyligen borttagna användare och återställ dem eller ta bort dem permanent.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för appbaserad villkorsstyrd åtkomst i Azure AD

**Typ:** **Tjänstkategorin Har ändrats:** Produktfunktion **för villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Följande appar har lagts till i listan över [godkända klientappar:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft Planner
- Azure Information Protection

Mer information finns i:

- [Godkänt krav för klientapp](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Appbaserad villkorsstyrd åtkomst i Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använda "OR" mellan kontroller i en princip för villkorsstyrd åtkomst

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Produktfunktion för villkorsstyrd åtkomst:** Identitetssäkerhet och skydd

Nu kan du använda "OR" (kräver en av de valda kontrollerna) för kontroller för villkorsstyrd åtkomst. Du kan använda den här funktionen för att skapa principer med "OR" mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare loggar in med hjälp av Multi-Factor Authentication "OR" för att vara på en kompatibel enhet.

Mer information finns i Kontroller [i villkorsstyrd åtkomst i Azure AD.](../conditional-access/controls.md)

---

### <a name="aggregation-of-real-time-risk-detections"></a>Sammansättning av riskidentifiering i realtid

**Typ:** **Funktionskategorin Tjänst har ändrats:** **Identitetsskydd Produktfunktion:** Identitetssäkerhet och skydd

I Azure AD Identity Protection sammanställs nu alla riskidentifieringar i realtid som kommer från samma IP-adress en viss dag för varje riskidentifieringstyp. Den här ändringen begränsar mängden riskidentifiering som visas utan att användarsäkerheten ändras.

Den underliggande realtidsidentifieringen fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggningsrisker konfigurerad för multifaktorautentisering eller blockera åtkomst utlöses den fortfarande vid varje riskabel inloggning.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Inaktuella Azure AD-rapporter

**Typ:** Planera för ändring **av tjänstkategori:** Rapportera **produktfunktion: Hantering** av identitetslivscykel

I Azure Portal får du:

- En ny Azure AD-administrationskonsol.
- Nya API:er för aktivitets- och säkerhetsrapporter.

På grund av dessa nya funktioner togs rapport-API:erna under /reports-slutpunkten ur bruk den 10 december 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk identifiering av inloggningsfält

**Typ:** Fast **tjänstkategori:** Mina appar **produktfunktion:** enkel inloggning

Azure AD stöder automatisk identifiering av inloggningsfält för program som återger ett HTML-användarnamn och lösenordsfält. De här stegen finns [dokumenterade i Avbilda inloggningsfält automatiskt för ett program.](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app) Du hittar den här funktionen genom att lägga till *ett program* som inte är ett galleriprogram på sidan **Företagsprogram** i [Azure Portal](https://aad.portal.azure.com). Dessutom kan du konfigurera  läget för enkel inloggning i det här nya programmet till Lösenordsbaserad enkel **inloggning,** ange en webbadress och sedan spara sidan.

På grund av ett tjänstproblem inaktiverades den här funktionen tillfälligt. Problemet har lösts och fältidentifieringen för automatisk inloggning är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya funktioner för Multi-Factor Authentication

**Typ:** Ny **funktionskategori: Produktfunktion** för multifaktorautentisering: **Identitetssäkerhet** och skydd

Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. För att göra autentiseringsuppgifterna mer anpassningsbara och upplevelsen smidigare har följande funktioner lagts till:

- Multifaktorutmaningsresultat är direkt integrerade i Azure AD-inloggningsrapporten, som innehåller programmatisk åtkomst till MFA-resultat.
- MFA-konfigurationen är mer integrerad i Azure AD-konfigurationsupplevelsen i Azure Portal.

Med den här offentliga förhandsversionen är hantering och rapportering av MFA en integrerad del av kärnkonfigurationen för Azure AD. Nu kan du hantera MFA-hanteringsportalens funktioner i Azure AD-upplevelsen.

Mer information finns i [Referens för MFA-rapportering i Azure Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Villkor för användning

**Typ:** Ny **tjänstkategori: Användningsvillkor** **produktfunktion:** Efterlevnad

Du kan använda användningsvillkoren för Azure AD för att presentera information, till exempel relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav för användare.

Du kan använda användningsvillkoren för Azure AD i följande scenarier:

- Allmänna användningsvillkor för alla användare i din organisation
- Specifika användningsvillkor som baseras på en användares attribut (till exempel läkare jämfört med läkare eller nationella jämfört med internationella anställda, som utförs av dynamiska grupper)
- Specifika användningsvillkor för åtkomst till företagsappar med stor inverkan, till exempel Salesforce

Mer information finns i [Användningsvillkor för Azure AD.](../conditional-access/terms-of-use.md)

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av Privileged Identity Management

**Typ:** Ny **funktionskategori:** Privileged Identity Management **produktfunktion:** Privileged Identity Management

Med Azure AD Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomsten till Azure-resurser (förhandsversion) i din organisation för att:

- Prenumerationer
- Resursgrupper
- Virtuella datorer

Alla resurser i Azure Portal som använder Azure RBAC-funktionen kan dra nytta av alla säkerhets- och livscykelhanteringsfunktioner som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i Privileged Identity Management [för Azure-resurser.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny **funktionskategori: Åtkomstgranskningar** **Produktfunktion:** Efterlevnad

Organisationer kan använda åtkomstgranskningar (förhandsversion) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram:

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om de vill tillåta gäster fortsatt åtkomst baserat på de insikter som tillhandahålls av åtkomstgranskningarna.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [Azure AD-åtkomstgranskningar.](../governance/access-reviews-overview.md)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina appar och Office 365-appstartaren

**Typ:** Ny **funktionskategori för tjänst:** Mina appar **produktfunktion:** enkel inloggning

Nu kan du bättre hantera appar som visas på användarnas portaler via en ny dölj **appegenskap.** Du kan dölja appar för att hjälpa till i fall där appaneler visas för backend-tjänster eller duplicerade paneler och stökiga användares appstartare. Växlingsknappen finns i **avsnittet** Egenskaper i tredjepartsappen och är märkt **Synlig för användaren?** Du kan också dölja en app programmatiskt via PowerShell.

Mer information finns i [Dölj ett program från tredje part från en användares upplevelse i Azure AD.](../manage-apps/hide-application-from-user-portal.md)


**Vad är tillgängligt?**

 Som en del av övergången till den nya administratörskonsolen finns två nya API:er för att hämta Azure AD-aktivitetsloggar tillgängliga. Den nya uppsättningen API:er ger bättre filtrerings- och sorteringsfunktioner samt bättre gransknings- och inloggningsaktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporterna kan nu nås via Api:et för riskidentifiering för Identity Protection i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager

**Typ:** **Tjänstkategorin Har ändrats:** Identity Manager **Product capability:** Identitetslivscykelhantering

Ett sammanslagningspaket för snabbkorrigeringar (version 4.4.1642.0) är tillgängligt från och med den 25 september 2017 för Identity Manager 2016 Service Pack 1. Det här sammanslagningspaketet:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla Identity Manager 2016 Service Pack 1-uppdateringar upp till version 4.4.1459.0 för Identity Manager 2016.
- Kräver att du har Identity Manager 2016 version 4.4.1302.0.

Mer information finns i [Samlad snabbkorrigeringspaket (version 4.4.1642.0) är tillgängligt för Identity Manager 2016 Service Pack 1.](https://support.microsoft.com/help/4021562)

---
