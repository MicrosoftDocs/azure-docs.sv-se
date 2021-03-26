---
title: Vill du arkivera vad som är nytt i Azure Active Directory? | Microsoft Docs
description: Avsnittet Nyheter i avsnittet Översikt i den här innehålls uppsättningen innehåller 6 månaders aktivitet. Efter 6 månader tas objekten bort från huvud artikeln och infogas i den här Arkiv artikeln.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40dafe13dc0e8866c99e56879bed419808a0487c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560190"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Vill du arkivera vad som är nytt i Azure Active Directory?

Den primära artikeln [Nyheter i Azure Active Directory? viktig](whats-new.md) information innehåller uppdateringar för de senaste sex månaderna, medan den här artikeln innehåller all äldre information.

Vad är nytt i Azure Active Directory? versions information innehåller information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

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

## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Som IT-administratör vill jag att mål klient program ska använda villkorlig åtkomst

**Typ:** Planera för ändring   
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Med GA-versionen av klient program villkoret i villkorlig åtkomst gäller nu nya principer som standard för alla klient program. Detta inkluderar äldre autentiserings klienter. Befintliga principer är oförändrade men inställningen *Konfigurera Ja/Nej* tas bort från befintliga principer för att enkelt se vilka klient program som tillämpas på principen. 

När du skapar en ny princip måste du se till att undanta användare och tjänst konton som fortfarande använder äldre autentisering. Om du inte gör det kommer de att blockeras. [Läs mer](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Kommande korrigeringar för SCIM-efterlevnad

**Typ:** Planera för ändring  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten utnyttjar SCIM-standarden för integrering med program. Vår implementering av SCIM-standarden utvecklas och vi förväntar oss att göra ändringar i vårt beteende i hur vi utför KORRIGERINGs åtgärder samt anger egenskapen "aktiv" på en resurs. [Läs mer](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Grupp ägar inställningen på Azure Admin Portal kommer att ändras

**Typ:** Planera för ändring  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Inställningen ägare på sidan allmänna inställningar för inställningar kan konfigureras för att begränsa ägar tilldelnings behörigheter till en begränsad grupp användare i Azure admin-portalen och åtkomst panelen. Vi kommer snart att ha möjlighet att tilldela behörighet för grupp ägare, inte bara på dessa två UX-portaler, men tillämpar även principen på Server delen för att ge ett konsekvent beteende mellan slut punkter, till exempel PowerShell och Microsoft Graph. 

Vi börjar med att inaktivera den aktuella inställningen för de kunder som inte använder den och kommer att erbjuda ett alternativ för att omfånget användare för grupp ägarens privilegium under de kommande månaderna. Information om hur du uppdaterar grupp inställningar finns i redigera grupp information med [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory registrerings tjänsten avslutar stödet för TLS 1,0 och 1,1

**Typ:** Planera för ändring  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Systemet

TLS (Transport Layer Security) 1,2 och uppdaterings servrar och klienter kommer snart att kommunicera med Azure Active Directory Device Registration-tjänsten. Stöd för TLS 1,0 och 1,1 för kommunikation med Azure AD Device Registration Service kommer att tas ur bruk:
- Den 31 augusti 2020, i alla suveräna moln (GCC hög, DoD osv.)
- Den 30 oktober 2020 i alla kommersiella moln

[Läs mer](../devices/reference-device-registration-tls-1-2.md) om TLS 1,2 för registrerings tjänsten för Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Inloggnings loggar för Windows Hello för företag visas i inloggnings loggar för Azure AD

**Typ:** Fastsatt  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Windows Hello för företag gör att slutanvändarna kan logga in på Windows-datorer med en gest (t. ex. en PIN-kod eller bio metrisk). Azure AD-administratörer kan vilja särskilja inloggnings program för Windows Hello för företag från andra Windows-inloggningar som en del av en organisations resan till lösen ords lös autentisering. 

Administratörer kan nu se om en Windows-autentisering använder Windows Hello för företag genom att kontrol lera fliken autentiseringsinformation för en Windows-inloggnings händelse på bladet Azure AD Sign-Ins i Azure Portal. Windows Hello för företag-autentiseringar kommer att innehålla "WindowsHelloForBusiness" i fältet autentiseringsmetod. Mer information om hur du tolkar Sign-In loggar finns i [dokumentationen för inloggnings loggar](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Korrigeringar för att gruppera borttagnings beteende och prestanda förbättringar

**Typ:** Fastsatt  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
När en grupp har ändrats från "i omfång" till "out-of-Scope" och en administratör klickade på Starta innan ändringen slutfördes, togs inte gruppobjektet bort. Nu kommer gruppobjektet att tas bort från mål programmet när det hamnar utanför omfånget (inaktiverat, Borttaget, ej tilldelat eller inte godkänt omfångs filter). [Läs mer](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Offentlig för hands version: administratörer kan nu lägga till anpassat innehåll i e-postmeddelandet till granskare när de skapar en åtkomst granskning

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Identitets styrning
 
När en ny åtkomst granskning skapas får granskaren ett e-postmeddelande som begär att de ska kunna slutföra åtkomst granskningen. Många av våra kunder har bett om möjlighet att lägga till anpassat innehåll i e-postmeddelandet, till exempel kontakt information eller annat ytterligare stöd för att hjälpa granskaren. 

Som är tillgänglig i den offentliga för hands versionen kan administratörer ange anpassat innehåll i e-postmeddelandet som skickas till granskare genom att lägga till innehåll i avsnittet "Avancerad" i åtkomst granskningar för Azure AD. Vägledning om hur du skapar åtkomst granskningar finns i [skapa en åtkomst granskning av grupper och program i åtkomst granskningar i Azure AD](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Authorization Code Flow för appar på en enda sida som är tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Utvecklings miljö
 
På grund av moderna webb läsar begränsningar från tredje part, till exempel Safari ITP, måste SPAs använda auktoriseringskod i stället för det implicita flödet för att upprätthålla SSO och MSAL.js v 2. x stöder nu Authorization Code Flow. 

Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA och skriva "Spa" och använda kod flödet för autentisering. Se [Logga in användare och få en åtkomsttoken i ett Java Script spa med hjälp av auth Code Flow](../develop/quickstart-v2-javascript-auth-code.md) för ytterligare vägledning.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD-programproxy stöder nu Fjärrskrivbordstjänster webb klienten

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Azure AD-programproxy stöder nu webb klienten Fjärrskrivbordstjänster (RDS). RDS-webbklienten ger användare åtkomst till fjärr skrivbords infrastrukturen via valfri HTLM5 webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome osv. Användare kan interagera med fjärrappar eller skriv bord som de skulle ha en lokal enhet från var som helst. Genom att använda Azure AD-programproxy kan du öka säkerheten för din RDS-distribution genom att tvinga Förautentiserings-och villkorliga åtkomst principer för alla typer av rika klient program. Vägledning finns i [publicera fjärr skrivbord med Azure AD-programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nästa generations Azure AD B2C användar flöden i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Förenklad användar flödes upplevelse erbjuder funktions paritet med förhands gransknings funktioner och är start för alla nya funktioner. Användarna kommer att kunna aktivera nya funktioner inom samma användar flöde, vilket minskar behovet av att skapa flera versioner med varje ny funktions version. Slutligen fören klar det nya, användarvänliga UX valet och skapandet av användar flöden. Prova nu genom att [skapa ett användar flöde](../../active-directory-b2c/tutorial-create-user-flows.md). 

Mer information om användare flöden finns [i användar flödes versioner i Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – juli 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juli 2020 har vi lagt till följande 55 nya program i vårt app-galleri med stöd för federation:

[Clap Your händerna](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor-valvet](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects receptionist](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [mynt konstruktion](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [Reback](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md), [bloggi](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md) [, kpifire](../saas-apps/kpifire-tutorial.md), [TextLine](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data hubb enkel inloggning](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [utgående](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right cybersäkerhet ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [taga](../saas-apps/momenta-tutorial.md), [öka](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe bot](https://tellme365liteweb.azurewebsites.net/), [inspirera](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (skol Management System)](https://smartschoolz.com/login), [Zepto-intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), Trackplan, [skedda](http://www.trackplanfm.com/), [WhosOnLocation](../saas-apps/skedda-tutorial.md), [Coggle](../saas-apps/whos-on-location-tutorial.md), [Kemp: LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), BrowserStack [enkel inloggning](../saas-apps/browserstack-single-sign-on-tutorial.md) [](../saas-apps/coggle-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här. https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visa roll tilldelningar för alla omfattningar och möjlighet att ladda ned dem till en CSV-fil

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Nu kan du Visa roll tilldelningar för alla omfattningar för en roll på fliken "roller och administratörer" i Azure AD-portalen. Du kan också hämta roll tilldelningarna för varje roll till en CSV-fil. Vägledning om hur du visar och lägger till roll tilldelningar finns i [Visa och tilldela administratörs roller i Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Inaktuellitet i Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Typ:** Föråldrad  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Azure Multi-Factor Authentication Software Development (Azure MFA SDK) nådde slutet av livs längden den 14 november 2018, som först lanserades i november 2017. Microsoft kommer att stänga av SDK-tjänsten gällande den 30 september 2020. Alla anrop till SDK: n kommer att Miss inträffat.

Om din organisation använder Azure MFA SDK måste du migrera den 30 september 2020:
- Azure MFA SDK för MIM: om du använder SDK med MIM bör du migrera till Azure MFA Server och aktivera Privileged Access Management (PAM) genom att följa dessa [anvisningar](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK för anpassade appar: Överväg att integrera din app i Azure AD och använda villkorlig åtkomst för att genomdriva MFA. Gå igenom den här [sidan](../manage-apps/plan-an-application-integration.md)för att komma igång. 

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Villkor för användar risk i princip för villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Med stöd för användar risker i Azure AD-princip för villkorlig åtkomst kan du skapa flera principer för användar risk. Olika minimi nivåer för användar risk kan krävas för olika användare och appar. Utifrån användar risk kan du skapa principer för att blockera åtkomst, kräva Multi-Factor Authentication, säkra lösen ords ändringar eller omdirigera till Microsoft Cloud App Security för att framtvinga en replikeringsprincip, till exempel ytterligare granskning.

Användar risk villkoret kräver Azure AD Premium P2 eftersom det använder Azure Identity Protection, som är ett P2-erbjudande. Mer information om villkorlig åtkomst finns i dokumentationen för [villkorlig åtkomst för Azure AD](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO stöder nu appar som kräver att SPNameQualifier anges vid begäran

**Typ:** Fastsatt  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Vissa SAML-program kräver att SPNameQualifier returneras i intygets ämne när det begärs. Azure AD svarar nu korrekt när en SPNameQualifier begärs i NameID-principen för begäran. Detta fungerar även för SP-initierad inloggning och IdP initierad inloggning kommer att följa.  Mer information om SAML-protokoll i Azure Active Directory finns i [Single Sign-On SAML-protokollet](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B-samarbete har stöd för att bjuda in MSA och Google-användare i Azure Government-klienter

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Azure Government klienter som använder B2B-samarbets funktionerna kan nu bjuda in användare som har ett Microsoft-eller Google-konto. Om du vill ta reda på om din klient kan använda dessa funktioner följer du anvisningarna på [Hur vet jag om B2B-samarbete är tillgängligt i min Azure amerikanska myndighets klient?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Användar objekt i MS Graph v1 innehåller nu externalUserState-och externalUserStateChangedDateTime-egenskaper

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Egenskaperna externalUserState och externalUserStateChangedDateTime kan användas för att hitta inbjudna B2B-gäster som inte har accepterat sina inbjudningar än och som skapar automatisering, som att ta bort användare som inte har accepterat sina inbjudningar efter ett visst antal dagar. De här egenskaperna är nu tillgängliga i MS Graph v1. Vägledning om hur du använder dessa egenskaper finns i [användar resurs typ](/graph/api/resources/user).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Hantera autentiseringsbegäranden i Azure AD villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Med hanterings funktionerna för autentisering kan du konfigurera hur ofta dina användare måste ange inloggnings uppgifter och om de behöver ange autentiseringsuppgifter efter att de har stängt och öppnat om webbläsare för att få större säkerhet och flexibilitet i din miljö.
 
Dessutom används hantering av autentisering för att endast använda den första Factor Authentication på Azure AD-anslutna, hybrid Azure AD-anslutna och registrerade Azure AD-enheter. Nu gäller hantering av autentiserings-sessioner även för MFA. Mer information finns i [Konfigurera hantering av autentisering med villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – juni 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juni 2020 har vi lagt till följande 29 nya program i vårt app-galleri med stöd för federation:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [grindar](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI revisor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [hubb Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-partner go-to-Marketing-verktygslåda](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation i molnet](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kisi fysisk säkerhet](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [noll](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [EDX för Business SAML 2,0-integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning-plattform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [fält-ID](../saas-apps/field-id-tutorial.md) [, kurs utbud](https://smallstep.com/sso-ssh/) [SAML](../saas-apps/curricula-saml-tutorial.md), [perforce Helix Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md) [,](https://cloud.metacompliance.com/)  

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial . För att lista ditt program i Azure AD App-galleriet läser du informationen här: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-kopplingar för externa identiteter för självbetjänings registrering finns nu i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
API-kopplingar för externa identiteter gör att du kan använda webb-API: er för att integrera självbetjänings registrering med externa moln system. Det innebär att du nu kan anropa webb-API: er som vissa steg i ett registrerings flöde för att utlösa molnbaserade anpassade arbets flöden. Du kan till exempel använda API-kopplingar för att:

- Integrera med ett anpassat godkännande arbets flöden.
- Utföra identitets bevisning
- Verifiera indata från användaren
- Skriv över användarattribut
- Kör anpassad affärs logik

Mer information om alla upplevelser som är möjliga med API-kopplingar finns i [använda API-kopplingar för att anpassa och utöka](../external-identities/api-connectors-overview.md)självbetjänings registrering eller [Anpassa extern identitet självbetjänings registrering med Web API-integreringar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Etablera på begäran och få användare i dina appar på några sekunder

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten arbetar för närvarande med en cyklisk bas. Tjänsten körs var 40: e minut. Med [etablerings funktionen på begäran](https://aka.ms/provisionondemand) kan du välja en användare och etablera dem på några sekunder. Med den här funktionen kan du snabbt felsöka etablerings problem, utan att behöva göra en omstart för att tvinga etablerings cykeln att starta igen. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Ny behörighet för att använda hantering av Azure AD-berättigande i grafen

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter
 
En ny delegerad behörighet EntitlementManagement. Read. all är nu tillgänglig för användning med rättighets hanterings-API: et i Microsoft Graph beta. Mer information om tillgängliga API: er finns i [arbeta med API för hantering av Azure AD-hantering](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true).

---

### <a name="identity-protection-apis-available-in-v10"></a>API: er för identitets skydd som är tillgängliga i v 1.0

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
RiskyUsers-och riskDetections-Microsoft Graph-API: er är nu allmänt tillgängliga. Nu när de är tillgängliga på v 1.0-slutpunkten, bjuder vi in dig att använda dem i produktionen. Mer information finns i [Microsoft Graph-dokumenten](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Känslighets etiketter för att tillämpa principer på Microsoft 365 grupper är nu allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 

Nu kan du skapa känslighets etiketter och använda etikett inställningar för att tillämpa principer på Microsoft 365 grupper, inklusive sekretess (offentlig eller privat) och åtkomst princip för externa användare. Du kan skapa en etikett med sekretess policyn som privat och extern användar åtkomst princip för att inte tillåta att gäst användare läggs till. När en användare använder den här etiketten i en grupp, kommer gruppen att vara privat och inga gäst användare får läggas till i gruppen. 

Känslighets etiketter är viktiga för att skydda affärs kritiska data och gör att du kan hantera grupper i skala på ett kompatibelt och säkert sätt. Vägledning om hur du använder känslighets etiketter finns i [tilldela känslighets etiketter till Microsoft 365 grupper i Azure Active Directory (för hands version)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Uppdateringar som stöder Microsoft Identity Manager för Azure AD Premium kunder

**Typ:** Ändrad funktion  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure-support är nu tillgängligt för Azure AD integration-komponenter i Microsoft Identity Manager 2016, genom slutet av det utökade stödet för Microsoft Identity Manager 2016. Läs mer på [support uppdatering för Azure AD Premium kunder som använder Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen höjs

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Tidigare var antalet grupper som du kan använda när du villkorligt ändrar anspråk baserat på grupp medlemskap i en enskild program konfiguration begränsad till 10. Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen har nu ökat till högst 50 grupper. Mer information om hur du konfigurerar anspråk finns i konfiguration av [SSO-anspråk för företags program](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Aktivera grundläggande formatering på inloggnings sidans text komponent i företags anpassning.

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Företags anpassnings funktionen på Azure AD/Microsoft 365 inloggnings upplevelsen har uppdaterats så att kunden kan lägga till hyperlänkar och enkel formatering, inklusive fetstil, understrykning och kursiv stil. Vägledning om hur du använder den här funktionen finns i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Prestanda förbättringar för etablering

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Etablerings tjänsten har uppdaterats för att minska tiden då en [stegvis cykel](../app-provisioning/how-provisioning-works.md#incremental-cycles) slutförs. Det innebär att användare och grupper kommer att tillhandahållas i sina program snabbare än tidigare. Alla nya etablerings jobb som skapats efter 6/10/2020 kommer automatiskt att dra nytta av prestanda förbättringarna. Alla program som kon figurer ATS för etablering före 6/10/2020 måste starta om en gång efter 6/10/2020 för att dra nytta av prestanda förbättringarna. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Att presentera utfasningen av ADAL-och MS Graph-paritet

**Typ:** Föråldrad  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Hantering av enhetens livs cykel

Nu när Microsoft Authentication libraries (MSAL) är tillgängligt kommer vi inte längre att lägga till nya funktioner i Azure Active Directory Authentication libraries (ADAL) och säkerhets korrigeringarna upphör den 30 juni 2022. Mer information om hur du migrerar till MSAL finns i [migrera program till Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Dessutom har vi utfört arbetet för att göra alla Azure AD Graph-funktioner tillgängliga via MS Graph. Azure AD Graph-API: er får därför bara Bugfix och säkerhets korrigeringar till och med den 30 juni 2022. Mer information finns i [Uppdatera dina program för att använda Microsoft Authentication Library och Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>Maj 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Dra tillbaka egenskaper i inloggningar-, riskyUsers-och riskDetections-API: er

**Typ:** Planera för ändring  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet

För närvarande används uppräknade typer för att representera egenskapen riskType i både riskDetections-API och riskyUserHistoryItem (i för hands version). Uppräknade typer används också för egenskapen riskEventTypes i inloggningar-API: et. Nu kommer vi att representera dessa egenskaper som strängar. 

Kunderna ska övergå till egenskapen riskEventType i beta-riskDetections och riskyUserHistoryItem-API: et och för att riskEventTypes_v2 egenskap i beta inloggningar-API: n den 9 september 2020. Vid det datumet kommer vi att ta bort de aktuella egenskaperna för riskType och riskEventTypes. Mer information finns i [ändringar i egenskaper för risk händelser och API: er för identitets skydd på Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Utfasning av egenskapen riskEventTypes i inloggningar v 1.0 API på Microsoft Graph

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** & skydd för identitets säkerhet

Uppräknade typer kommer att växla till sträng typer när de representerar risk händelse egenskaper i Microsoft Graph september 2020. Förutom att påverka för hands versions-API: erna påverkar den här ändringen även inloggningar-API: t för produktion.

Vi har infört en ny riskEventsTypes_v2 (String)-egenskap för inloggningar v 1.0-API: et. Vi kommer att dra tillbaka den aktuella riskEventTypes-egenskapen (Enum) den 11 juni 2022 i enlighet med vår Microsoft Graph utfasnings princip. Kunderna ska övergå till riskEventTypes_v2-egenskapen i v 1.0 inloggningar-API: n den 11 juni 2022. Mer information finns i [utfasningen av egenskapen riskEventTypes i inloggningar v 1.0 API på Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Kommande ändringar av MFA-e-postmeddelanden

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Vi gör följande ändringar i e-postmeddelandena för Cloud MFA:

E-postaviseringar kommer att skickas från följande adress: azure-noreply@microsoft.com och msonlineservicesteam@microsoftonline.com . Vi uppdaterar innehållet i meddelanden om bedrägeri aviseringar för att bättre illustrera de steg som krävs för att avblockera användningen.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Ny självbetjänings registrering för användare i federerade domäner som inte kan komma åt Microsoft Teams eftersom de inte har synkroniserats med Azure Active Directory.

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 

För närvarande kan användare som finns i domäner federerade i Azure AD, men som inte har synkroniserats till klienten, inte komma åt team. Med början i slutet av juni gör den här nya funktionen att de kan göra det genom att utöka den befintliga e-postverifierade inloggnings funktionen. Detta gör det möjligt för användare som kan logga in till en federerad IdP, men som ännu inte har ett användar objekt i Azure-ID, för att skapa ett användar objekt automatiskt och autentiseras för team. Deras användar objekt kommer att markeras som "självbetjänings registrering". Det här är en utökning av den befintliga funktionen för att verifiera e-postverifiering som användare i hanterade domäner kan göra och kan kontrol leras med hjälp av samma flagga. Den här ändringen kommer att slutföras under följande två månader. Titta efter dokumentations uppdateringar [här](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Kommande korrigering: OIDC identifierings dokument för Azure Government molnet uppdateras för att referera till rätt graf-slutpunkter.

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Från och med juni börjar OIDC identifierings dokument [Microsoft Identity Platform och OpenID Connect-protokollet](../develop/v2-protocols-oidc.md) på [Azure Government moln](../develop/authentication-national-cloud.md) slut punkten (login.microsoftonline.us) att returnera rätt [nationell moln](/graph/deployments) slut punkt ( https://graph.microsoft.us eller https://dod-graph.microsoft.us) , baserat på den klient som tillhandahölls).  Den ger för närvarande den felaktiga graph.microsoft.com msgraph_host-fältet (graf endpoint).  

Den här fel korrigeringen distribueras gradvis över cirka 2 månader.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government användare kommer inte längre att kunna logga in på login.microsoftonline.com

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Den officiella Azure Active Directory-utfärdaren (Azure AD) för Azure Government ändrats från till den 1 juni 2018 https://login-us.microsoftonline.com https://login.microsoftonline.us . Om du äger ett program i en Azure Government-klient måste du uppdatera ditt program för att kunna logga in användare i en. us-slutpunkt.

Från och med den 5 maj kommer Azure AD att börja verkställa slut punkts ändringen, vilket hindrar Azure Government användare från att logga in på appar som finns i Azure Government-klienter med hjälp av den offentliga slut punkten (microsoftonline.com). Påverkade appar kommer att börja se ett fel AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Det kommer att finnas en gradvis distribution av den här ändringen med tvång som förväntas vara slutförd för alla appar i juni 2020. Mer information finns i [blogg inlägget Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML-begäran om enkel utloggning skickar nu NameID i rätt format

**Typ:** Fastsatt  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
När en användare klickar på utloggning (t. ex. i portalen för appar) skickar Azure AD ett SAML-meddelande med en enkel utloggning till varje app som är aktiv i användarsessionen och har en konfigurerad utloggnings-URL. Dessa meddelanden innehåller en NameID i ett beständigt format.

Om den ursprungliga SAML-inloggningens token använder ett annat format för NameID (t. ex. e-post/UPN) kan SAML-appen inte korrelera NameID i utloggnings meddelandet till en befintlig session (eftersom NameIDs som används i båda meddelandena är olika), vilket gjorde att utloggnings meddelandet ignorerades av SAML-appen och användaren är inloggad. Den här snabb korrigeringen gör utloggnings meddelandet konsekvent med den NameID som har kon figurer ATS för programmet.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Rollen som hybrid identitets administratör är nu tillgänglig med moln etablering

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Cloud-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
IT-administratörer kan börja använda den nya rollen "hybrid administratör" som den minst privilegierade rollen för att konfigurera Azure AD Connect moln etablering. Med den nya rollen behöver du inte längre använda den globala administratörs rollen för att konfigurera och konfigurera moln etablering. [Läs mer](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I maj 2020 har vi lagt till följande 36 nya program i vårt app-galleri med stöd för federation:

[Moula](https://moula.com.au/pay/merchants), [surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [](http://www.tacklebox.app/)engagerande, [Powell-team](https://powell-software.com/en/powell-teams-en/), [TalentSoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engagerad](https://b-engaged.se/), [Competella kontakt Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft-identitet](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insvit](../saas-apps/insuite-tutorial.md), [ändrings process hantering](../saas-apps/change-process-management-tutorial.md), [cyara CX Assurance-plattform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart global styrning](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [WHimsical](../saas-apps/whimsical-tutorial.md) [, Trelica,](../saas-apps/trelica-tutorial.md) [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO för BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), EasySSO [för Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Axiad](../saas-apps/axiad-cloud-tutorial.md) [, ColorTokens](../saas-apps/humanage-tutorial.md) [, ZTNA](../saas-apps/anyone-home-crm-tutorial.md) [CCH](../saas-apps/colortokens-ztna-tutorial.md) [,](../saas-apps/ice-contact-center-tutorial.md) Tagetik [, ShareVault](../saas-apps/sharevault-tutorial.md) [, Vyond](../saas-apps/textexpander-tutorial.md) [, TextExpander,](../saas-apps/vyond-tutorial.md) [askSpoke,](../saas-apps/askspoke-tutorial.md) [](../saas-apps/cch-tagetik-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial .

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge – över 26M-användare är redan en omfattning av en rapport princip. Med meddelandet idag skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du [hantera endast rapport principer program mässigt](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Registrera dig för gäst användare med självbetjäning

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Med externa identiteter i Azure AD kan du tillåta att personer utanför organisationen kan komma åt dina appar och resurser samtidigt som de kan logga in med den identitet de föredrar. När du delar ett program med externa användare är det inte säkert att du alltid vet i förväg vilka som behöver åtkomst till programmet. Med [självbetjänings registrering](../external-identities/self-service-sign-up-overview.md)kan du göra det möjligt för gäst användare att registrera sig och få ett gäst konto för dina branschspecifika appar (LOB). Registrerings flödet kan skapas och anpassas för att ge stöd för Azure AD och sociala identiteter. Du kan också samla in ytterligare information om användaren under registreringen.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

[Arbets boken insikter och rapportering](../conditional-access/howto-conditional-access-insights-reporting.md) ger administratörer en översikt över villkorlig åtkomst för Azure AD i sin klient organisation. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](../conditional-access/troubleshoot-conditional-access.md) visas de tilldelningar, villkor och kontroller som är uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna villkorlig åtkomst eller endast rapporter i inloggnings informationen.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nya fråge funktioner för katalog objekt i Microsoft Graph finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph **Product Capability:** Developer Experience

Nya funktioner introduceras för Microsoft Graph Directory-objekt-API: er, vilket möjliggör antal, Sök, filtrera och sortera-åtgärder. Detta ger utvecklare möjlighet att snabbt fråga våra katalog objekt utan att det sker några lösningar, till exempel minnes filtrering och sortering. Lär dig mer i det här [blogg inlägget](https://aka.ms/CountFilterMSGraphAAD).

Vi är för närvarande en offentlig för hands version, som söker efter feedback. Skicka dina kommentarer med den här [korta undersökningen](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurera SAML-baserad enkel inloggning med Microsoft Graph API (beta)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Nu finns stöd för att skapa och konfigurera ett program från Azure AD-galleriet med MS Graph API: er i Beta versionen. Om du behöver konfigurera SAML-baserad enkel inloggning för flera instanser av ett program sparar du tid genom att använda Microsoft Graph API: er för att [Automatisera konfigurationen av SAML-baserad enkel inloggning](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nya etablerings anslutningar i Azure AD-programgalleriet – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

* [8 x 8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic efter organisation](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Kryptering av SAML-token är allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Med [SAML token Encryption](../manage-apps/howto-saml-token-encryption.md) kan program konfigureras för att ta emot krypterade SAML-kontroller. Funktionen är nu allmänt tillgänglig i alla moln.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Grupp namns anspråk i Application-token är allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Grupp anspråk som utfärdats i en token kan nu begränsas till bara de grupper som tilldelats programmet.  Detta är särskilt viktigt när användare är medlemmar i ett stort antal grupper och det uppstod en risk för att överskrida storleks gränser för token. När den här nya funktionen är på plats är möjligheten att [lägga till grupp namn i tokens](../hybrid/how-to-connect-fed-group-claims.md) allmänt tillgänglig.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Tillbakaskrivning av workday har nu stöd för att ange attribut för telefonnummer till arbetet

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Vi har förbättrat programetablerings appen för Workday-installation så att den nu stöder tillbakaskrivning av arbetstelefonnummer och attribut för mobil nummer. Förutom e-post och användar namn kan du nu konfigurera appen för tillbakaskrivning av arbets dagar för att flöda telefonnummer värden från Azure AD till Workday. Mer information om hur du konfigurerar tillbakaskrivning av telefonnummer finns i själv studie kursen om [tillbakaskrivning av workday](../saas-apps/workday-writeback-tutorial.md) -appar. 

---

### <a name="publisher-verification-preview"></a>Utgivarens verifiering (för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Utvecklings miljö
 
Utgivarens verifiering (för hands version) hjälper administratörer och slutanvändare att förstå äktheten hos programutvecklare som integreras med Microsoft Identity Platform. Mer information finns i [utgivar verifiering (för hands version)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Authorization Code Flow för appar på en sida

**Typ:** Ändrad funktions **tjänst kategori:** autentisering **produkt kapacitet:** utvecklings miljö

På grund av moderna webb läsar [begränsningar från tredje part, till exempel Safari ITP](../develop/reference-third-party-cookies-spas.md), måste SPAs använda kod flödet i stället för det implicita flödet för att upprätthålla SSO. MSAL.js v 2. x stöder nu Authorization Code Flow. Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA och skriva "Spa" och använda kod flödet för autentisering. Anvisningar finns i [snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script spa med hjälp av kod flödet för autentisering](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Förbättrad filtrering för enheter finns i offentlig för hands version

**Typ:** Ändrad funktion   
**Tjänste kategori:** **Produkt kapacitet** för enhets hantering: hantering av enhetens livs cykel
 
Tidigare var de enda filter som du kan använda "aktiverade" och "aktivitets datum". Nu kan du [filtrera listan över enheter på fler egenskaper](../devices/device-management-azure-portal.md#device-list-filtering-preview), inklusive OS-typ, kopplings typ, efterlevnad med mera. De här tilläggen bör förenkla lokalisering av en viss enhet.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig

**Typ:** Ändrad funktion   
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig. 

Tidigare var du tvungen att hantera dina B2C-klientbaserade program separat från resten av dina appar med hjälp av den äldre program upplevelsen. Detta innebar att olika appar skapas på olika platser i Azure.

Den nya upplevelsen visar alla B2C app-registreringar och Azure AD App-registreringar på ett och samma ställe och ger ett konsekvent sätt att hantera dem. Oavsett om du behöver hantera en app som riktas mot kund eller en app som har åtkomst till Microsoft Graph för program mässigt Hantera Azure AD B2C resurser behöver du bara lära dig ett sätt att göra saker.

Du kan komma igång med den nya upplevelsen genom att navigera i Azure AD B2Cs tjänsten och välja bladet Appregistreringar. Upplevelsen är också tillgänglig från Azure Active Directorys tjänsten.

Azure AD B2C Appregistreringar-upplevelsen baseras på den allmänna [appens registrerings upplevelse](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) för Azure AD-klienter, men är skräddarsydd för Azure AD B2C. Den äldre program upplevelsen är inaktuell i framtiden.

Mer information finns [i den nya appens registrerings upplevelse för Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Den kombinerade registreringen av säkerhets information är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Den kombinerade registrerings upplevelsen för Multi-Factor Authentication (MFA) och Self-Service återställning av lösen ord (SSPR) är nu allmänt tillgänglig. Den här nya registreringen gör det möjligt för användare att registrera sig för MFA-och SSPR i en enda steg-för-steg-process. När du distribuerar den nya upplevelsen för din organisation kan användarna registrera sig på kortare tid och med färre krångel. Kolla in blogg inlägget [här](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Utvärdering av kontinuerlig åtkomst är en ny säkerhetsfunktion som gör det möjligt att tillämpa principer på förlitande parter i nära real tid när händelser inträffar i Azure AD (t. ex. borttagning av användar konto). Vi rullar ut den här funktionen först för team-och Outlook-klienter. Läs vår [blogg](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) och  [dokumentation](../conditional-access/concept-continuous-access-evaluation.md)om du vill ha mer information.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-inloggning: firstline-anställda kan logga in till Azure AD-program med sina telefonnummer och inget lösen ord

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** Användarautentisering

Office lanserar en serie mobila appar som går till icke-traditionella organisationer och anställda i stora organisationer som inte använder e-post som primär kommunikations metod. De här apparna riktar Frontline anställda, Deskless-arbetskrafter, fält agenter eller detalj handels anställda som inte får en e-postadress från sin arbetsgivare, har åtkomst till en dator eller till den. Med det här projektet kan de anställda logga in på företags program genom att ange ett telefonnummer och roundtripping en kod. Mer information finns i vår dokumentation om [administratör](../authentication/howto-authentication-sms-signin.md) [och slutanvändare](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Bjud in interna användare att använda B2B-samarbete

**Typ:** Ny funktion

**Tjänste kategori:** Business

**Produkt kapacitet:**

Vi utökar B2B-Inbjudnings funktionen så att befintliga interna konton kan bjudas in att använda B2B-samarbets uppgifter som skickas framåt. Detta görs genom att skicka användarobjektet till inbjudans-API: t Förutom vanliga parametrar som den inbjudna e-postadressen. Användarens objekt-ID, UPN, grupp medlemskap, app-tilldelning etc. förblir intakt, men fortsätter att använda B2B för att autentisera med sina autentiseringsuppgifter för hem klienten i stället för de interna autentiseringsuppgifter som de använde före inbjudan. Mer information finns i [dokumentationen](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge, med över 26M-användare som redan är i omfattning av en rapport princip. Med det här meddelandet skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du även [Hantera rapport principer program mässigt](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Arbets boken för villkorlig åtkomst [insikter och rapportering](../conditional-access/howto-conditional-access-insights-reporting.md) ger administratörer en översikt över villkorlig åtkomst för Azure AD i klienten. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](../conditional-access/troubleshoot-conditional-access.md) visas vilka tilldelningar, villkor och kontroller som var uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna **villkorlig åtkomst** eller **endast rapporter** i inloggnings informationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nya federerade appar är tillgängliga i Azure AD App Galleri – april 2020

**Typ:** Ny funktion

**Tjänste kategori:** Företags program

**Produkt kapacitet:** integration från tredje part

I april 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[SincroPool-appar](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT anskaffnings Svit](../saas-apps/iwt-procurement-suite-tutorial.md) [](../saas-apps/trend-micro-tutorial.md) , Lunni, [EasySSO för JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 flytta](https://app.mover.io/login), [högtalare](https://speakerengage.com/login.php), [belys](../saas-apps/honestly-tutorial.md) [, Ally, DutyFlow](../saas-apps/ally-tutorial.md) [, AlertMedia](https://app.dutyflow.nl/) [, gr8](../saas-apps/alertmedia-tutorial.md), Pendo [,](../saas-apps/highground-tutorial.md)Highground [,](../saas-apps/harmony-tutorial.md)Timetabling [, SynchroNet](../saas-apps/timetabling-solutions-tutorial.md), [Fortes](../saas-apps/synchronet-click-tutorial.md), [Litmus, GroupTalk](../saas-apps/gr8-people-tutorial.md) [, Frontify](../saas-apps/pendo-tutorial.md), MongoDB, TickitLMS, Coco [, Nitro](../saas-apps/frontify-tutorial.md), TMWS [](https://www.made-in-office.com/en/) [,](../saas-apps/nitro-productivity-suite-tutorial.md) [,](../saas-apps/fortes-change-cloud-tutorial.md), [](https://recorder.grouptalk.com/),, [,](../saas-apps/mongodb-cloud-tutorial.md) [](../saas-apps/tickitlms-learn-tutorial.md) [,,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [,](../saas-apps/litmus-tutorial.md), [](https://lunni.fi/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för oAuth2PermissionGrant tillgängligt för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för oAuth2PermissionGrant är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Stöd för Microsoft Graph delta frågor för organisations kontakt är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta frågor för organisatoriska kontakter är allmänt tillgängliga! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker orgContact data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Stöd för Microsoft Graph delta frågor för programmet är allmänt tillgängligt

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för program är allmänt tillgänglig! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker program data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för administrativa enheter som är tillgängliga för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings upplevelse delta fråga för administrativa enheter är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Hantera telefonnummer för autentisering och mer i nya Microsoft Graph beta-API: er

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Dessa API: er är ett nyckel verktyg för att hantera dina användares autentiseringsmetoder. Nu kan du programmatiskt för att registrera och hantera de autentiserare som används för MFA och lösen ords återställning via självbetjäning (SSPR). Detta har varit en av de mest efterfrågade funktionerna i Azure AD MFA, SSPR och Microsoft Graph Spaces. De nya API: er som vi har lanserat i den här vågen ger dig möjlighet att:

- Läsa, lägga till, uppdatera och ta bort en användares telefoner för autentisering
- Återställa en användares lösen ord
- Aktivera och inaktivera SMS-inloggning

Mer information finns i [Översikt över Azure AD Authentication Methods API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

---

### <a name="administrative-units-public-preview"></a>Den offentliga för hands versionen av administrativa enheter

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Med administrativa enheter kan du bevilja administratörs behörigheter som är begränsade till en avdelning, region eller något annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå. En användar konto administratör kan till exempel uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i sin administrativa enhet.

Med hjälp av administrativa enheter kan en central administratör:

- Skapa en administrativ enhet för decentraliserad hantering av resurser
- Tilldela en roll med administrativ behörighet enbart över Azure AD-användare i en administrativ enhet
- Fyll i de administrativa enheterna med användare och grupper efter behov

Mer information finns i [hantering av administrativa enheter i Azure Active Directory (för hands version)](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Inbyggda roller för skrivar administratör och skrivar tekniker

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

**Skrivar administratör**: användare med den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivar konfigurationer i Microsofts Universal Print-lösning, inklusive inställningar för Universal Print Connector. De kan godkänna alla delegerade utskrifts behörighets begär Anden. Skrivar administratörer har även åtkomst till utskrifts rapporter. 

**Skrivar tekniker**: användare med den här rollen kan registrera skrivare och hantera skrivar status i Microsoft Universal Print-lösningen. De kan också läsa all anslutnings information. Viktiga aktiviteter en skrivare tekniker kan inte ange användar behörigheter för skrivare och dela skrivare. [Läs mer.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Inbyggd rollen hybrid identitets administratör

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Användare med den här rollen kan aktivera, konfigurera och hantera tjänster och inställningar för att aktivera hybrid identitet i Azure AD. Den här rollen ger möjlighet att konfigurera Azure AD till någon av de tre autentiseringsmetoder som stöds&#8212;(PHS), direktautentisering (PTA) eller Federation (AD FS eller tredjepartsleverantörer) &#8212;och för att distribuera relaterad lokal infrastruktur för att aktivera dem. Lokal infrastruktur innehåller etablerings-och PTA-agenter. Den här rollen ger möjlighet att aktivera sömlös enkel Sign-On (S-SSO) för att möjliggöra sömlös autentisering på icke-Windows 10-enheter eller datorer som inte är Windows Server 2016. Dessutom ger den här rollen möjlighet att se inloggnings loggar och för att få åtkomst till hälso-och analys funktioner för övervakning och fel sökning. [Läs mer.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Inbyggd nätverks administratörs roll

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Användare med den här rollen kan granska rekommendationer för nätverks perimeter arkitektur från Microsoft som baseras på telemetri från användarens platser. Nätverks prestanda för Microsoft 365 är beroende av en noggrann nätverks gräns arkitektur för företags nätverk, vilket vanligt vis är specifika för användare. Med den här rollen kan du redigera identifierade användar platser och konfigurera nätverks parametrar för de platserna för att under lätta förbättrad telemetri och bättre design rekommendationer. [Läs mer.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Mass aktivitet och nedladdningar i Azure AD Admin Portal-upplevelsen

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:** Katalogen

Nu kan du utföra Mass aktiviteter för användare och grupper i Azure AD genom att ladda upp en CSV-fil i Azure AD Admin Portal-upplevelsen. Du kan skapa användare, ta bort användare och bjuda in gäst användare. Och du kan lägga till och ta bort medlemmar från en grupp.

Du kan också hämta listor över Azure AD-resurser från Azure AD Admin Portal-upplevelsen. Du kan hämta listan över användare i katalogen, listan över grupper i katalogen och medlemmarna i en viss grupp.

Om du vill ha mer information kan du läsa följande:

- [Skapa användare](../enterprise-users/users-bulk-add.md) eller [Bjud in gäst användare](../external-identities/tutorial-bulk-invite.md)
- [Ta bort användare](../enterprise-users/users-bulk-delete.md) eller [Återställ borttagna användare](../enterprise-users/users-bulk-restore.md)
- [Hämta lista över användare](../enterprise-users/users-bulk-download.md) eller [Hämta lista över grupper](../enterprise-users/groups-bulk-download.md)
- [Lägg till (importera) medlemmar](../enterprise-users/groups-bulk-import-members.md) eller [ta bort medlemmar](../enterprise-users/groups-bulk-remove-members.md) eller [Hämta en lista över medlemmar](../enterprise-users/groups-bulk-download-members.md) för en grupp

---

### <a name="my-staff-delegated-user-management"></a>Min personal har delegerat användar hantering

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:**

Min personal ger firstline-ansvariga, till exempel en butiks chef, för att säkerställa att deras personal medlemmar kan komma åt sina Azure AD-konton. I stället för att förlita dig på en central helpdesk kan organisationer delegera vanliga uppgifter, till exempel att återställa lösen ord eller ändra telefonnummer till en firstline Manager. Med min personal kan en användare som inte har åtkomst till sitt konto återfå åtkomst på bara några klick, utan supportavdelningen eller IT-personal som krävs. Mer information finns i [Hantera dina användare med min personal (för hands version)](../roles/my-staff-configure.md) och [delegera användar hantering med min personal (för hands version)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>En uppgraderad slut användar upplevelse i åtkomst granskningar

**Typ:** Ändrad funktion

**Tjänste kategori:** Åtkomst granskningar

**Produkt kapacitet:** Identitets styrning

Vi har uppdaterat gransknings upplevelsen för åtkomst granskningar av Azure AD i portalen Mina appar. I slutet av april visas en banderoll som gör det möjligt för dina granskare som är inloggade i Azure AD Access granskare att se en banderoll som gör det möjligt för dem att testa den uppdaterade upplevelsen i min åtkomst. Observera att de uppdaterade åtkomst granskningarna har samma funktioner som den aktuella upplevelsen, men med ett förbättrat användar gränssnitt ovanpå nya funktioner som gör att användarna kan vara produktiva. [Du kan lära dig mer om den uppdaterade upplevelsen här](../governance/perform-access-review.md). Den här allmänna för hands versionen kommer att gälla till slutet av juli 2020. I slutet av juli kommer granskare som inte har valt förhands gransknings upplevelsen automatiskt att dirigeras till min åtkomst för att utföra åtkomst granskningar. Om du vill att granskarna ska växlas över till för hands versionen i min åtkomst nu, gör du [en begäran här](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Arbets dag för inkommande användar etablering och tillbakaskrivning av appar stöder nu de senaste versionerna av webb tjänstens API för arbets dagar

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** 

Baserat på kundfeedback har vi nu uppdaterat inetablerings-och tillbakaskrivning av workday-appar i Enterprise App-galleriet för att stödja de senaste versionerna av WWS-API: et för webb tjänster (Workday). Med den här ändringen kan kunderna ange den WWS-API-version som de vill använda i anslutnings strängen. Detta ger kunderna möjlighet att hämta fler HR-attribut som är tillgängliga i alla versioner av arbets dagen. Tillbakaskrivning-appen för Workday använder nu den rekommenderade Change_Work_Contact_Info Workday-webbtjänsten för att lösa Maintain_Contact_Infos begränsningar.

Om ingen version anges i anslutnings strängen fortsätter inkommande Provisioning-appar som standard att använda WWS v-21.1 för att växla till de senaste Workday-API: erna för inkommande användar etablering, kunder måste uppdatera anslutnings strängen som dokumenteras [i självstudien](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) och även uppdatera de XPath-attribut som används för Workday-attribut enligt beskrivningen i [referens hand boken för Workday-attributet](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Om du vill använda det nya API: t för tillbakaskrivning behöver du inte göra några ändringar i programetablerings appen för Workday-tillbakaskrivning. På sidan arbets dag ser du till att användar kontot för ISU-kontot (Workday) har behörighet att anropa Change_Work_Contact affärs process som dokumenteras i avsnittet självstudie, [Konfigurera säkerhets princip för affärs processer](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Vi har uppdaterat vår [själv studie guide](../saas-apps/workday-inbound-tutorial.md) för att återspegla den nya stöd för API-versionen.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Användare med rollen standard åtkomst är nu inom omfånget för etablering

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Tidigare har användare med standard åtkomst rollen utanför definitions området för etablering. Vi har hört feedback om att kunderna vill att användare med den här rollen ska omfattas av etableringen. Från och med 16 april 2020 låter alla nya etablerings konfigurations användare med standard åtkomst rollen tillhandahållas. Gradvis kommer vi att ändra beteendet för befintliga etablerings konfigurationer som stöder etablering av användare med den här rollen. [Läs mer.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Uppdaterat etablerings gränssnitt

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Vi har uppdaterat vår etablerings upplevelse för att skapa en mer fokuserad hanterings vy. När du navigerar till etablerings bladet för ett företags program som redan har kon figurer ATS kan du enkelt övervaka förloppet för etablering och hantering av åtgärder som att starta, stoppa och starta om etableringen. [Läs mer.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Verifiering av dynamisk grupp regel är nu tillgängligt för offentlig för hands version

**Typ:** Ändrad funktion

**Tjänste kategori:** Grupp hantering

**Produkt kapacitet:** Samarbete

Azure Active Directory (Azure AD) ger nu möjlighet att verifiera dynamiska grupp regler. På fliken **validera regler** kan du verifiera din dynamiska regel mot exempel grupp medlemmar för att bekräfta att regeln fungerar som förväntat. När du skapar eller uppdaterar dynamiska grupp regler vill administratörer veta om en användare eller enhet kommer att vara medlem i gruppen. Detta hjälper till att utvärdera om en användare eller enhet uppfyller regel villkoren och hjälper till med fel sökning när medlemskap inte förväntas.

Mer information finns i [Validera en regel för dynamisk grupp medlemskap (för hands version)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identifiera säkra Poäng – säkerhets inställningar och uppdatering av MFA-förbättringar

**Typ:** Ändrad funktion

**Tjänste kategori:** EJ TILLÄMPLIGT

**Produkt kapacitet:** & skydd för identitets säkerhet

**Stöd för säkerhets inställningar för Azure AD-förbättringar:** Microsofts säkra poäng kommer att uppdatera förbättrings åtgärder för att ge stöd för [säkerhets inställningar i Azure AD](./concept-fundamentals-security-defaults.md), vilket gör det enklare att skydda din organisation med förkonfigurerade säkerhets inställningar för vanliga attacker. Detta kommer att påverka följande förbättrings åtgärder:

- Se till att alla användare kan slutföra Multi-Factor Authentication för säker åtkomst
- Kräv MFA för administrativa roller
- Aktivera princip för att blockera äldre autentisering
 
**Uppdatering av MFA-förbättringar:** För att avspegla behovet av företag för att säkerställa den högsta säkerheten vid användning av principer som fungerar med deras verksamhet har Microsoft Secure score tagit bort tre förbättringar av förbättringarna i mitten av Multi-Factor Authentication och lagt till två.

Borttagna förbättrings åtgärder:

- Registrera alla användare för Multi-Factor Authentication
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Ytterligare förbättrings åtgärder:

- Se till att alla användare kan slutföra Multi-Factor Authentication för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringar kräver att du registrerar dina användare eller administratörer för Multi-Factor Authentication (MFA) i din katalog och upprättar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Det kan vara form av att ha flera principer som tillämpar definitions områden, eller ställa in säkerhets inställningar (från och med den 16 mars) som gör att Microsoft bestämmer sig för att utmana användare för MFA. [Läs mer om vad som är nytt i Microsofts säkra Poäng](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Mars 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Ohanterade Azure Active Directory konton i B2B-uppdatering för mars 2021

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
**Från och med den 31 mars 2021** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure Active Directory-konton (Azure AD) och klienter för B2B-samarbets scenarier. Vi rekommenderar att du väljer att [e-posta autentisering med eng ång slö sen ord](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Användare med standard åtkomst rollen är inom omfånget för etablering

**Typ:** Planera för ändring  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Tidigare har användare med standard åtkomst rollen utanför definitions området för etablering. Vi har hört feedback om att kunderna vill att användare med den här rollen ska omfattas av etableringen. Vi arbetar med att distribuera en ändring så att alla nya etablerings konfigurationer tillåter användare med standard åtkomst rollen som ska tillhandahållas. Gradvis kommer vi att ändra beteendet för befintliga etablerings konfigurationer som stöder etablering av användare med den här rollen. Ingen kund åtgärd krävs. Vi publicerar en uppdatering av vår [dokumentation](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) när den här ändringen är på plats.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samarbete är tillgängligt i Microsoft Azure som drivs av 21Vianet-klienter (Azure Kina 21Vianet)

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Funktionerna i Azure AD B2B-samarbete kommer att göras tillgängliga i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet), vilket gör det möjligt för användare i en Azure Kina 21Vianet-klient att samar beta sömlöst med användare i andra Azure Kina 21Vianet-klienter. [Lär dig mer om Azure AD B2B-samarbete](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Inbjudan till Azure AD B2B-samarbete e-postdesign

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
[E-postmeddelanden](../external-identities/invitation-email-elements.md) som skickas av Azure AD B2B Collaboration-Inbjudnings tjänsten för att bjuda in användare till katalogen, kommer att omutformas för att göra Inbjudnings informationen och användarens nästa steg tydligare.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery princip ändringar visas i gransknings loggarna

**Typ:** Fastsatt  
**Tjänste kategori:** Händelse  
**Produkt kapacitet:** Övervaka & rapportering
 
Vi har åtgärdat ett fel där ändringar i [HomeRealmDiscovery-principen](../manage-apps/configure-authentication-for-federated-users-portal.md) inte inkluderades i gransknings loggarna. Du kommer nu att kunna se när och hur principen ändrades, och av vem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – mars 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I mars 2020 har vi lagt till dessa 51 nya appar med stöd för federation i app-galleriet: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho, en Kina](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML-App](../saas-apps/profitco-saml-app-tutorial.md), [iPoint-tjänsteleverantör](../saas-apps/ipoint-service-provider-tutorial.md), [Contexxt.AI-sfär](https://contexxt-sphere.com/login), [ingengörskunskap av Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [överstrålning, digitalt signerat](https://spark-dev.pixelnebula.com/login), [Logz.io – molnets användarvänlighet för ingenjörer](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SPECTRUMU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano SSO](../saas-apps/elqano-sso-tutorial.md), [MarketSignShare](http://www.signshare.com/), CrossKnowledge [Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision kompositioner](../saas-apps/netvision-compas-tutorial.md), [FCM Hub](../saas-apps/fcm-hub-tutorial.md), [revben A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](../saas-apps/golinks-tutorial.md), [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [](https://demo.asterapp.io/login) [hiss](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md) [, WatchTeams och Aster](https://www.devfinition.com/), [kompetens arbets flöde](../saas-apps/skills-workflow-tutorial.md) [,](https://admin.nodeinsight.com/AADLogin.aspx) [IP-plattform](../saas-apps/ip-platform-tutorial.md), [insikt](../saas-apps/invision-tutorial.md), [PipeDrive](../saas-apps/pipedrive-tutorial.md), [Showcase-workshop](https://app.showcaseworkshop.com/), [GreenLight integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [GreenLight-kompatibel åtkomst hantering](../saas-apps/greenlight-compliant-access-management-tutorial.md), [grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md) [, AskYourTeam, TruNarrative](../saas-apps/askyourteam-tutorial.md) [, Smartwaiver](../saas-apps/trunarrative-tutorial.md), [Bizagi Studio för digital process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX](https://www.insuite.jp/) [, Sybo](https://www.systexsoftware.com.tw/) [, Britive](../saas-apps/britive-tutorial.md) [,](../saas-apps/whosoffice-tutorial.md) [WhosOffice,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [E-dagar](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [PlayVox](https://my.playvox.com/login), [korn fartyg 360](../saas-apps/korn-ferry-360-tutorial.md), [Campus kafé](../saas-apps/campus-cafe-tutorial.md) [, Catchpoint](../saas-apps/catchpoint-tutorial.md) [, Code42](../saas-apps/code42-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samarbete tillgängligt i Azure Government-klienter

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Samarbets funktionerna i Azure AD B2B är nu tillgängliga mellan vissa Azure Government klienter.  Om du vill ta reda på om din klient kan använda de här funktionerna följer du anvisningarna på [Hur vet jag om B2B-samarbete är tillgängligt i min Azure amerikanska myndighets klient?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integration för Azure-loggar är nu tillgänglig i Azure Government

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure Monitor integrering med Azure AD-loggar finns nu i Azure Government. Du kan skicka Azure AD-loggar (gransknings-och inloggnings loggar) till ett lagrings konto, Event Hub och Log Analytics. Läs igenom den [detaljerade dokumentationen](../reports-monitoring/concept-activity-logs-azure-monitor.md) [och distributions planer för rapportering och övervakning](../reports-monitoring/plan-monitoring-and-reporting.md) av Azure AD-scenarier.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Uppdatera identitets skydd i Azure Government

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet

Vi är glada att kunna dela att vi nu har lanserat den uppdaterade [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)   upplevelsen på [Microsoft Azure Government portalen](https://portal.azure.us/). Mer information finns i [blogg inlägget för meddelande](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Haveri beredskap: Hämta och lagra etablerings konfigurationen

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten innehåller en omfattande uppsättning konfigurations funktioner. Kunderna måste kunna spara sin konfiguration så att de kan referera till den senare eller återställa till en känd version. Vi har lagt till möjligheten att ladda ned din etablerings konfiguration som en JSON-fil och ladda upp den när du behöver den. [Läs mer](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (lösen ords återställning via självbetjäning) kräver nu två portar för administratörer i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet) 

**Typ:** Ändrad funktion  
**Tjänst kategori:** Self-Service återställning av lösen ord  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Tidigare i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet), administratörer som använder självbetjäning för återställning av lösen ord (SSPR) för att återställa sina egna lösen ord behövde bara en "grind" (Challenge) för att bevisa sin identitet. I offentliga och andra nationella moln måste administratörerna i allmänhet använda två portar för att bevisa sin identitet när de använder SSPR. Men eftersom vi inte har stöd för SMS eller telefonsamtal i Azure Kina 21Vianet, tillät vi ett-Gate lösen ords återställning av administratörer.

Vi skapar SSPR funktions paritet mellan Azure Kina 21Vianet och det offentliga molnet. Administratörerna måste använda två portar när de använder SSPR. SMS, telefonsamtal och meddelandeautentisering och-koder kommer att stödjas. [Läs mer](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Lösen ords längden är begränsad till 256 tecken

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
För att säkerställa tillförlitligheten för Azure AD-tjänsten är användar lösen ord nu begränsade till 256 tecken. Användare med lösen ord som är längre än detta kommer att uppmanas att ändra sina lösen ord vid efterföljande inloggningar, antingen genom att kontakta deras administratör eller genom att använda funktionen för lösen ords återställning via självbetjäning.

Den här ändringen aktiverades den 13 mars 2020, 10 PST (18:00 UTC) och felet är AADSTS 50052, InvalidPasswordExceedsMaxLength. Mer information finns i meddelandet om att [bryta ändring](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Inloggnings loggarna för Azure AD är nu tillgängliga för alla kostnads fria klienter via Azure Portal

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Nu kan kunder som har kostnads fria klient organisationer komma åt [inloggnings loggarna för Azure AD från Azure Portal](../reports-monitoring/concept-sign-ins.md) i upp till 7 dagar. Tidigare var inloggnings loggar bara tillgängliga för kunder med Azure Active Directory Premium licenser. Med den här ändringen kan alla klienter komma åt dessa loggar via portalen.

> [!NOTE]
> Kunderna behöver fortfarande en Premium licens (Azure Active Directory Premium P1 eller P2) för att komma åt inloggnings loggarna via Microsoft Graph-API och Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Utfasning av alternativ för katalog över hela gruppen från grupper allmänna inställningar på Azure Portal

**Typ:** Föråldrad  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

För att ge kunderna ett mer flexibelt sätt att skapa katalog grupper som bäst uppfyller deras behov har vi ersatt alternativet för **katalog grupper** **från gruppen**  >  **allmänna** inställningar i Azure Portal med en länk till [dynamisk grupp dokumentation](../enterprise-users/groups-dynamic-membership.md). Vi har förbättrat vår dokumentation för att inkludera fler instruktioner så att administratörer kan skapa grupper med alla användare som inkluderar eller undantar gäst användare.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Kommande ändringar i anpassade kontroller

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Vi planerar att ersätta den aktuella för hands versionen av anpassade kontroller med en metod som gör det möjligt att samar beta med de funktioner som tillhandahålls av partner för att arbeta smidigt med Azure Active Directory administratör och slutanvändarens upplevelse. I dag är partner MFA-lösningarna riktade mot följande begränsningar: de fungerar bara när ett lösen ord har angetts. de fungerar inte som MFA för stegvis autentisering i andra nyckel scenarier. och de integreras inte med funktionerna för hantering av slutanvändare och administration av autentiseringsuppgifter. Den nya implementeringen tillåter att samarbets faktorer som tillhandahålls av partner arbetar tillsammans med inbyggda faktorer för viktiga scenarier, inklusive registrering, användning, MFA-anspråk, stegvis autentisering, rapportering och loggning. 

Anpassade kontroller fortsätter att stödjas i för hands versionen tillsammans med den nya designen tills den når allmän tillgänglighet. Vid det här skedet ger vi kunderna tid att migrera till den nya designen. På grund av begränsningarna i den aktuella metoden kommer vi inte att publicera nya leverantörer förrän den nya designen är tillgänglig. Vi arbetar nära kunder och leverantörer och kommer att kommunicera med tids linjen medan vi får närmare. [Läs mer](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identifiera säkra Poäng – MFA Improvement åtgärd uppdateringar

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
För att avspegla behovet av företag för att säkerställa den högsta säkerheten vid användning av principer som fungerar med deras verksamhet, tar Microsoft Secures score bort tre förbättrings åtgärder som är centrerade kring Multi-Factor Authentication (MFA) och lägger till två.

Följande förbättrings åtgärder kommer att tas bort:

- Registrera alla användare för MFA
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Följande förbättrings åtgärder kommer att läggas till:

- Se till att alla användare kan slutföra MFA för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringar kräver att du registrerar användare eller administratörer för MFA i din katalog och hur du skapar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Detta kan vara en form av att ställa in säkerhets inställningar som gör att Microsoft kan avgöra när användare ska kunna använda MFA eller ha flera principer som tillämpar besluts fattandet i definitions området. Som en del av dessa uppdateringar av förbättrings åtgärden kommer principer för bas linje skydd inte längre att ingå i beräknings beräkningar. [Läs mer om vad som kommer i Microsofts säkra Poäng](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Val av Azure AD Domain Services SKU

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services
 
Vi har hört feedback som Azure AD Domain Services kunder vill ha mer flexibilitet när de väljer prestanda nivåer för sina instanser. Från och med den 1 februari 2020 bytte vi från en dynamisk modell (där Azure AD fastställer prestanda-och pris nivån baserat på antal objekt) till en egen markerings modell. Nu kan kunderna välja en prestanda nivå som matchar deras miljö. Den här ändringen gör det också möjligt för oss att aktivera nya scenarier som resurs skogar och Premium funktioner som dagliga säkerhets kopieringar. Antalet objekt är nu obegränsat för alla SKU: er, men vi fortsätter att erbjuda förslag på antal objekt för varje nivå.

**Ingen omedelbar kund åtgärd krävs.** För befintliga kunder fastställer den dynamiska nivån som användes den 1 februari 2020 den nya standard nivån. Det finns inga priser eller prestanda som påverkar resultatet av den här ändringen. Azure AD DS-kunder kommer att behöva utvärdera prestanda krav när deras katalog storlek och arbets belastnings egenskaper ändras. Växling mellan tjänst nivåer fortsätter att vara en åtgärd utan avbrott och vi kommer inte längre att automatiskt flytta kunder till nya nivåer baserat på katalogens tillväxt. Dessutom kommer det inte att finnas några prisökningar och den nya prissättningen kommer att justeras mot vår aktuella fakturerings modell. Mer information finns i dokumentationen för [Azure AD DS SKU](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) och [sidan Azure AD Domain Services prissättning](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I februari 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[IamIP patent Platform](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [ns1 SSO för Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda e-mail Security Service](https://ess.barracudanetworks.com/sso/azure), [ABA repor ting](https://myaba.co.uk/client-access/signin/auth/msad), [i händelse av kris – online portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [BIC Cloud design](../saas-apps/bic-cloud-design-tutorial.md), [Biodlings Azure AD data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [Korns](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada-kommando](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB navigera](../saas-apps/eab-navigate-tutorial.md), [New Relic (begränsad utgåva)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [Tick Manager](../saas-apps/ticketmanager-tutorial.md), [mall väljare för team](https://links.officeatwork.com/templatechooser-download-teams), [bin](https://www.beesy.me/index.php/site/login), [hälso support system](../saas-apps/health-support-system-tutorial.md), [MURAL](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), Wakelet, [](https://wakelet.com/login)Firmex [vdr](../saas-apps/firmex-vdr-tutorial.md), [ThingLink för lärare och skolor](https://www.thinglink.com/) [,](../saas-apps/teamviewer-tutorial.md) [CODA](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md) [, InvitePeople,](https://invitepeople.com/login) [,](../saas-apps/reprints-desk-article-galaxy-tutorial.md)

 
Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-stöd för FIDO2 säkerhets nycklar i hybrid miljöer

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Vi presenterar den offentliga för hands versionen av Azure AD-stöd för FIDO2-säkerhetsnycklar i hybrid miljöer. Användare kan nu använda FIDO2-säkerhetsnycklar för att logga in på sina hybrid Azure AD-anslutna Windows 10-enheter och få sömlös inloggning till sina lokala och molnbaserade resurser. Stöd för Hybrid miljöer har varit den mest efterfrågade funktionen från våra kunder med lösen ords skydd eftersom vi ursprungligen startade den offentliga för hands versionen för FIDO2-stöd i Azure AD-anslutna enheter. Autentisering utan lösen ord med avancerade tekniker som biometrik och offentlig/privat nyckel kryptering ger bekvämlighet och enkel användning medan de är säkra. Med den här offentliga för hands versionen kan du nu använda modern autentisering som FIDO2-säkerhetsnycklar för att komma åt traditionella Active Directory-resurser. Mer information finns [på SSO till lokala resurser](../authentication/howto-authentication-passwordless-security-key-on-premises.md). 

Kom igång genom att gå till [Aktivera FIDO2-säkerhetsnycklar för din klient](../authentication/howto-authentication-passwordless-security-key.md) för stegvisa anvisningar. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Den nya mitt konto upplevelsen är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Mitt konto, det enda steget för att hantera slut användar kontots behov, är nu allmänt tillgängligt! Slutanvändare kan komma åt den här nya webbplatsen via URL eller i rubriken för den nya Mina appar-upplevelsen. Lär dig mer om alla självbetjänings funktioner som den nya upplevelsen erbjuder i [min konto Portal översikt](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Min konto webbplats-URL uppdateras till myaccount.microsoft.com

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Den nya slut användar upplevelsen för mitt konto kommer att uppdatera URL: en till `https://myaccount.microsoft.com` Nästa månad. Hitta mer information om upplevelsen och alla självbetjänings funktioner som det erbjuder för slutanvändare i [mitt konto Portal hjälp](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Den nya portalen för Mina appar är nu allmänt tillgänglig

**Typ:** Planera för ändring  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Slut användar upplevelser
 
Uppgradera din organisation till den nya My Apps-portalen som nu är allmänt tillgänglig! Mer information om den nya portalen och samlingar finns i [skapa samlingar på mina apps-portalen](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbets ytor i Azure AD har bytt namn till samlingar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Mina appar   
**Produkt kapacitet:** Slut användar upplevelser
 
Arbets ytor, filter administratörer kan konfigurera för att organisera sina användares appar, kommer nu att kallas samlingar. Mer information om hur du konfigurerar dem finns i [skapa samlingar på mina apps-portalen](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefonin loggning och inloggning med anpassad princip (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Med telefonnummer för registrering och inloggning kan utvecklare och företag låta sina kunder registrera sig och logga in med ett eng ång slö sen ord som skickas till användarens telefonnummer via SMS. Den här funktionen gör det också möjligt för kunden att ändra sina telefonnummer om de förlorar åtkomsten till sin telefon. Med kraften i anpassade principer och telefon registrering och inloggning kan utvecklare och företag kommunicera sitt varumärke genom sidan anpassning. Ta reda på hur du [ställer in telefonin loggning och inloggning med anpassade principer i Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I januari 2020 har vi lagt till dessa 33 nya appar med stöd för federation i app-galleriet: 

[JOSA](../saas-apps/josa-tutorial.md), [snabbt Edge-moln](../saas-apps/fastly-edge-cloud-tutorial.md), [terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [LeaveBot](../saas-apps/upshotly-tutorial.md), [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions, SmartWork](../saas-apps/tripactions-tutorial.md), [Dotcom, SSOGEN](../saas-apps/dotcom-monitor-tutorial.md), [EBS,](https://appsource.microsoft.com/en-us/product/office/WA200001175),,, [,](https://www.intumit.com/teams-smartwork/)- [Azure AD SSO Gateway för Oracle E-Business Suite-, JDE](../saas-apps/ssogen-tutorial.md), [VÄRDBASERAD MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), driver- [Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [TalentSoft](../saas-apps/talentsoft-tutorial.md), [SmartDB för Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient Driver Manager-programvara](../saas-apps/maxient-conduct-manager-software-tutorial.md), [helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/) [,, Squelch](https://portal.coreview.com/)Cloud Office365 Connector, [PingFlow Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), Akari [Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Två nya identifieringar av identitets skydd

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Vi har lagt till två nya inloggnings typer som är länkade till identitets skydd: misstänkta regler för att ändra Inkorgen och omöjlig resa. De här offline-identifieringarna upptäcks av Microsoft Cloud App Security (MCAS) och påverkar användaren och inloggnings risken i identitets skyddet. Mer information om dessa identifieringar finns i våra [typer av inloggnings risker](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Bryta ändring: URI-fragment kommer inte att transporteras via omdirigeringen för inloggning

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Från och med den 8 februari 2020 lägger tjänsten till ett tomt fragment i begäran när en begäran skickas till login.microsoftonline.com för att logga in en användare.  Detta förhindrar en klass av omdirigerings attacker genom att se till att webbläsaren rensar alla befintliga fragment i begäran. Inget program bör ha ett beroende på detta beteende. Mer information finns i avsnittet om att [dela upp ändringar](../develop/reference-breaking-changes.md#february-2020) i dokumentationen för Microsoft Identity Platform.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrera SAP SuccessFactors-etablering i Azure AD och on-premises AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Nu kan du integrera SAP-SuccessFactors som en auktoritativ identitets källa i Azure AD. Den här integreringen hjälper dig att automatisera livs cykeln för slut punkt till slut punkt, inklusive användning av HR-baserade händelser, t. ex. nya anställningar eller uppsägningar, för att styra etableringen av Azure AD-konton.

Mer information om hur du konfigurerar SAP SuccessFactors inkommande etablering i Azure AD finns i själv studie kursen [Konfigurera SAP-SuccessFactors automatisk etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Stöd för anpassade e-postmeddelanden i Azure AD B2C (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du använda Azure AD B2C för att skapa anpassade e-postmeddelanden när användarna registrerar sig för att använda dina appar. Genom att använda DisplayControls (för närvarande i för hands version) och en e-postprovider från tredje part (till exempel, [SendGrid](https://sendgrid.com/), [Spark post](https://sparkpost.com/)eller en anpassad REST API), kan du använda din egen e-postmall, **från** adress och ämnes text, samt stöd för lokalisering och anpassad eng ång slö sen ord.

Mer information finns i [anpassad e-postverifiering i Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Utbyte av bas linje principer med säkerhets inställningar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Identitets säkerhet och skydd

Som en del av en säker modell som är säker för autentisering tar vi bort befintliga principer för bas linje skydd från alla klienter. Den här borttagningen är avsedd för slut för ande i slutet av februari. Ersättningen för de här bas linje skydds principerna är säkerhets inställningar. Om du har använt principer för bas linje skydd måste du planera att flytta till den nya säkerhets standard principen eller villkorlig åtkomst. Om du inte har använt de här principerna finns det ingen åtgärd att vidta.

Mer information om de nya säkerhets standarderna finns i [Vad är säkerhets inställningar?](./concept-fundamentals-security-defaults.md) Mer information om principer för villkorlig åtkomst finns i [vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för SameSite-attributet och Chrome 80

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Som en del av en säker standard modell för cookies, ändrar Chrome 80-webbläsaren hur den behandlar cookies utan `SameSite` attributet. Alla cookies som inte anger `SameSite` attributet behandlas som om det var inställt på `SameSite=Lax` , vilket leder till att Chrome blockerar vissa scenarier mellan domänens cookie-delning som din app kan vara beroende av. Om du vill behålla det äldre Chrome-beteendet kan du använda `SameSite=None` attributet och lägga till ytterligare ett `Secure` attribut, så att cookies från flera platser bara kan nås via HTTPS-anslutningar. Chrome har schemalagts för att slutföra den här ändringen senast den 4 februari 2020.

Vi rekommenderar att alla utvecklare testar sina appar med hjälp av den här vägledningen:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ange standardvärdet för attributet **SameSite** till **none**.

- Lägg till ytterligare ett `SameSite` attribut för **Secure**.

Mer information finns i [kommande SameSite cookie-ändringar i ASP.net och ASP.net Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) och [potentiella avbrott till kund webbplatser och Microsofts produkter och tjänster i Chrome version 79 och senare](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Ny snabb korrigering för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Fastsatt  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel

Det finns ett samlat snabb korrigerings paket (build 4.6.34.0) för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här samlade paketet löser problem och lägger till förbättringar som beskrivs i avsnittet "problem som åtgärd ATS och förbättringar som lagts till i den här uppdateringen".

Mer information och hämta snabb korrigerings paketet finns [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Samlad uppdatering är tillgänglig](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Ny rapport för AD FS app-aktivitet som hjälper till att migrera appar till Azure AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Använd aktivitets rapporten ny Active Directory Federation Services (AD FS) (AD FS) i Azure Portal för att identifiera vilka av dina appar som kan migreras till Azure AD. Rapporten visar alla AD FS appar för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om att förbereda enskilda appar för migrering.

Mer information finns i [använda rapporten AD FS program aktivitet för att migrera program till Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nytt arbets flöde för användare som begär administratörs medgivande (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Access Control

Det nya administratörs godkännande arbets flödet ger administratörer ett sätt att bevilja åtkomst till appar som kräver administratörs godkännande. Om en användare försöker få åtkomst till en app, men inte kan ge samtycke, kan de nu skicka en begäran om administratörs godkännande. Begäran skickas via e-post och placeras i en kö som är tillgänglig från Azure Portal, till alla administratörer som har angetts som granskare. När en granskare har åtgärd ATS för en väntande begäran, meddelas de begär ande användarna om åtgärden.

Mer information finns i [Konfigurera arbets flödet för administratörs medgivande (för hands version)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nya Azure AD App registrerar konfigurations upplevelsen för token för hantering av valfria anspråk (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Utvecklings miljö

Bladet ny **Azure AD App registreringar för token-konfiguration** på Azure Portal visar nu Apps-utvecklare en dynamisk lista med valfria anspråk för sina appar. Den här nya upplevelsen hjälper till att effektivisera Azure AD-App-migreringar och minimera valfria felkonfigurationer för anspråk.

Mer information finns i [tillhandahålla valfria anspråk till din Azure AD-App](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nytt arbets flöde med två stegs godkännande i hantering av Azure AD-rättigheter (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter

Vi har lanserat ett nytt arbets flöde med två steg som gör att du kan kräva att två god kännare godkänner en användares begäran till ett Access-paket. Du kan till exempel ställa in det så att den begär ande användarens chef först måste godkänna, och sedan kan du också kräva att en resurs ägare godkänner detta. Om en av god kännarna inte godkänner beviljas inte åtkomst.

Mer information finns i [Inställningar för ändring av begäran och godkännande för ett Access-paket i hantering av Azure AD-rättigheter](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdateringar av sidan Mina appar tillsammans med nya arbets ytor (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** integration från tredje part

Nu kan du anpassa hur din organisations användare visar och får åtkomst till den uppdaterade Mina appar-upplevelsen. Den här nya upplevelsen innehåller även funktionen nya arbets ytor, vilket gör det enklare för användarna att hitta och organisera appar.

Mer information om den nya upplevelsen för Mina appar och hur du skapar arbets ytor finns i [skapa arbets ytor på portalen Mina appar](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google social ID-stöd för Azure AD B2B-samarbete (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** Användarautentisering

Nytt stöd för användning av Google social ID (Gmail-konton) i Azure AD hjälper till att förenkla samarbetet för dina användare och partner. Du behöver inte längre använda dina partner för att skapa och hantera ett nytt Microsoft-särskilt konto. Microsoft Teams har nu fullständigt stöd för Google-användare på alla klienter och över de vanliga och klient-relaterade autentiserings slut punkterna.

Mer information finns i [lägga till Google som en identitets leverantör för B2B-gäst användare](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile-support för villkorlig åtkomst och enkel inloggning (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

Azure AD för Microsoft Edge på iOS och Android har nu stöd för Azure AD Single Sign-On och villkorlig åtkomst:

- **Enkel inloggning för Microsoft Edge (SSO):** Enkel inloggning är nu tillgängligt över interna klienter (till exempel Microsoft Outlook och Microsoft Edge) för alla Azure AD-anslutna appar.

- **Villkorlig åtkomst för Microsoft Edge:** Via programbaserade villkorliga åtkomst principer måste användarna använda Microsoft Intune-skyddade webbläsare, till exempel Microsoft Edge.

Mer information om villkorlig åtkomst och enkel inloggning med Microsoft Edge finns i [Microsoft Edge Mobile support för villkorlig åtkomst och enkel inloggning nu allmänt tillgängliga](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogg inlägg. Mer information om hur du konfigurerar dina klient program med hjälp av [app-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md) eller [enhets-baserad villkorlig åtkomst](../conditional-access/require-managed-devices.md)finns i [Hantera webb åtkomst med hjälp av en Microsoft Intune-princip-skyddad webbläsare](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Hantering av Azure AD-rättigheter (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter

Hantering av Azure AD-berättigande är en ny funktion för identitets styrning, som hjälper organisationer att hantera identitets-och åtkomst livs cykeln i stor skala. Den här nya funktionen hjälper till att automatisera arbets flöden för åtkomstbegäran, åtkomst tilldelningar, recensioner och förfallo datum för grupper, appar och SharePoint Online-webbplatser.

Med hantering av Azure AD-behörighet kan du effektivare hantera åtkomst både för anställda och även för användare utanför organisationen som behöver åtkomst till dessa resurser.

Mer information finns i [Vad är Azure AD-hantering av rättigheter?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part  

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace program vara](../saas-apps/officespace-software-provisioning-tutorial.md), [prioritets mat ris](../saas-apps/priority-matrix-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – november 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I november 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Flygbord](../saas-apps/airtable-tutorial.md), [HootSuite](../saas-apps/hootsuite-tutorial.md), [blå åtkomst för medlemmar (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal enkel inloggning (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [, Motus,](../saas-apps/motus-tutorial.md)MyAryaka, BlueMail [, Beedle](https://loginself1.bluemail.me/), Visma [,](../saas-apps/visma-tutorial.md) [OneDesk](../saas-apps/onedesk-tutorial.md), [foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets-idé & innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [](https://teams-web.beedle.co/#/) [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [JISC student registrering](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/) [](../saas-apps/myaryaka-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nytt och förbättrat program Galleri för Azure AD

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har uppdaterat Azure AD-programgalleriet för att göra det enklare för dig att hitta förintegrerade appar som stöder etablering, OpenID Connect och SAML på din Azure Active Directory klient.

Mer information finns i [lägga till ett program i Azure Active Directory-klienten](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Utökad längd gräns för program Rolls definition från 120 till 240 tecken

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har hört kunder om att längd gränsen för definition svärdet för app-roll i vissa appar och tjänster är för kort med 120 tecken. Som svar har vi ökat den maximala längden för roll värdes definitionen till 240 tecken.

Mer information om hur du använder programspecifika roll definitioner finns i [lägga till app-roller i programmet och ta emot dem i token](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Utfasning av identityRiskEvent-API: et för Azure AD Identity Protection risk identifieringar

**Typ:** Plan för ändrings **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Azure AD Premium P2-prenumeranter kan nu utföra komplexa frågor på Azure AD Identity Protections risk identifierings data genom att använda det nya riskDetection-API: et för Microsoft Graph. Den befintliga [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API Beta-versionen slutar att returnera data cirka **10 januari 2020**. Om din organisation använder identityRiskEvent-API: t bör du övergå till det nya riskDetection-API: et.

Mer information om det nya riskDetection-API: et finns i [referens dokumentationen för riskhantering](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för programproxy för attributet SameSite och Chrome 80

**Typ:** Plan för ändrings **tjänst kategori:** App proxy **produkt kapacitet:** Access Control

Ett par veckor före versionen av Chrome 80-webbläsaren, planerar vi att uppdatera hur programproxy-cookies behandlar attributet **SameSite** . I och med lanseringen av Chrome 80 behandlas alla cookies som inte anger attributet **SameSite** som om det var inställt på `SameSite=Lax` .

För att undvika potentiellt negativa konsekvenser på grund av den här ändringen uppdaterar vi programproxyns åtkomst och sessionscookies genom att:

- Ställer in standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ställer in standardvärdet för attributet **SameSite** till **none**.

    >[!NOTE]
    > Cookies för åtkomst till programproxyn har alltid skickats exklusivt över säkra kanaler. Dessa ändringar gäller endast för sessionscookies.

Mer information om cookies-inställningarna för programproxyn finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Appregistreringar (bakåtkompatibelt) och program hantering i program registrerings portalen (apps.dev.microsoft.com) är inte längre tillgängligt

**Typ:** Plan för ändrings **tjänst kategori:** saknas **produkt kapacitet:** utvecklings miljö

Användare med Azure AD-konton kan inte längre registrera eller hantera program med hjälp av program registrerings portalen (apps.dev.microsoft.com) eller registrera och hantera program i Appregistreringar (äldre) i Azure Portal.

Mer information om den nya Appregistreringar upplevelsen finns i [Appregistreringar i Azure Portal tränings guide](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Användare behöver inte längre registrera sig på nytt vid migrering från användarspecifika MFA till villkorlig åtkomst-baserad MFA

**Typ:** Fast **tjänste kategori:** MFA **Product Capability:** Identity Security & Protection

Vi har åtgärdat ett känt problem, vilket innebär att när användare var tvungen att omregistrera om de har inaktiverats för Multi-Factor Authentication per användare (MFA) och sedan aktive ras för MFA via en princip för villkorlig åtkomst.

Om du vill kräva att användarna registrerar om kan du välja alternativet **Omregistrera MFA** från användarens autentiseringsmetoder i Azure AD-portalen. Mer information om hur du migrerar användare från per användare MFA till villkorlig åtkomst-baserad MFA finns i [konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nya funktioner för att transformera och skicka anspråk i SAML-token

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Vi har lagt till ytterligare funktioner som hjälper dig att anpassa och skicka anspråk i SAML-token. De här nya funktionerna är:

- Ytterligare omvandlings funktioner för anspråk, vilket hjälper dig att ändra värdet som du skickar i anspråket.

- Möjlighet att tillämpa flera transformationer på ett enda anspråk.

- Möjlighet att ange anspråks källan, baserat på användar typen och gruppen som användaren tillhör.

Detaljerad information om de här nya funktionerna, inklusive hur du använder dem, finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Sidan nya inloggnings tillägg för slutanvändare i Azure AD

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** övervakning & rapportering

Vi har lagt till en ny **mina inloggnings** sida ( https://mysignins.microsoft.com) för att låta din organisations användare visa sin senaste inloggnings historik för att söka efter en ovanlig aktivitet. På den nya sidan kan användarna se:

- Om någon försöker gissa sitt lösen ord.

- Om en angripare har loggat in på kontot och från vilken plats.

- Vilka appar angriparen försökte få åtkomst till.

Mer information finns i avsnittet [användare kan nu kontrol lera sin inloggnings historik för ovanlig aktivitets](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) blogg.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrering av Azure AD Domain Services (Azure AD DS) från klassisk till Azure Resource Manager virtuella nätverk

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Till våra kunder som har fastnat i klassiska virtuella nätverk – vi har bra nyheter! Du kan nu utföra en eng ång slö flytt från ett klassiskt virtuellt nätverk till ett befintligt virtuellt Resource Manager-nätverk. När du har flyttat till det virtuella Resource Manager-nätverket kan du dra nytta av de ytterligare och uppgraderade funktionerna, till exempel detaljerade lösen ords principer, e-postaviseringar och gransknings loggar.

Mer information finns i [förhands granskning – migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Uppdateringar av layouten Azure AD B2C sid kontrakt

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Vi har introducerat några nya ändringar i version 1.2.0 av sid kontraktet för Azure AD B2C. I den här uppdaterade versionen kan du nu styra inläsnings ordningen för dina element, vilket också kan hjälpa till att stoppa flimmer som inträffar när format mal len (CSS) läses in.

En fullständig lista över de ändringar som gjorts i sidan kontrakt finns i [versions ändrings loggen](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdatera till sidan Mina appar tillsammans med nya arbets ytor (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** Access Control

Nu kan du anpassa hur din organisations användare visar och kommer åt den anpassade appen Mina appar, inklusive att använda funktionen nya arbets ytor för att göra det lättare för dem att hitta appar. Funktionen nya arbets ytor fungerar som ett filter för de appar som organisationens användare redan har åtkomst till.

Mer information om hur du utvärderar nya funktioner för Mina appar och skapar arbets ytor finns i [skapa arbets ytor på portalen Mina appar (för hands version)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Stöd för den månatliga aktiva användarbaserade fakturerings modellen (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Azure AD B2C stöder nu MAU-fakturering (Monthly Active Users). MAU-fakturering baseras på antalet unika användare med autentiserings aktivitet under en kalender månad. Befintliga kunder kan när som helst byta till den nya fakturerings metoden.

Från den 1 november 2019 debiteras alla nya kunder automatiskt med den här metoden. Den här fakturerings metoden fördelar kunder genom kostnads förmåner och möjligheten att planera framåt.

Mer information finns i [Uppgradera till månatliga aktiva användares fakturerings modell](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – oktober 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I oktober 2019 har vi lagt till dessa 35 nya appar med stöd för federation i app-galleriet:

[I händelse av kris – mobil](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno resa](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant), [OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [dynaTrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks, eCornell](../saas-apps/freshworks-tutorial.md) [, ShipHazmat](../saas-apps/ecornell-tutorial.md), Netskope, Bindtuning, HireVue,,,,, [,](../saas-apps/shiphazmat-tutorial.md) [Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [contentable](../saas-apps/contentful-tutorial.md), [](https://bindtuning.com/login), [e](https://www.hirevue.com/) [HireVue koordinat-USOnly](https://www.hirevue.com/), [HireVue-koordinat – US](https://www.hirevue.com/), [WittyParrot](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md) [, Visit.org, Cambium](../saas-apps/visitorg-tutorial.md)Xirrus [EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [Maile!](../saas-apps/mail-luck-tutorial.md), [team](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login) [, SIGNL4, EAB](https://account.signl4.com/manage) [navigera implementering](../saas-apps/eab-navigate-impl-tutorial.md), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speak e-mail for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [talad e-post för Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), iHealthHome [Care navigerings system](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Meny alternativet konsol IDE rad säkerhet i Azure AD-portalen

**Typ:** Ändring av funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Nu kan du komma åt alla tillgängliga Azure AD-säkerhetsfunktioner från det nya **säkerhets** meny alternativet och från **sök** fältet i Azure Portal. Dessutom innehåller den nya **säkerhets** landnings sidan, som kallas **säkerhets komma igång**, länkar till vår offentliga dokumentation, säkerhets vägledning och distributions guider.

Den nya **säkerhets** menyn innehåller:

- Villkorlig åtkomst
- Identity Protection
- Security Center
- Identifiera säkra Poäng för identitet
- Autentiseringsmetoder
- Multifaktorautentisering
- Risk rapporter – riskfyllda användare, riskfyllda inloggningar, risk identifiering
- Med mera...

Mer information finns i säkerhet för att [komma igång](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Utgångs princip för Office 365-grupper har förbättrats med autoförnyelse

**Typ:** Ändra funktions **tjänst kategori:** grupp hantering **produkt kapacitet:** hantering av identitets livs cykel

Utgångs principen för Office 365-grupper har förbättrats för att automatiskt förnya grupper som används aktivt av dess medlemmar. Grupper förnyas automatiskt baserat på användar aktivitet i alla Office 365-appar, inklusive Outlook, SharePoint och Teams.

Den här förbättringen hjälper till att minska antalet aviseringar för din grupp och hjälper till att se till att aktiva grupper fortfarande är tillgängliga. Om du redan har en aktiv utgångs princip för dina Office 365-grupper behöver du inte göra något för att aktivera den här nya funktionen.

Mer information finns i [Konfigurera förfallo principen för Office 365-grupper](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Uppdaterad Azure AD Domain Services (Azure AD DS)-skapande

**Typ:** Ändrad funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Vi har uppdaterat Azure AD Domain Services (Azure AD DS) för att ta med en ny och förbättrad skapande upplevelse, som hjälper dig att skapa en hanterad domän i tre klick! Dessutom kan du nu ladda upp och Distribuera Azure AD DS från en mall.

Mer information finns i [Självstudier: skapa och konfigurera en Azure Active Directory Domain Services instans](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plan för ändring: utfasning av Power BI innehålls paket

**Typ:** Plan för ändrings **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Från och med den 1 oktober 2019 börjar Power BI att ta bort alla innehålls paket, inklusive Azure AD Power BI-innehålls paketet. Som ett alternativ till detta innehålls paket kan du använda Azure AD-arbetsböcker för att få insikter om dina Azure AD-relaterade tjänster. Ytterligare arbets böcker kommer, inklusive arbets böcker om principer för villkorlig åtkomst i endast rapport läge, app-baserade insikter med mera.

Mer information om arbets böckerna finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Mer information om utfasningen av innehålls paket finns i blogg inlägget om att [presentera Power BI Template Apps allmänt tillgänglighet](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Min profil byter namn och integrerar med sidan Microsoft Office konto

**Typ:** Plan för ändrings **tjänst kategori:** min profil/konto **produkt kapacitet:** samarbete

Från och med oktober blir min profil upplevelse mitt konto. Som en del av ändringen kommer **min profil** att ändras till **mitt konto** för närvarande. Den uppdaterade upplevelsen av namn ändringen och vissa design förbättringar erbjuder ytterligare integrering med Microsoft Office konto sidan. Mer specifikt kommer du att kunna komma åt Office-installationer och-prenumerationer från sidan **översikts konto** , tillsammans med Office-relaterade kontakt inställningar på sidan **Sekretess** .

Mer information om min profil (för hands version) finns i [Översikt över min profil (för hands version)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Mass hantering av grupper och medlemmar med CSV-filer i Azure AD-portalen (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Vi är glada över att kunna meddela den offentliga för hands versionen av hanterings upplevelser för flera grupper i Azure AD-portalen. Nu kan du använda en CSV-fil och Azure AD-portalen för att hantera grupper och medlems listor, inklusive:

- Lägga till eller ta bort medlemmar från en grupp.

- Hämtar listan över grupper från katalogen.

- Hämtar listan över grupp medlemmar för en speciell grupp.

Mer information finns i avsnittet [Lägg till medlemmar](../enterprise-users/groups-bulk-import-members.md), [Mass borttagnings medlemmar](../enterprise-users/groups-bulk-remove-members.md), lista över grupp [medlemmar](../enterprise-users/groups-bulk-download-members.md)och [grupper med hämtnings grupper](../enterprise-users/groups-bulk-download.md)för grupp.

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiskt medgivande stöds nu via en ny administratörs tillstånds slut punkt

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Vi har skapat en ny administratörs medgivande slut punkt som stöder dynamiskt medgivande, vilket är användbart för appar som vill använda den dynamiska godkännande modellen på Microsoft Identity Platform.

Mer information om hur du använder den nya slut punkten finns i [using the admin medgivande Endpoint](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – september 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I september 2019 har vi lagt till dessa 29 nya appar med stöd för federation i app-galleriet:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO-åtkomst för Ethidex Compliance &trade; Office – enkel inloggning](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iserver-portalen](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur res-och utgift](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` , Arc- [anläggningar](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus-team](https://stratus.emea.luware.cloud/login), [breda idéer](https://wideideas.online/wideideas/), [Prisma-moln](../saas-apps/prisma-cloud-tutorial.md), JDLT- [klient hubb](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [sealpath Brings säker webbläsare](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma moln](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/` , `https://app.testhtm.com/settings/email-integration` , [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](../saas-apps/whitesource-tutorial.md), [värd arvs online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [CakeHR](../saas-apps/cakehr-tutorial.md), [BIS](../saas-apps/bis-tutorial.md), [COO Kai team build](https://ms-contacts.coo-kai.jp/), [SonarQube](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [identifierings förmåner SSO](../saas-apps/discovery-benefits-sso-tutorial.md) [](https://app.amelio.co/)`https://itask.yipinapp.com/`

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Ny Azure AD global läsar roll

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för Azure AD-roller: Access Control

Från den 24 september 2019 kommer vi att börja lansera en ny Azure Active Directory (AD) roll som kallas global läsare. Den här distributionen startar med produktions-och globala Cloud-kunder (GCC), som slutförs i hela världen i oktober.

Global läsar roll är den skrivskyddade motsvarigheten till global administratör. Användare med den här rollen kan läsa inställningar och administrativ information mellan Microsoft 365 tjänster, men kan inte vidta hanterings åtgärder. Vi har skapat rollen global läsare som hjälper till att minska antalet globala administratörer i din organisation. Eftersom globala administratörs konton är kraftfulla och utsatta för angrepp rekommenderar vi att du har färre än fem globala administratörer. Vi rekommenderar att du använder rollen global läsare för planering, granskningar och undersökningar. Vi rekommenderar också att du använder rollen global läsare i kombination med andra begränsade administratörs roller, t. ex. Exchange-administratör, för att få jobbet gjort utan att den globala administratörs rollen krävs.

Rollen global läsare fungerar med de nya Microsoft 365 administrations centret, administrations Center för team, team administrations Center, Security Center, Compliance Center, Azure AD administrations Center och administrations Center för enhets hantering.

>[!NOTE]
> I början av den offentliga för hands versionen fungerar inte den globala läsar rollen med: SharePoint, Privileged Access Management, Customer Lockbox, känslighets etiketter, teams livs cykel, team rapportering & samtals analys, team hantering av IP-telefonen och Teams-katalogen.

Mer information finns i [Administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Få åtkomst till en lokal rapport Server från din Power BI Mobile-app med hjälp av Azure Active Directory-programproxy

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för App-proxy: Access Control

Med den nya integreringen mellan Power BI mobilapp och Azure AD-programproxy kan du på ett säkert sätt logga in på den Power BI mobilappen och visa vilken som helst av organisationens rapporter som finns på den lokala Power BI-rapportserver.

Information om Power BI Mobile-appen, inklusive var du hämtar appen, finns på [Power BI webbplats](https://powerbi.microsoft.com/mobile/). Mer information om hur du konfigurerar Power BI mobilapp med Azure AD-programproxy finns i [aktivera fjärråtkomst till Power BI Mobile med azure AD-programproxy](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Det finns en ny version av AzureADPreview PowerShell-modulen

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** katalog

Nya cmdletar har lagts till i AzureADPreview-modulen för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Ny version av Azure AD Connect

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** katalog

Vi har släppt en uppdaterad version av Azure AD Connect för kunder med automatisk uppgradering. Den här nya versionen innehåller flera nya funktioner, förbättringar och fel korrigeringar. Mer information om den här nya versionen finns [Azure AD Connect: versions historik](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication-Server (MFA), version 8.0.2 är nu tillgänglig

**Typ:** Fast **tjänste kategori:** MFA **Product Capability:** Identity Security & Protection

Om du är en befintlig kund, som aktiverade MFA Server tidigare än den 1 juli 2019, kan du nu ladda ned den senaste versionen av MFA Server (version 8.0.2). I den här nya versionen:

- Ett problem har åtgärd ATS så när Azure AD Sync ändrar en användare från inaktive rad till aktive rad skickas ett e-postmeddelande till användaren.

- Ett problem har åtgärd ATS så att kunderna kan uppgraderas och fortsätta att använda funktionen taggar.

- Las till Kosovo (+ 383) landskod.

- En eng ång slö MultiFactorAuthSvc-loggning har lagts till i filen. log.

- Bättre prestanda för webbtjänst-SDK.

- Andra mindre buggar.

Från och med den 1 juli 2019 erbjuder Microsoft även MFA Server för nya distributioner. Nya kunder som behöver Multi-Factor Authentication bör använda molnbaserad Azure AD-Multi-Factor Authentication. Mer information finns i [Planera en molnbaserad Azure AD Multi-Factor Authentication-distribution](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Augusti 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Förbättrad sökning, filtrering och sortering för grupper är tillgängligt i Azure AD-portalen (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Vi är glada över att kunna tillkännage den offentliga för hands versionen av de förbättrade grupper som är relaterade till Azure AD-portalen. Dessa förbättringar hjälper dig att hantera grupper och medlems listor bättre genom att tillhandahålla:

- Avancerade Sök funktioner, till exempel under Strängs sökning i grupp listor.
- Avancerade alternativ för filtrering och sortering i listor över medlemmar och ägare.
- Nya Sök funktioner för medlems-och ägar listor.
- Mer exakta grupp antal för stora grupper.

Mer information finns i [hantera grupper i Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nya anpassade roller är tillgängliga för registrerings hantering av appar (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för Azure AD-roller: Access Control

Anpassade roller (tillgängliga med en Azure AD P1-eller P2-prenumeration) kan nu hjälpa dig att ge dig detaljerad åtkomst, genom att låta dig skapa roll definitioner med särskilda behörigheter och sedan tilldela rollerna till särskilda resurser. För närvarande kan du skapa anpassade roller genom att använda behörigheter för att hantera app-registreringar och sedan tilldela rollen till en speciell app. Mer information om anpassade roller finns i [anpassade administratörs roller i Azure Active Directory (för hands version)](../roles/custom-overview.md).

Om du behöver ytterligare behörigheter eller resurser som stöds, som du för närvarande inte ser, kan du skicka feedback till vår [Azure feedback-webbplats](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) så lägger vi till din begäran till vår uppdatering av väg kartan.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nya etablerings loggar kan hjälpa dig att övervaka och felsöka din app etablerings distribution (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** hantering av identitets livs cykel

Nya etablerings loggar är tillgängliga som hjälper dig att övervaka och felsöka distribution av användar-och grupp etablering. Dessa nya loggfiler innehåller information om:

- Vilka grupper har skapats i [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Vilka roller har importer ATS från [AWS Single-Account åtkomst](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Vilka medarbetare som inte har importer ATS från [Workday](../saas-apps/workday-inbound-tutorial.md)

Mer information finns i [etablerings rapporter i Azure Active Directory portal (för hands version)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nya säkerhets rapporter för alla Azure AD-administratörer (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Som standard kommer alla Azure AD-administratörer snart att kunna komma åt moderna säkerhets rapporter i Azure AD. Fram till slutet av september kommer du att kunna använda banderollen högst upp i moderna säkerhets rapporter för att återgå till gamla rapporter.

Moderna säkerhets rapporter ger ytterligare funktioner från de äldre versionerna, inklusive:

- Avancerad filtrering och sortering
- Mass åtgärder, t. ex. problem med att ignorera användar risk
- Bekräftelse av komprometterade eller säkrade entiteter
- Risk tillstånd, täcker: vid risk, avstängd, åtgärdad och bekräftat komprometterad
- Nya riskfyllda identifieringar (tillgängliga för Azure AD Premium prenumeranter)

Mer information finns i [riskfyllda användare](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [riskfyllda inloggningar](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)och [risk identifieringar](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Användare som tilldelats hanterad identitet är tillgänglig för Virtual Machines och Virtual Machine Scale Sets (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** hanterade identiteter för Azure-resurser **produkt kapacitet:** utvecklings miljö

Användare som tilldelats hanterade identiteter är nu allmänt tillgängliga för Virtual Machines och Virtual Machine Scale Sets. Som en del av detta kan Azure Skapa en identitet i Azure AD-klienten som är betrodd av den prenumeration som används och kan tilldelas till en eller flera Azure Service-instanser. Mer information om användarspecifika hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Användare kan återställa sina lösen ord med hjälp av en mobilapp eller maskinvaru-token (allmän tillgänglighet)

**Typ:** Ändring av funktions **tjänst kategori:** självbetjäning för återställning av lösen ord för **produkter:** användarautentisering

Användare som har registrerat en mobilapp med din organisation kan nu återställa sina egna lösen ord genom att godkänna ett meddelande från appen Microsoft Authenticator eller genom att ange en kod från deras mobilapp eller maskinvaru-token.

Mer information finns i [så här fungerar det: lösen ords återställning](../authentication/concept-sspr-howitworks.md)via självbetjäning i Azure AD. Mer information om användar upplevelsen finns i [återställa ditt eget arbets-eller skol lösen ord översikt](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignorerar det delade cacheminnet MSAL.NET för on-of-scenarier

**Typ:** Fast **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Från och med Azure AD Authentication Library (ADAL.NET) version 5.0.0 – för hands version måste Apps-utvecklare [serialisera en cache per konto för webbappar och webb-API: er](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). I annat fall kan vissa scenarier som använder [sig av flödet](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) för Java, tillsammans med vissa speciella användnings fall av `UserAssertion` , resultera i en höjning av behörighet. För att undvika den här säkerhets risken ignorerar ADAL.NET nu det delade cacheminnet för Microsoft Authentication Library för dotNET (MSAL.NET) för scenarier med olika förutsättningar.

Mer information om det här problemet finns i [Azure Active Directory behörighets bibliotekets utökning av behörighets sårbarhet](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – augusti 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I augusti 2019 har vi lagt till dessa 26 nya appar med stöd för federation i app-galleriet:

[Civic-plattform](../saas-apps/civic-platform-tutorial.md), [Amazon-verksamhet](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareports Inativ portal (Europa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy-närvaro](../saas-apps/academy-attendance-tutorial.md), [prioritets mat ris](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), [CPQSync av Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [leverera. Media &trade; Portal](https://portal.deliver.media), [Frontline utbildning](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [Blink](../saas-apps/blink-tutorial.md), [Vocoli](../saas-apps/vocoli-tutorial.md), ProNovos- [analys](../saas-apps/pronovos-analytics-tutorial.md) [, Sigstr,](../saas-apps/sigstr-tutorial.md) [Darwinbox,](../saas-apps/darwinbox-tutorial.md) [titta efter färger](../saas-apps/watch-by-colors-tutorial.md), [nät](../saas-apps/harness-tutorial.md)och [EAB navigera i strategiska vård](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Det finns nya versioner av AzureAD PowerShell-och AzureADPreview PowerShell-modulerna

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** katalog

Nya uppdateringar av AzureAD-och AzureAD Preview PowerShell-modulerna är tillgängliga:

- En ny `-Filter` parameter har lagts till i `Get-AzureADDirectoryRole` parametern i AzureAD-modulen. Med den här parametern kan du filtrera efter katalog roller som returneras av cmdleten.
- Nya cmdletar har lagts till i AzureADPreview-modulen för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Förbättringar av användar gränssnittet för dynamisk grupp regel verktyg i Azure Portal

**Typ:** Ändra funktions **tjänst kategori:** grupp hantering **produkt kapacitet:** samarbete

Vi har gjort några förbättringar av användar gränssnittet för dynamisk grupp regel verktyget, som är tillgängliga i Azure Portal, för att hjälpa dig att enkelt konfigurera en ny regel eller ändra befintliga regler. Med den här design förbättringen kan du skapa regler med upp till fem uttryck i stället för bara en. Vi har också uppdaterat enhets egenskaps listan för att ta bort föråldrade enhets egenskaper.

Mer information finns i [Hantera dynamiska medlemskaps regler](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Ny Microsoft Graph app-behörighet som är tillgänglig för användning med åtkomst granskningar

**Typ:** Ändrad funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** identitets styrning

Vi har introducerat en ny Microsoft Graph app-behörighet, `AccessReview.ReadWrite.Membership` som gör det möjligt för appar att automatiskt skapa och hämta åtkomst granskningar för grupp medlemskap och app-tilldelningar. Den här behörigheten kan användas av schemalagda jobb eller som en del av din automatisering, utan att det krävs någon inloggad användar kontext.

Mer information finns i exempel på [hur du skapar åtkomst granskningar för Azure AD med hjälp av Microsoft Graph app-behörigheter med PowerShell-bloggen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-aktivitets loggar är nu tillgängliga för myndighets moln instanser i Azure Monitor

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi är glada över att kunna meddela att Azure AD-aktivitets loggar nu är tillgängliga för myndighets moln instanser i Azure Monitor. Nu kan du skicka Azure AD-loggar till ditt lagrings konto eller till en Event Hub för att integrera med dina SIEM-verktyg, t. ex. [SumoLogic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)och [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Mer information om hur du konfigurerar Azure Monitor finns [i Azure AD-aktivitets loggar i Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Uppdatera dina användare till den nya, förbättrade säkerhets informations upplevelsen

**Typ:** Ändrad funktions **tjänst kategori:**  autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Den 25 september 2019 kommer vi att stänga av den gamla, icke-förbättrade säkerhets informationen för att registrera och hantera användar säkerhets information och bara aktivera den nya, [förbättrade versionen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Det innebär att användarna inte längre kommer att kunna använda den gamla upplevelsen.

Mer information om förbättrad säkerhets information finns i vår [Administratörs dokumentation](../authentication/concept-registration-mfa-sspr-combined.md) och i [användar dokumentationen](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Om du vill aktivera den här nya upplevelsen måste du:

1. Logga in på Azure Portal som global administratör eller användar administratör.

2. Gå till **Azure Active Directory > användar inställningar > hantera inställningar för för hands versions funktionerna i åtkomst panelen**.

3. I **användarna kan använda för hands versions funktioner för att registrera och hantera säkerhets information – förbättrat** utrymme, Välj **vald** och sedan välja en grupp med användare eller Välj **alla** för att aktivera den här funktionen för alla användare i klienten.

4. I * *-användarna kan använda för hands versions funktioner för registrering och hantering av säkerhet * * * * * * * * * * * * * * * * ***.**

5. Spara inställningarna.

    När du har sparat inställningarna har du inte längre till gång till den gamla säkerhets informationen.

>[!Important]
>Om du inte slutför de här stegen före den 25 september 2019 aktive ras din Azure Active Directory klient automatiskt för den förbättrade upplevelsen. Om du har frågor kan du kontakta oss på registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Autentiseringsbegäranden med POST-inloggningar blir mer strikt verifierad

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** standarder

Från och med den 2 september 2019 kommer autentiseringsbegäranden som använder POST-metoden att verifieras striktare mot HTTP-standarder. Mer specifikt kommer blank steg och dubbla citat tecken (") inte längre att tas bort från begär ande formulär värden. De här ändringarna förväntas inte konvertera några befintliga klienter och hjälper till att kontrol lera att förfrågningar som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång.

Mer information finns i meddelanden om [ändringar i Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för ändring: programproxy-tjänstens uppdatering som endast stöder TLS 1,2

**Typ:** Plan för ändrings **tjänst kategori:** App proxy **produkt kapacitet:** Access Control

För att hjälpa dig med vår starkaste kryptering kommer vi att börja begränsa åtkomsten till Application Proxy-tjänsten till endast TLS 1,2-protokoll. Den här begränsningen kommer inlednings vis att distribueras till kunder som redan använder TLS 1,2-protokoll, så att du inte ser effekten. Fullständig utfasning av TLS 1,0 och TLS 1,1-protokollen kommer att slutföras den 31 augusti 2019. Kunder som fortfarande använder TLS 1,0 och TLS 1,1 får ett avancerat meddelande om att förbereda inför den här ändringen.

För att upprätthålla anslutningen till Application Proxy-tjänsten i hela den här ändringen rekommenderar vi att du kontrollerar att kombinationerna av klient-och webb läsar servern har uppdaterats för att använda TLS 1,2. Vi rekommenderar också att du tar med alla klient system som används av dina anställda för att få åtkomst till appar som publicerats via tjänsten Application Proxy.

Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plan för ändring: design uppdateringar kommer för program galleriet

**Typ:** Plan för ändrings **tjänst kategori:** företags program **produkt kapacitet:** SSO

Nya användar gränssnitts ändringar kommer till design av bladet **Lägg till från galleriet** i bladet **Lägg till ett program** . Med de här ändringarna kan du enkelt hitta dina appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och inloggning med lösen ord (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plan för ändring: borttagning av MFA-serverns IP-adress från Office 365-IP-adressen

**Typ:** Plan för ändrings **tjänst kategori:** MFA **Product Capability:** Identity Security & Protection

Vi tar bort MFA-serverns IP-adress från [Office 365 IP-adress och URL-webbtjänst](/office365/enterprise/office-365-ip-web-service). Om du för närvarande använder dessa sidor för att uppdatera brand Väggs inställningarna måste du se till att du även inkluderar listan över IP-adresser som beskrivs i avsnittet **krav för azure Multi-Factor Authentication-Server brand vägg** i artikeln [komma igång med Azure Multi-Factor Authentication-Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Endast app-token kräver att klient programmet finns i resurs klienten

**Typ:** Fast **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller app-only-token via [tilldelningen av klientens autentiseringsuppgifter](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Tidigare kunde appar Hämta token för att anropa andra appar, oavsett om klient programmet fanns i klienten. Vi har uppdaterat det här beteendet så att resurser med en enda klient, ibland kallade webb-API: er, bara kan anropas av klient program som finns i resurs klienten.

Om din app inte finns i resurs klienten får du ett fel meddelande som säger att `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` du kan åtgärda det här problemet genom att skapa klientens huvud namn för klienten i klienten, antingen med hjälp av [Administratörs medgivande slut punkten](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) eller [via PowerShell](../develop/howto-authenticate-service-principal-powershell.md), som säkerställer att klienten har gett appen behörighet att köras i klienten.

Mer information finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Det befintliga godkännandet mellan klienten och API: et fortsätter inte att krävas. Apparna bör fortfarande utföra sina egna verifierings kontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nytt lösen ords lös inloggning till Azure AD med hjälp av FIDO2-säkerhetsnycklar

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2 säkerhets nycklar för deras organisations användare och grupper. Slutanvändare kan även registrera sina säkerhets nycklar genom att använda nycklar för att logga in på sina Microsoft-konton på webbplatser på FIDO enheter, samt logga in på sina Azure AD-anslutna Windows 10-enheter.

Mer information finns i [Aktivera lösen ords lös inloggning för Azure AD (för hands version)](../authentication/concept-authentication-passwordless.md) för administratörs information och [Konfigurera säkerhets information för att använda en säkerhets nyckel (för hands version)](../user-help/security-info-setup-security-key.md) för information om slutanvändare.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – juli 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I juli 2019 har vi lagt till dessa 18 nya appar med stöd för federation i app-galleriet:

[Ungerboeck program vara](../saas-apps/ungerboeck-software-tutorial.md), [starkt mönster Omnichannel kontakt Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [smarta Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [LOOOP](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO-åtkomst för Ethidex &trade; Compliance Office](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [djupet](../saas-apps/hype-tutorial.md), [abstract](../saas-apps/abstract-tutorial.md) [,](../saas-apps/ascentis-tutorial.md), [FlipSnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [,](../saas-apps/hyperanna-tutorial.md) [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog- [artefakting](../saas-apps/jfrog-artifactory-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: övervakning av & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federerad katalog](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ny Azure AD Domain Services service tag för nätverks säkerhets grupp

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Om du är trött på att hantera långa listor över IP-adresser och intervall kan du använda den nya **AzureActiveDirectoryDomainServices** Network Service tag i din Azure-nätverks säkerhets grupp för att skydda inkommande trafik till ditt Azure AD Domain Services virtuella nätverk under nätet.

Mer information om den här nya service tag-koden finns i [nätverks säkerhets grupper för Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nya autentiseringsmetoder användning & insikter (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** självbetjäning för återställning av lösen ord **produkt kapacitet:** övervakning & rapportering

De nya autentiseringsmetoderna för användning & insikter kan hjälpa dig att förstå hur funktioner som Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta lösen ords återställning via självbetjäning används för att återställa lösen ord och med vilken metod som återställningen sker.

Mer information finns i [användning av autentiseringsmetoder & insikter (för hands version)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhets rapporter är tillgängliga för alla Azure AD-administratörer (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Alla Azure AD-administratörer kan nu välja banderollen överst i befintliga säkerhets rapporter, till exempel användare som har **flaggats för risk** rapport, för att börja använda den nya säkerhets upplevelsen som visas i rapporterna **riskfyllda användare** och **riskfyllda inloggningar** . Med tiden kommer alla säkerhets rapporter att flyttas från äldre versioner till de nya versionerna, med de nya rapporterna som ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Mass åtgärder, t. ex. problem med att ignorera användar risk

- Bekräftelse av komprometterade eller säkrade entiteter

- Risk tillstånd, täcker: vid risk, avstängd, åtgärdad och bekräftat komprometterad

Mer information finns i rapporten om [riskfyllda användare](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) och [riskfyllda inloggningar](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B direkt Federation med SAML/WS-utfodras (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C

Direkt federationen hjälper till att göra det enklare för dig att arbeta med partner vars IT-hanterade identitets lösning inte är Azure AD, genom att arbeta med identitets system som har stöd för SAML-eller WS-Fed-standarder. När du har konfigurerat en direkt Federations relation med en partner, kan alla nya gäst användare som du bjuder in från domänen samar beta med dig som använder sitt befintliga organisations konto, vilket gör användar upplevelsen mer sömlös.

Mer information finns i [direkt Federation med AD FS och tredje parts leverantörer för gäst användare (för hands version)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: övervakning av & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federerad katalog](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ny kontroll för dubbletter av grupp namn i Azure AD-portalen

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Nu när du skapar eller uppdaterar ett grupp namn från Azure AD-portalen, kontrollerar vi om du duplicerar ett befintligt grupp namn i din resurs. Om vi bestämmer att namnet redan används av en annan grupp, blir du ombedd att ändra ditt namn.

Mer information finns i [hantera grupper i Azure AD-portalen](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svar (omdirigerings-URI: er)

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Azure AD-appar kan nu registrera och använda URI: er för svar (omdirigering) med statiska frågeparametrar (till exempel `https://contoso.com/oauth2?idp=microsoft` ) för OAuth 2,0-begäranden. Den statiska Frågeparametern omfattas av sträng matchning för svars-URI: er, precis som andra delar av svars-URI: n. Om det inte finns någon registrerad sträng som matchar URL-kodad omdirigerings-URI, avvisas begäran. Om svars-URI: n hittas används hela strängen för att omdirigera användaren, inklusive parametern för den statiska frågan.

Dynamiska svars-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och inte kan användas för att bevara tillståndsinformation i en autentiseringsbegäran. För det här ändamålet använder du `state` parametern.

För närvarande blockerar appens registrerings skärmar för Azure Portal fortfarande frågeparametrar. Du kan dock redigera appens manifest manuellt för att lägga till och testa frågeparametrar i din app. Mer information finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitets loggar (MS Graph API: er) för Azure AD är nu tillgängliga via PowerShell-cmdletar

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi är glada över att kunna meddela att Azure AD aktivitets loggar (gransknings-och inloggnings rapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API-slutpunkter och nu har vi utökat den möjligheten till PowerShell-cmdletar.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdlets för rapportering](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filter kontroller för gransknings-och inloggnings loggar i Azure AD

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi har uppdaterat rapporterna för gransknings-och inloggnings loggar så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner i rapport skärmarna. Dessutom kan du bestämma hur många filter som ska visas på skärmen. De här uppdateringarna fungerar tillsammans för att göra dina rapporter lättare att läsa och mer begränsade till dina behov.

Mer information om de här uppdateringarna finns i [filtrera gransknings loggar](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) och [filtrera inloggnings aktiviteter](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>New riskDetections-API för Microsoft Graph (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Vi är glada över att kunna presentera den nya riskDetections-API: n för Microsoft Graph nu i offentlig för hands version. Du kan använda den här nya API: n för att visa en lista över organisationens identitet skydd – relaterad användare och inloggnings risk identifieringar. Du kan också använda det här API: et för att effektivt fråga dina risk identifieringar, inklusive information om identifierings typ, status, nivå och mycket annat.

Mer information finns i [referens dokumentationen för riskhanterings-API](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I juni 2019 har vi lagt till dessa 22 nya appar med stöd för federation i app-galleriet:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN-klient](https://portal.azure.com/), [utgifteri](../saas-apps/expensein-tutorial.md), [Hjälp](../saas-apps/helper-helper-tutorial.md)verktyg för hjälp, [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager för Oracle återförsäljarversion, Oracle Access Manager för Oracle E-Business Suite, Oracle IDCS för E-Business Suite, Oracle IDCS för IDCS, Oracle JD för Edwards

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: övervakning av & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa real tids förloppet för Azure AD Provisioning-tjänsten

**Typ:** Ändrad funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** hantering av identitets livs cykel

Vi har uppdaterat Azure ADs etablerings miljö för att inkludera en ny förlopps indikator som visar hur långt du befinner dig i användar etablerings processen. Den här uppdaterade upplevelsen ger också information om antalet användare som etablerats under den aktuella cykeln, samt hur många användare som har etablerats till dagens datum.

Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företags anpassning visas nu på skärmen Logga ut och fel

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Vi har uppdaterat Azure AD så att ditt företags varumärke nu visas på skärmen Logga ut och fel och på inloggnings sidan. Du behöver inte göra något för att aktivera den här funktionen, Azure AD använder bara de till gångar som du redan har konfigurerat i **företags anpassnings** delen av Azure Portal.

Mer information om hur du konfigurerar din företags anpassning finns i [lägga till anpassning till din organisations Azure Active Directory sidor](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-servern (MFA) är inte längre tillgänglig för nya distributioner

**Typ:** Föråldrad **tjänst kategori:** MFA **Product Capability:** Identity Security & Protection

Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication i organisationen måste nu använda molnbaserad Azure AD-Multi-Factor Authentication. Kunder som aktiverade MFA Server tidigare än 1 juli ser ingen ändring. Du kommer fortfarande att kunna ladda ned den senaste versionen, Hämta framtida uppdateringar och generera autentiseringsuppgifter för aktivering.

Mer information finns i [komma igång med Azure Multi-Factor Authentication-Server](../authentication/howto-mfaserver-deploy.md). Mer information om molnbaserad Azure AD-Multi-Factor Authentication finns i [Planera en molnbaserad Azure ad Multi-Factor Authentication-distribution](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänst förändring: framtida stöd för endast TLS 1,2-protokoll i Application Proxy-tjänsten

**Typ:** Plan för ändrings **tjänst kategori:** App proxy **produkt kapacitet:** Access Control

För att tillhandahålla bästa möjliga kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1,2-protokoll på Application Proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan använder TLS 1,2-protokoll, så du bör inte se några ändringar.

Utfasningen av TLS 1,0 och TLS 1,1 sker den 31 augusti 2019, men vi ger ytterligare Avancerat meddelande så att du har tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen kontrollerar du att kombinationen av klient-och webb läsar Server, inklusive alla klienter som användarna använder för att komma åt appar som publicerats via programproxy, har uppdaterats för att använda TLS 1,2-protokollet för att upprätthålla anslutningen till Application Proxy-tjänsten. Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd rapporten användning och insikter för att visa dina app-relaterade inloggnings data

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: övervakning av & rapportering

Nu kan du använda rapporten användning och insikter som finns i avsnittet **företags program** i Azure Portal för att få en programinriktad vy över dina inloggnings data, inklusive information om:

- Mest använda appar för din organisation

- Appar med de mest misslyckade inloggnings programmen

- Vanligaste inloggnings fel för varje app

Mer information om den här funktionen finns i [användnings-och insikts rapport i Azure Active Directory Portal](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användar etablering för molnappar med Azure AD

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: övervakning av & rapportering

Följ de här nya självstudierna för att använda Azure AD Provisioning-tjänsten för att automatisera skapande, borttagning och uppdatering av användar konton för följande molnbaserade appar:

- [Samstämm](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Du kan också följa den här nya [guiden Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), som innehåller information om hur du etablerar grupp objekt.

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitets säkra poäng är nu tillgängligt i Azure AD (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** N/A **produkt kapacitet:** identitets säkerhet & skydd

Nu kan du övervaka och förbättra din position för identiteter genom att använda funktionen identitet säkra poäng i Azure AD. I funktionen för identitetens säkra poäng används en enda instrument panel för att hjälpa dig:

- Objektivt mäta din position för identiteter baserat på en poäng på mellan 1 och 223.

- Planera för förbättringar av din identitets säkerhet

- Granska lyckade säkerhets förbättringar

Mer information om funktionen för identitets säkerhets Poäng finns [i vad är identitetens säkra poäng i Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Ny Appregistreringar upplevelse är nu tillgänglig (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** utvecklings miljö

Den nya [Appregistreringar](https://aka.ms/appregistrations) upplevelsen är nu allmänt tillgänglig. Den här nya upplevelsen innehåller alla viktiga funktioner som du är van vid från Azure Portal och program registrerings portalen och som ökar genom att:

- **Bättre hantering av appar.** I stället för att se dina appar över olika portaler kan du nu se alla dina appar på en plats.

- **Förenklad registrering av appar.** Från den förbättrade navigerings upplevelsen av förbättringar-behörigheten är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabb starts guider med mera.

Mer information finns i [Microsoft Identity Platform](../develop/index.yml) och [Appregistreringar Experience är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogg meddelande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API för riskfyllda användare för identitets skydd

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Vi är glada över att kunna meddela att du nu kan använda API: erna för riskfyllda användare för att hämta användares risk historik, ignorera riskfyllda användare och bekräfta användare som komprometterade. Den här ändringen hjälper dig att effektivt uppdatera risk statusen för dina användare och förstå deras risk historik.

Mer information finns i [referens dokumentationen för riskfyllda användare](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I maj 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [riktiga länkar](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [enkel inloggning](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [displayer](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales-engagerande](https://toutapp.com/login), [ACLP](../saas-apps/aclp-tutorial.md),- [system](../saas-apps/outsystems-tutorial.md), [META4 global timme](../saas-apps/meta4-global-hr-tutorial.md), [Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md), [kobolt](../saas-apps/cobalt-tutorial.md), [webMethods API-moln](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [kontroll](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [näring](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade grupper för att skapa och hantera funktioner i Azure AD-portalen

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Vi har gjort förbättringar i de grupprelaterade upplevelserna i Azure AD-portalen. Dessa förbättringar gör det möjligt för administratörer att hantera grupper listor, medlems listor och ge ytterligare skapande alternativ.

Här är några av förbättringarna:

- Grundläggande filtrering efter medlemskaps typ och grupptyp.

- Tillägg av nya kolumner, t. ex. källa och e-postadress.

- Möjlighet att välja flera grupper, medlemmar och ägar listor för enkel borttagning.

- Möjlighet att välja en e-postadress och lägga till ägare när gruppen skapas.

Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (allmän tillgänglighet)

**Typ:** Ändra funktions **tjänst kategori:** grupp hantering **produkt kapacitet:** samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitets loggar (allmän tillgänglighet)

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi är glada över att kunna meddela allmän tillgänglighet för stöd för Microsoft Graph API-slutpunkter för Azure AD-aktivitets loggar. I den här versionen kan du nu använda version 1,0 av både gransknings loggarna i Azure AD och inloggnings loggarna.

Mer information finns i [Översikt över Azure AD audit log API](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registrerings processen (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registrerings sidan. Detta omfattar att tillämpa principer för att tillåta registrering om:

- Användare finns i ett betrott nätverk.

- Användare är en låg inloggnings risk.

- Användare finns på en hanterad enhet.

- Användarna godkänner organisationens användnings villkor (TOU).

Om du vill ha mer information om villkorlig åtkomst och återställning av lösen ord kan du se [blogg inlägget för den villkorliga åtkomsten i blogg inlägget för registrering i Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Mer information om principer för villkorlig åtkomst för den kombinerade registrerings processen finns i [principer för villkorlig åtkomst för kombinerad registrering](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Mer information om funktionen användnings villkor för Azure AD finns i Azure Active Directory användnings [villkor](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Ny identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av Azure AD Identity Protection

**Typ:** Ny funktions **tjänst kategori:** Azure AD Identity Protection **produkt kapacitet:** identitets säkerhet & skydd

Identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av den uppdaterade Azure AD Identity Protections funktionen. Med den här nya funktionen kan du ange ovanliga användar aktiviteter för en viss användare eller aktivitet som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i de [fyra viktiga Azure AD Identity Protection förbättringarna finns nu i den offentliga för hands](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) versionen av bloggen och [vad som är Azure Active Directory Identity Protection (uppdaterat)?](../identity-protection/overview-identity-protection.md) . Mer information om identifiering av Azure AD Threat Intelligence finns i artikeln [Azure Active Directory Identity Protection risk identifieringar](../identity-protection/concept-identity-protection-risks.md) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Hantering av Azure AD-berättigande är nu tillgängligt (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** identitets styrning **produkt kapacitet:** identitets styrning

Hantering av Azure AD-hantering, nu i offentlig för hands version, hjälper kunderna att delegera hantering av åtkomst paket, som definierar hur anställda och affärs partner kan begära åtkomst, vem som måste godkänna och hur länge de har åtkomst. Åtkomst paket kan hantera medlemskap i Azure AD-och Office 365-grupper, roll tilldelningar i företags program och roll tilldelningar för SharePoint Online-webbplatser. Läs mer om hantering av rättigheter i [Översikt över hantering av Azure AD-rättigheter](../governance/entitlement-management-overview.md). Om du vill veta mer om bredden på Azure AD Identity Governance funktioner, inklusive Privileged Identity Management, åtkomst granskningar och användnings villkor, se [Vad är Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitets loggar är nu tillgängliga i Azure Monitor (allmän tillgänglighet)

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi presenterar en ny insikts funktion i Log Analytics för att hjälpa dig att åtgärda dina synpunkter på visualiseringar med aktivitets loggarna i Azure AD. Den här funktionen hjälper dig att få insikter om dina Azure AD-resurser med hjälp av våra interaktiva mallar, som kallas arbets böcker. Dessa färdiga arbets böcker kan ge information om appar eller användare och inkludera:

- **Inloggningar.** Innehåller information för appar och användare, inklusive inloggnings plats, klient och version för operativ systemet eller webbläsare och antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och villkorlig åtkomst.** Innehåller information för appar och användare som använder äldre autentisering, inklusive Multi-Factor Authentication användning som utlösts av principer för villkorlig åtkomst, appar som använder principer för villkorlig åtkomst och så vidare.

- **Analys av inloggnings problem.** Hjälper dig att avgöra om inloggnings felen inträffar på grund av en användar åtgärd, princip problem eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbets böcker som hjälper dig att anpassa insikter-funktionen för din organisation.

Mer information finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – april 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I april 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks enkel inloggning](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [shibumi](../saas-apps/shibumi-tutorial.md), [Bänkning](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), PageDNA, [EduBrite](../saas-apps/pagedna-tutorial.md) [LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [spetsig Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (rollbaserad SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent egendoms hantering](../saas-apps/certent-equity-management-tutorial.md), [Sectigo certifikat hanterare](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [Monday.com](../saas-apps/mondaycom-tutorial.md), SurveyMonkey [Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md) [, INDIGGO](https://indiggolead.com/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nya frekvens alternativ för åtkomst granskningar och flera roll val

**Typ:** Ny funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** identitets styrning

Nya uppdateringar i åtkomst granskningar i Azure AD gör att du kan:

- Ändra frekvensen för dina åtkomst granskningar till **halv varje år**, förutom de tidigare befintliga alternativen varje vecka, varje månad, kvartals vis och varje år.

- Välj flera Azure AD-och Azure-resurs roller när du skapar en enda åtkomst granskning. I den här situationen konfigureras alla roller med samma inställningar och alla granskare meddelas på samma tid.

Mer information om hur du skapar en åtkomst granskning finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-postaviserings system överförs, skickar ny e-postavsändar information för vissa kunder

**Typ:** Ändrad funktions **tjänst kategori:** AD Sync **produkt kapacitet:** plattform

Azure AD Connect håller på att överföra våra e-postvarnings system, vilket potentiellt visar vissa kunder en ny e-postsändare. För att lösa detta måste du lägga till `azure-noreply@microsoft.com` i din organisations lista över tillåtna eller så kan du inte fortsätta att ta emot viktiga aviseringar från Office 365, Azure eller dina Sync-tjänster.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Ändringar av UPN-suffix har nu slutförts mellan federerade domäner i Azure AD Connect

**Typ:** Fast **tjänste kategori:** AD Sync **produkt kapacitet:** plattform

Du kan nu ändra en användares UPN-suffix från en federerad domän till en annan federerad domän i Azure AD Connect. Den här korrigeringen innebär att du inte längre bör uppleva FederatedDomainChangeError-felmeddelande under synkroniseringsprocessen eller få ett meddelande om att det inte går att uppdatera det här objektet i Azure Active Directory eftersom attributet [FederatedUser. UserPrincipalName] inte är giltigt. Uppdatera värdet i dina lokala katalog tjänster.

Mer information finns i [fel sökning av fel under synkronisering](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av app Protection-baserad princip för villkorlig åtkomst i Azure AD (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

App Protection-baserad villkorlig åtkomst är nu tillgänglig med hjälp av principen **Kräv app-skydd** . Den här nya principen hjälper till att öka din organisations säkerhet genom att bidra till att förhindra:

- Användare får åtkomst till appar utan en Microsoft Intune-licens.

- Användare kan inte hämta en Microsoft Intune skydds princip för appar.

- Användare får åtkomst till appar utan en konfigurerad Microsoft Intune App Protection-princip.

Mer information finns i [så här kräver du app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD i Microsoft Edge (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

Vi har förbättrat Azure AD-supporten för Microsoft Edge, inklusive att tillhandahålla nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD. Om du tidigare har använt Microsoft Intune Managed Browser kan du nu använda Microsoft Edge i stället.

Mer information om hur du konfigurerar och hanterar enheter och appar med hjälp av villkorlig åtkomst finns i [Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](../conditional-access/require-managed-devices.md) och [Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst](../conditional-access/app-based-conditional-access.md). Mer information om hur du hanterar åtkomst med hjälp av Microsoft Edge med Microsoft Intune-principer finns i [Hantera Internet åtkomst med en Microsoft Intune-skyddad webbläsare](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identitets upplevelse ramverk och anpassad princip support i Azure Active Directory B2C är nu tillgänglig (GA)

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Nu kan du skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i stor skala och under vårt Azure SLA:

- Skapa och ladda upp användar transporter för anpassad autentisering med hjälp av anpassade principer.

- Beskriv användar resan steg för steg som utbyten mellan anspråks leverantörer.

- Definiera villkorlig grenning i användar resor.

- Transformera och mappa anspråk för användning i real tids beslut och kommunikation.

- Använd REST API-aktiverade tjänster i dina anpassade användar resor för autentisering. Till exempel med e-postleverantörer, CRMs och tillverkarspecifika auktoriserings system.

- Federera med identitets leverantörer som är kompatibla med OpenIDConnect-protokollet. Till exempel med flera klient organisationer för Azure AD, sociala konto leverantörer eller två-Factor Verification-leverantörer.

För ytterligare information om hur du skapar anpassade principer, se [Developer-anteckningar för anpassade principer i Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) och Läs [Alex-Simons blogg inlägg, inklusive fallstudier](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – mars 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I mars 2019 har vi lagt till dessa 14 nya appar med stöd för federation i app-galleriet:

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [förklarings-baserade gransknings system](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool prestanda](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), [Iris](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw, Confirmit](../saas-apps/smartdraw-tutorial.md)- [horisonter](../saas-apps/confirmit-horizons-tutorial.md), [aktivitet](../saas-apps/tas-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler-och Atlassian-etablerings anslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** integration från tredje part

Automatisera att skapa, uppdatera och ta bort användar konton för följande appar:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler två](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler tre](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera dina borttagna Office 365-grupper i Azure AD-portalen

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Nu kan du Visa och hantera dina borttagna Office 365-grupper från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för återställning, tillsammans med att låta dig ta bort alla grupper som inte behövs i din organisation Permanent.

Mer information finns i [återställa inaktuella eller borttagna grupper](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgängligt för Azure AD SAML-skyddade lokala appar via programproxy (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för App-proxy: Access Control

Nu kan du tillhandahålla enkel inloggning (SSO) för lokala, SAML-autentiserade appar, tillsammans med fjärråtkomst till dessa appar via programproxyn. Mer information om hur du konfigurerar SAML SSO med dina lokala appar finns i [SAML enkel inloggning för lokala program med Application Proxy (för hands version)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klient program i begär ande slingor avbryts för att förbättra tillförlitligheten och användar upplevelsen

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Klient program kan felaktigt skicka hundratals av samma inloggnings begär Anden under en kort tids period. Dessa förfrågningar, oavsett om de är lyckade eller inte, bidrar till en dåligt användar upplevelse och förhöjda arbets belastningar för IDP, ökande svars tid för alla användare och för att minska tillgängligheten för IDP.

Den här uppdateringen skickar ett `invalid_grant` fel: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` till klient program som utfärdar dubbla förfrågningar flera gånger under en kort tids period, utöver normal drifts omfattning. Klient program som stöter på det här problemet bör visa en interaktiv prompt som kräver att användaren loggar in igen. Mer information om den här ändringen och hur du åtgärdar din app om det här felet påträffas finns i [Vad är nytt för autentisering?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nya gransknings loggar användar upplevelsen är nu tillgänglig

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi har skapat en ny sida med **gransknings loggar** för Azure AD som hjälper till att förbättra både läsbarhet och hur du söker efter din information. Om du vill se sidan nya **gransknings loggar** väljer du **gransknings loggar** i avsnittet **aktivitet** i Azure AD.

![Sidan nya gransknings loggar med exempel information](media/whats-new/audit-logs-page.png)

Mer information om sidan nya **gransknings loggar** finns [i gransknings aktivitets rapporter i Azure Active Directory-portalen](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och rikt linjer som hjälper dig att förhindra oavsiktlig administratörs utelåsning från felkonfigurerade principer för villkorlig åtkomst

**Typ:** Ändrad funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

För att förhindra att administratörer oavsiktligt låser sig själva ut från sina egna klienter via felkonfigurerade principer för villkorlig åtkomst har vi skapat nya varningar och uppdaterad vägledning i Azure Portal. Mer information om den nya vägledningen finns i [Vad är tjänst beroenden i Azure Active Directory villkorlig åtkomst](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrade användnings villkor för slutanvändare på mobila enheter

**Typ:** Ändrad funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Vi har uppdaterat våra befintliga användnings villkor för att hjälpa till att förbättra hur du granskar och godkänner användnings villkoren på en mobil enhet. Nu kan du zooma in och ut, gå tillbaka, ladda ned informationen och välja hyperlänkar. Mer information om de uppdaterade användnings villkoren finns i Azure Active Directory användnings [villkor](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nya hämtnings möjligheter för Azure AD-aktivitets loggar är tillgängliga

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Nu kan du hämta stora mängder aktivitets loggar direkt från Azure Portal. Med den här uppdateringen kan du:

- Hämta upp till 250 000 rader.

- Få ett meddelande när nedladdningen är klar.

- Anpassa ditt fil namn.

- Ta reda på utdataformatet, antingen JSON eller CSV.

Mer information om den här funktionen finns i [snabb start: Hämta en gransknings rapport med hjälp av Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Brytar förändring: uppdateringar av villkors utvärderingen av Exchange ActiveSync (EAS)

**Typ:** Plan för ändrings **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: Access Control

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användar plats, baserat på land, region eller IP-adress

- Inloggningsrisk

- Enhetsplattform

Om du tidigare har använt dessa villkor i dina principer för villkorlig åtkomst bör du tänka på att villkors beteendet kan ändras. Om du till exempel tidigare använde villkoret användar plats i en princip kan du se till att principen nu hoppas över baserat på användarens plats.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML token Encryption (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Nu kan du konfigurera en SAML-app som stöds för att ta emot krypterade SAML-token. När Azure AD är konfigurerat och används med en app, krypterar Azure AD de utgivna SAML-beställarna med en offentlig nyckel som hämtats från ett certifikat som lagras i Azure AD.

Mer information om hur du konfigurerar din kryptering för SAML-token finns i [Konfigurera Azure AD SAML token Encryption](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomst granskning för grupper eller appar med hjälp av åtkomst granskningar för Azure AD

**Typ:** Ny funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** styrning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD Access-granskning för grupp medlemskap eller program tilldelning. Åtkomst granskningar med flera grupper eller appar konfigureras med samma inställningar och alla granskare som ingår meddelas på samma gång.

Mer information om hur du skapar en åtkomst granskning med hjälp av åtkomst granskningar i Azure AD finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – februari 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I februari 2019 har vi lagt till dessa 27 nya appar med stöd för federation i app-galleriet:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MINDTICKLE](../saas-apps/mindtickle-tutorial.md), [fett finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [netstack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [iDrive](../saas-apps/idrive-tutorial.md), [skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), permission klickning, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), [Pexip](../saas-apps/pexip-tutorial.md), [Stormboard](../saas-apps/stormboard-tutorial.md), [seismisk](../saas-apps/seismic-tutorial.md), [dela en dröm](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [webMethods integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [kunskap överallt](../saas-apps/knowledge-anywhere-lms-tutorial.md), [](../saas-apps/ou-campus-tutorial.md) [](../saas-apps/periscope-data-tutorial.md) [](../saas-apps/netop-portal-tutorial.md) [](../saas-apps/smartvid.io-tutorial.md) [](../saas-apps/purecloud-by-genesys-tutorial.md) [](../saas-apps/clickup-productivity-platform-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombination av MFA-/SSPR-registrering

**Typ:** Ändring av funktions **tjänst kategori:** självbetjäning för återställning av lösen ord för **produkter:** användarautentisering

Som svar på kundfeedback har vi utökat den kombinerade för hands versionen av MFA/SSPR-registrering, vilket hjälper användarna att snabbt registrera sina säkerhets uppgifter för både MFA och SSPR.

**Följ dessa steg om du vill aktivera den förbättrade upplevelsen för dina användare ' idag:**

1. Som global administratör eller användar administratör loggar du in på Azure Portal och går till **Azure Active Directory > användar inställningar > hantera inställningar för åtkomst panelens för hands versions funktioner**.

2. I de **användare som kan använda för hands versions funktioner för att registrera och hantera säkerhets information – uppdaterings** alternativ väljer du att aktivera funktionerna för en **vald grupp av användare** eller för **alla användare**.

Under de kommande veckorna kommer vi att ta bort möjligheten att aktivera den tidigare kombinerade för hands versionen av MFA-/SSPR-registrering för klienter som inte redan har den aktiverat.

**Följ dessa steg om du vill se om kontrollen kommer att tas bort för din klient:**

1. Som global administratör eller användar administratör loggar du in på Azure Portal och går till **Azure Active Directory > användar inställningar > hantera inställningar för åtkomst panelens för hands versions funktioner**.

2. Om de **användare som kan använda alternativet för för hands versions funktioner för att registrera och hantera säkerhets information** har angetts till **ingen**, tas alternativet bort från din klient.

Oavsett om du tidigare har aktiverat den tidigare samlade för hands versionen av MFA/SSPR-registrering för användare eller inte, kommer den gamla upplevelsen att inaktive ras vid ett framtida datum. Därför föreslår vi starkt att du flyttar till den nya, förbättrade upplevelsen så snart som möjligt.

För ytterligare information om den förbättrade registrerings upplevelsen, se de [häftiga förbättringarna i registrerings upplevelsen av Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterad princip hanterings upplevelse för användar flöden

**Typ:** Ändrad funktions **tjänst kategori:** B2C-Consumer Identity Management **produkt kapacitet:** B2B/B2C

Vi har uppdaterat processen för att skapa och hantera principer för användar flöden (tidigare kallade inbyggda principer) enklare. Den nya upplevelsen är nu standard för alla dina Azure AD-klienter.

Du kan ge ytterligare feedback och förslag genom att använda ikonerna leende eller bister min i avsnittet **Skicka feedback för oss** överst på Portal skärmen.

Mer information om den nya princip hanterings upplevelsen finns i [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj vissa sid element versioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Nu kan du välja en speciell version av sid elementen som tillhandahålls av Azure AD B2C. Genom att välja en viss version kan du testa dina uppdateringar innan de visas på en sida och du kan få förutsägbara beteenden. Dessutom kan du välja att använda vissa sid versioner för att tillåta anpassade JavaScript-anpassningar. Om du vill aktivera den här funktionen går du till sidan **Egenskaper** i dina användar flöden.

Mer information om hur du väljer vissa versioner av sid element finns i avsnittet [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösen ords krav för B2C (GA)

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Nu kan du konfigurera din organisations lösen ords komplexitet för dina slutanvändare, i stället för att behöva använda din interna lösen ords princip för Azure AD. Från **egenskaps** bladet för dina användar flöden (kallades tidigare dina inbyggda principer) kan du välja ett lösen ords komplexitet som är **enkelt** eller **starkt**, eller så kan du skapa en **anpassad** uppsättning krav.

Mer information om konfiguration av lösen ords komplexitets krav finns i [Konfigurera komplexitets krav för lösen ord i Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade funktioner för anpassad autentisering

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Du kan använda våra nya standardmallar som finns på **bladet sidlayouter i** dina användar flöden (tidigare kallade inbyggda principer) för att skapa en anpassad anpassad autentisering för dina användare.

Mer information om hur du använder mallarna finns i [Azure AD B2C nu har Java Script-anpassning och många nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete med autentisering med eng ång slö sen ord (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C

Vi har infört autentisering med eng ång slö sen ord för B2B-gäst användare som inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. Den här nya autentiseringsmetoden innebär att gäst användare inte behöver skapa nya Microsoft-konto. I stället kan en gäst användare begära att en tillfällig kod skickas till en e-postadress, samtidigt som du löser in en inbjudan eller använder en delad resurs. Med den här tillfälliga koden kan gäst användaren fortsätta att logga in.

Mer information finns i [e-postautentisering med eng ång slö sen ord (för hands version)](../external-identities/one-time-passcode.md) och bloggen. [Azure AD gör delning och samarbete sömlöst för alla användare med ett konto](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya inställningar för Azure AD-programproxy cookies

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för App-proxy: Access Control

Vi har introducerat tre nya cookie-inställningar som är tillgängliga för dina appar som publiceras via programproxy:

- **Använd HTTP-Only cookie.** Ställer in flaggan **HTTPOnly** på din Application Proxy-åtkomst och sessionscookies. Att aktivera den här inställningen ger ytterligare säkerhets fördelar, till exempel att förhindra att cookies kopieras eller ändras via skript på klient sidan. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för de extra fördelarna.

- **Använd säker cookie.** Anger **säker** flagga för programproxyns åtkomst och sessionscookies. Att aktivera den här inställningen ger ytterligare säkerhets förmåner genom att se till att cookies bara överförs via säkra TLS-kanaler, t. ex. HTTPS. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för de extra fördelarna.

- **Använd beständig cookie.** Förhindrar att åtkomst till cookies upphör att gälla när webbläsaren stängs. Dessa cookies sist under åtkomsttoken. Men cookies återställs om förfallo tiden nås eller om användaren manuellt tar bort cookien. Vi rekommenderar att du behåller standardinställningen **Nej** och bara aktiverar inställningen för äldre appar som inte delar cookies mellan processer.

Mer information om de nya cookies finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – januari 2019

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I januari 2019 har vi lagt till dessa 35 nya appar med stöd för federation i app-galleriet:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [personal-palett](../saas-apps/talent-palette-tutorial.md), [infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco paraply](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access-administratör](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [förfallo påminnelse](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md), [CORPTAX](../saas-apps/corptax-tutorial.md), [verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md) [, iProva,](../saas-apps/iprova-tutorial.md) [fungerande](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO system](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ar för företag](../saas-apps/ares-for-enterprise-tutorial.md), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [på](../saas-apps/visitly-tutorial.md)plats, [korn Alp](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), Adoddle [cSaas-plattform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya Azure AD Identity Protection förbättringar (offentlig för hands version)

**Typ:** Ändring av funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

Vi är glada över att kunna meddela att vi har lagt till följande förbättringar i Azure AD Identity Protection Public Preview-erbjudandet, inklusive:

- Ett uppdaterat och mer integrerat användar gränssnitt

- Ytterligare API: er

- Förbättrad risk bedömning via Machine Learning

- Anpassning av hela produkten över riskfyllda användare och riskfyllda inloggningar

För ytterligare information om förbättringarna, se [Vad är Azure Active Directory Identity Protection (uppdaterat)?](../identity-protection/overview-identity-protection.md) för att lära dig mer och dela med dig av dina tankar genom produkt frågorna.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny app lock-funktion för Microsoft Authenticator-appen på iOS-och Android-enheter

**Typ:** Ny funktions **tjänst kategori:** Microsoft Authenticator **produkt kapacitet** för appar: identitets säkerhet & skydd

Om du vill behålla dina lösen ord, appdata och appinställningar säkrare, kan du aktivera appens lås funktion i Microsoft Authenticator-appen. Genom att aktivera app-lås kan du bli ombedd att autentisera med hjälp av din PIN-kod eller bio metrisk varje gång du öppnar Microsoft Authenticator-appen.

Mer information finns i [vanliga frågor och svar om Microsoft Authenticator app](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrade export funktioner för Azure AD Privileged Identity Management (PIM)

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Privileged Identity Management (PIM)-administratörer kan nu exportera alla aktiva och berättigade roll tilldelningar för en speciell resurs, som innehåller roll tilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över roll tilldelningar för en prenumeration och de behövde exportera roll tilldelningar för varje enskild resurs.

Mer information finns i [Visa aktivitet och gransknings historik för Azure Resource roles i PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som tas bort från omfånget växlar inte längre till enbart moln konton

**Typ:** Fast **tjänste kategori:** användar hantering **produkt kapacitet:** katalog

>[!Important]
>Vi har hört och förstår din hjälp på grund av den här korrigeringen. Vi har därför återställt den här ändringen tills vi kan göra det lättare för dig att implementera i din organisation.

Vi har åtgärdat ett fel där en användares DirSyncEnabled flagga skulle felaktigt växlas till **false** när Active Directory Domain Services (AD DS)-objektet uteslöts från omfånget och sedan flyttades till pappers korgen i Azure AD på följande synkroniseringstid. Till följd av den här korrigeringen, om användaren undantas från Sync-omfånget och sedan återställs från Azure AD-pappers korgen, är användar kontot kvar som synkroniserat från den lokala AD-platsen som förväntat och kan inte hanteras i molnet eftersom dess auktoritets källa (SoA) fortfarande är lokal AD.

Före den här korrigeringen uppstod ett problem när flaggan DirSyncEnabled växlades till false. Det gav fel intryck att dessa konton konverterades till molnbaserade objekt och att kontona kan hanteras i molnet. Men kontona har kvar sin SoA som lokalt och alla synkroniserade egenskaper (Shadow-attribut) som kommer från den lokala AD-platsen. Det här tillståndet orsakade flera problem i Azure AD och andra moln arbets belastningar (t. ex. Exchange Online) som förväntar sig att hantera dessa konton som synkroniserade från AD, men som nu fungerar som molnbaserade konton.

För närvarande är det enda sättet att verkligen konvertera ett synkroniserat-från-AD-konto till endast ett moln konto genom att inaktivera DirSync på klient nivån, vilket utlöser en server dels åtgärd för att överföra SoA. Den här typen av SoA-ändring kräver (men är inte begränsad till) rengöring av alla lokala relaterade attribut (till exempel LastDirSyncTime och skugg egenskaper) och sändning av en signal till andra moln arbets belastningar så att respektive objekt konverteras till ett moln konto.

Den här korrigeringen förhindrar därför direkta uppdateringar av ImmutableID-attributet för en användare som synkroniseras från AD, som i vissa scenarier tidigare var obligatoriska. Enligt design är ImmutableID för ett objekt i Azure AD, som namnet antyder, avsett att vara oföränderligt. Nya funktioner som implementeras i Azure AD Connect Health-och Azure AD Connect-synkroniseringstjänsten är tillgängliga för att lösa sådana scenarier:

- **Storskalig ImmutableID-uppdatering för många användare i en stegvis metod**

  Du måste t. ex. göra en lång AD DS-migrering mellan skogar. Lösning: Använd Azure AD Connect om du vill **Konfigurera käll ankare** och, när användaren migreras, kopiera de befintliga ImmutableID-värdena från Azure AD till den lokala AD DS-användarens ms-DS-attribut-GUID-attribut i den nya skogen. Mer information finns i [using ms-DS-ConsistencyGuid som sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Storskaliga ImmutableID-uppdateringar för många användare i en bild**

  Exempel: när du implementerar Azure AD Connect du göra fel och nu måste du ändra attributet SourceAnchor. Lösning: inaktivera DirSync på klient nivån och rensa alla ogiltiga ImmutableID-värden. Mer information finns i [Inaktivera katalog-synkronisering för Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Matcha lokal användare med en befintlig användare i Azure AD** En användare som har återskapats i AD DS genererar till exempel en dubblett i Azure AD-konto i stället för att matcha det med ett befintligt Azure AD-konto (överblivna objekt). Lösning: Använd Azure AD Connect Health i Azure Portal för att mappa om käll fäst punkten/ImmutableID. Mer information finns i [scenario med överblivna objekt](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Bryta ändring: uppdateringar av schemat för gransknings-och inloggnings loggar via Azure Monitor

**Typ:** Ändrad funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi publicerar för närvarande både gransknings-och inloggnings logg strömmar via Azure Monitor, så att du sömlöst kan integrera loggfilerna med dina SIEM-verktyg eller med Log Analytics. Utifrån din feedback och för den här funktionens allmänna tillgänglighets meddelande gör vi följande ändringar i vårt schema. De här schema ändringarna och de relaterade dokumentations uppdateringarna sker den första veckan i januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nya fält i gransknings schema
Vi lägger till ett nytt **Åtgärds typ** fält för att ange vilken typ av åtgärd som utförs på resursen. Du kan till exempel **lägga till**, **Uppdatera** eller **ta bort**.

#### <a name="changed-fields-in-the-audit-schema"></a>Ändrade fält i gransknings schema
Följande fält ändras i gransknings schemat:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|Kategori|Detta är **tjänstens namn** fält. Nu är fältet **gransknings kategorier** . **Tjänst namnet** har bytt namn till fältet **loggedByService** .|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Lösen ords återställning via självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Hantering av appar</li></ul>|
|targetResources|Innehåller **TargetResourceType** på den översta nivån.|&nbsp;|<ul><li>Policy</li><li>App</li><li>User</li><li>Group</li></ul>|
|loggedByService|Innehåller namnet på tjänsten som skapade gransknings loggen.|Null|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord för självbetjäning</li></ul>|
|Resultat|Visar resultatet av gransknings loggarna. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Klart</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggnings schema
Följande fält ändras i inloggnings schema:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Detta var fältet **conditionalaccessPolicies** . Nu är fältet **appliedConditionalAccessPolicies** .|Ingen ändring|Ingen ändring|
|conditionalAccessStatus|Visar resultatet av den villkorliga åtkomst princip statusen vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Används inte</li><li>Inaktiverad</li></ul>|
|appliedConditionalAccessPolicies: resultat|Visar resultatet av en enskild princip status för villkorlig åtkomst vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Används inte</li><li>Inaktiverad</li></ul>|

Mer information om schemat finns i [tolka schemat för gransknings loggar i Azure AD i Azure Monitor (för hands version)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Förbättringar av identitets skydd i den övervakade Machine Learning-modellen och risk Poäng motorn

**Typ:** Ändrad funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** risk Poäng

Förbättringar av motorn för identitets skydd – relaterad användare och inloggnings riskbedömning kan hjälpa till att förbättra precisionen och täckningen av användarnas risker. Administratörer kan se att användar risk nivån inte längre är direkt länkad till risk nivån för vissa identifieringar, och att det finns en ökning av antalet och graden av riskfyllda inloggnings händelser.

Risk identifieringar utvärderas nu av den övervakade Machine Learning-modellen, som beräknar användar risken genom att använda ytterligare funktioner i användarens inloggnings program och ett mönster för identifieringar. Utifrån den här modellen kan administratören hitta användare med hög risk poäng, även om identifieringar som är associerade med den användaren är av låg eller medelhög risk.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sina egna lösen ord med hjälp av Microsoft Authenticator-appen (offentlig för hands version)

**Typ:** Ändring av funktions **tjänst kategori:** självbetjäning för återställning av lösen ord för **produkter:** användarautentisering

Azure AD-administratörer kan nu återställa sina egna lösen ord med hjälp av Microsoft Authenticator app-meddelanden eller en kod från valfri mobilapp eller maskinvarubaserad token. Administratörer kan nu använda två av följande metoder för att återställa sina egna lösen ord:

- Microsoft Authenticator app-avisering

- Annan mobil autentiserare app/maskinvaru-token kod

- E-post

- Telefonsamtal

- Textmeddelande

Mer information om hur du använder Microsoft Authenticator-appen för att återställa lösen ord finns i [Azure AD Self-Service Password rereset-Mobile app and SSPR (för hands version)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny administratörs roll för moln enheten i Azure AD (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** enhets registrering och hantering **produkt kapacitet:** åtkomst kontroll

Administratörer kan tilldela användare till den nya rollen som administratör för moln enhet för att utföra administrativa uppgifter. Användare som tilldelats rollen som moln enhets administratörer kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med att kunna läsa Windows 10 BitLocker-nycklar (om de finns) i Azure Portal.

Mer information om roller och behörigheter finns [i tilldela administratörs roller i Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med den nya aktivitetens tidstämpel i Azure AD (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** enhets registrering och hantering **produkt kapacitet:** hantering av enhetens livs cykel

Vi inser att du med tiden måste uppdatera och dra tillbaka dina organisationers enheter i Azure AD för att undvika att ha inaktuella enheter i din miljö. För att hjälpa dig med den här processen uppdaterar Azure AD dina enheter med en ny aktivitets tids stämpling, vilket hjälper dig att hantera din enhets livs cykel.

Mer information om hur du hämtar och använder den här tidsstämpeln finns i [så här gör du: hantera de inaktuella enheterna i Azure AD](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användarna godkänner användnings villkoren på varje enhet

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Administratörer kan nu aktivera alternativet **Kräv att användare godkänner varje enhet** för att kräva att användarna godkänner dina användnings villkor på varje enhet som de använder på din klient organisation.

Mer information finns i [avsnittet användnings villkor per enhet i den Azure Active Directory användnings villkors funktionen](../conditional-access/terms-of-use.md#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användnings villkor så att de upphör att gälla baserat på ett återkommande schema

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning


Administratörer kan nu aktivera alternativet för att alternativet **upphör** att gälla upphör att gälla för alla dina användare utifrån det angivna återkommande schemat. Schemat kan vara årligen, två per år, varje kvartal eller varje månad. När användnings villkoren upphör att gälla måste användarna godkänna.

Mer information finns i [avsnittet lägga till användnings villkor i funktionen Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use)användnings villkor.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera användnings villkor så att de upphör att gälla baserat på varje användares schema

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Administratörer kan nu ange en varaktighet då användaren måste godkänna användnings villkoren igen. Administratörer kan till exempel ange att användarna måste godkänna ett användnings villkor var 90 dag.

Mer information finns i [avsnittet lägga till användnings villkor i funktionen Azure Active Directory](../conditional-access/terms-of-use.md#add-terms-of-use)användnings villkor.

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nya Azure AD Privileged Identity Management-e-postmeddelanden (PIM) för Azure Active Directory roller

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Kunder som använder Azure AD Privileged Identity Management (PIM) kan nu få ett e-postmeddelande med veckovis sammandrag, inklusive följande information under de senaste sju dagarna:

- Översikt över de mest kvalificerade och permanenta roll tilldelningarna

- Antal användare som aktiverar roller

- Antal användare som tilldelats roller i PIM

- Antal användare som tilldelats roller utanför PIM

- Antal användare som har gjorts permanenta i PIM

Mer information om PIM och tillgängliga e-postaviseringar finns i [e-postaviseringar i PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Group-based Licensing är nu allmänt tillgänglig

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** katalog

Gruppbaserad licensiering är utanför den offentliga för hands versionen och är nu allmänt tillgänglig. Som en del av den här allmänna versionen har vi gjort den här funktionen mer skalbar och har lagt till möjligheten att ombearbeta gruppbaserade licens tilldelningar för en enskild användare och möjligheten att använda gruppbaserad licensiering med Office 365 E3/a3-licenser.

Mer information om gruppbaserad licensiering finns [i vad är gruppbaserad licensiering i Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – november 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I november 2018 har vi lagt till dessa 26 nya appar med stöd för federation i app-galleriet:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [gra-PE](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [oändlig Campus](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HEYBUDDY](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [drift](../saas-apps/drift-tutorial.md), [Zenegy för företag, Central 365](https://accounting.zenegy.com/), [EVERBRIDGE medlems Portal](../saas-apps/everbridge-tutorial.md), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [Plex Apps-klassiskt test](https://test.plexonline.com/signon), [Plex Apps – klassiskt](https://www.plexonline.com/signon), [Plex Apps-UX-test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [hantverk-Childcare poster, närvaro, & Financial tracking system](https://getcrafts.ca/craftsregistration)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-loggar fungerar nu med Azure Log Analytics (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Vi är glada över att kunna meddela att du nu kan vidarebefordra dina Azure AD-loggar till Azure Log Analytics! Den här begärda funktionen ger dig ännu bättre åtkomst till analyser för din verksamhet, verksamhet och säkerhet, samt ett sätt att övervaka infrastrukturen. Mer information finns i [Azure Active Directory aktivitets loggar i Azure Log Analytics nu tillgänglig](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogg.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – oktober 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I oktober 2018 har vi lagt till dessa 14 nya appar med stöd för federation i app-galleriet:

[Mina belönings punkter](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [låna](../saas-apps/borrowbox-tutorial.md), dialpad, ON24 [virtuell miljö](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler tre](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [bedömning](../saas-apps/appraisd-tutorial.md), Workspot- [kontroll](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-postaviseringar

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services **produkt kapacitet:** Azure AD Domain Services

Azure AD Domain Services innehåller varningar på Azure Portal om fel konfiguration eller problem med din hanterade domän. De här aviseringarna innehåller steg-för-steg-guider så att du kan försöka åtgärda problemen utan att behöva kontakta supporten.

Från och med oktober kommer du att kunna anpassa aviserings inställningarna för din hanterade domän, så när nya aviseringar inträffar skickas ett e-postmeddelande till en viss grupp med personer, vilket eliminerar behovet av att kontinuerligt kontrol lera portalen efter uppdateringar.

Mer information finns i [meddelande inställningar i Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD Portal stöder användning av ForceDelete-domän-API: et för att ta bort anpassade domäner

**Typ:** Ändrade funktions **tjänst kategori:** katalog hantering **produkt kapacitet:** katalog

Vi är glada över att kunna meddela att du nu kan använda ForceDelete-domän-API: et för att ta bort dina anpassade domän namn genom att asynkront byta namn på referenser, t. ex. användare, grupper och appar från ditt anpassade domän namn (contoso.com) till det initiala standard domän namnet (contoso.onmicrosoft.com).

Den här ändringen hjälper dig att snabbt ta bort dina anpassade domän namn om organisationen inte längre använder namnet eller om du behöver använda domän namnet med en annan Azure AD.

Mer information finns i [ta bort ett anpassat domän namn](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>September 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade administratörs roll behörigheter för dynamiska grupper

**Typ:** Fast **tjänst kategori:** grupp hantering **produkt kapacitet:** samarbete

Vi har åtgärdat ett problem så att vissa administratörs roller nu kan skapa och uppdatera regler för dynamiskt medlemskap, utan att behöva vara ägare till gruppen.

Rollerna är:

- Global administratör

- Intune-administratör

- Användaradministratör

Mer information finns i [skapa en dynamisk grupp och kontrol lera status](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Konfigurations inställningar för förenklad enkel Sign-On (SSO) för vissa appar från tredje part

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Vi inser att konfigurationen av en enda Sign-On (SSO) för SaaS-appar (program vara som en tjänst) kan vara utmanande på grund av den unika typen av varje apps-konfiguration. Vi har skapat en förenklad konfigurations upplevelse för att automatiskt fylla i SSO-konfigurations inställningarna för följande SaaS-appar från tredje part:

- Zendesk

- ArcGis online

- Jamf Pro

Börja använda den här funktionen genom att gå till   >  **konfigurations** sidan för Azure Portal SSO för appen. Mer information finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – var finns dina data? tvåsidig

**Typ:** Ny funktions **tjänst kategori:** annan **produkt kapacitet:** GoLocal

Välj ditt företags region från **Azure Active Directory – var finns din data** sida för att se vilka Azure-datacenter som rör dina Azure AD-data i vila för alla Azure AD-tjänster. Du kan filtrera informationen efter särskilda Azure AD-tjänster för ditt företags region.

För att få åtkomst till den här funktionen och mer information, se [Azure Active Directory – var finns dina data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Ny distributions plan tillgänglig för åtkomst panelen för Mina appar

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** SSO

Kolla in den nya distributions plan som är tillgänglig för åtkomst panelen för Mina appar ( https://aka.ms/deploymentplans) .
Åtkomst panelen för Mina appar ger användare en enda plats för att hitta och komma åt sina appar. Den här portalen ger också användare självbetjänings möjligheter, till exempel att begära åtkomst till appar och grupper eller att hantera åtkomst till dessa resurser för andras räkning.

Mer information finns i [Vad är min Apps-portalen?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Fliken ny fel sökning och support på sidan inloggnings loggar i Azure Portal

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Fliken ny **fel sökning och support** på sidan **inloggningar** i Azure Portal är avsedd att hjälpa administratörer och support tekniker att felsöka problem som rör Azure AD-inloggningar. Den nya fliken innehåller felkod, fel meddelande och reparations rekommendationer (om sådana finns) för att hjälpa till att lösa problemet. Om du inte kan lösa problemet ger vi också ett nytt sätt att skapa ett support ärende med hjälp av funktionen **Kopiera till Urklipp** , som fyller i fälten för **begäran-ID** och **datum (UTC)** för logg filen i ditt support ärende.

![Inloggnings loggar som visar den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrat stöd för anpassade tilläggs egenskaper som används för att skapa regler för dynamiskt medlemskap

**Typ:** Ändra funktions **tjänst kategori:** grupp hantering **produkt kapacitet:** samarbete

Med den här uppdateringen kan du nu Klicka på länken **Hämta anpassade tilläggs egenskaper** från regel verktyget för dynamisk användar grupp, ange ditt unika app-ID och ta emot den fullständiga listan med anpassade tilläggs egenskaper som ska användas när du skapar en regel för dynamiskt medlemskap för användare. Den här listan kan också uppdateras för att få nya anpassade tilläggs egenskaper för appen.

Mer information om hur du använder anpassade tilläggs egenskaper för dynamiska medlemskaps regler finns i [tilläggs egenskaper och anpassade tilläggs egenskaper](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Plan för ändrings **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft To-Do

- Microsoft Stream

Mer information finns i:

- [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nytt stöd för Self-Service lösen ords återställning från Windows 7/8/8.1 Lås skärmen

**Typ:** Ny funktions **tjänst kategori:** SSPR **produkt kapacitet:** användarautentisering

När du har konfigurerat den här nya funktionen visas en länk för att återställa lösen ordet från **Lås** skärmen på en enhet som kör Windows 7, Windows 8 eller Windows 8,1. Genom att klicka på länken vägleds användaren genom samma flöde för lösen ords återställning som i webbläsaren.

Mer information finns i [så här aktiverar du återställning av lösen ord från Windows 7, 8 och 8,1](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändrings meddelande: auktoriseringsregler kommer inte längre att vara tillgängliga för åter användning

**Typ:** Plan för ändrings **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Från den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringsnivåer för appar. Den här säkerhets ändringen hjälper till att placera Azure AD i linje med OAuth-specifikationen och tillämpas på både v1-och v2-slutpunkter.

Om appen återanvänder auktoriseringsregler för att hämta tokens för flera resurser, rekommenderar vi att du använder koden för att hämta en uppdateringstoken, och sedan använder den uppdateringstoken för att hämta ytterligare token för andra resurser. Det går bara att använda auktoriseringsregler en gång, men uppdaterade token kan användas flera gånger i flera resurser. En app som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – september 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I september 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Möt rekryterings program vara](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [snö](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), Join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback, Riverbed](../saas-apps/silverback-tutorial.md)Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO för Azure, SurveyMonkey, [sammankalla](../saas-apps/convene-tutorial.md), [dmarcian](../saas-apps/dmarcian-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Stöd för ytterligare metoder för att transformera anspråk

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Vi har introducerat nya omvandlings metoder för anspråk, ToLower () och ToUpper () som kan tillämpas på SAML-tokens från den SAML-baserade **konfigurations sidan för en enda Sign-On** .

Mer information finns i [så här anpassar du anspråk som utfärdats i SAML-token för företags program i Azure AD](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat SAML-baserat konfigurations gränssnitt för app (för hands version)

**Typ:** Ändrad funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Som en del av det uppdaterade SAML-baserade konfigurations gränssnittet för SAML-baserade appar får du:

- En uppdaterad genom gångs upplevelse för att konfigurera dina SAML-baserade appar.

- Mer information om vad som saknas eller är felaktigt i konfigurationen.

- Möjlighet att lägga till flera e-postadresser för meddelande om förfallo datum för certifikat.

- Nya omvandlings metoder för anspråk, ToLower () och ToUpper () med mera.

- Ett sätt att ladda upp ditt eget token signerings certifikat för dina företags program.

- Ett sätt att ange NameID-formatet för SAML-appar och ett sätt att ange NameID-värdet som katalog tillägg.

Om du vill aktivera den här uppdaterade vyn klickar du på länken **prova vår nya upplevelse** överst på sidan för **enkel inloggning** . Mer information finns i [Självstudier: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar i Azure Active Directory IP-adressintervall

**Typ:** Plan för ändrings **tjänst kategori:** annan **produkt kapacitet:** plattform

Vi presenterar större IP-intervall för Azure AD, vilket innebär att om du har konfigurerat Azure AD IP-adressintervall för brand väggar, routrar eller nätverks säkerhets grupper måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra inställningarna för IP-intervall för brand väggen, routern eller nätverks säkerhets grupper igen när Azure AD lägger till nya slut punkter.

Nätverks trafiken flyttas till de nya intervallen under de kommande två månaderna. Om du vill fortsätta med en oavbruten tjänst måste du lägga till dessa uppdaterade värden till dina IP-adresser före den 10 september 2018:

- 20.190.128.0/18

- 40.126.0.0/18

Vi rekommenderar starkt att inte ta bort de gamla IP-adressintervall förrän all nätverks trafik har flyttats till de nya intervallen. För uppdateringar om flytten och för att lära dig när du kan ta bort de gamla intervallen, se [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändrings meddelande: auktoriseringsregler kommer inte längre att vara tillgängliga för åter användning

**Typ:** Plan för ändrings **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Från den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringsnivåer för appar. Den här säkerhets ändringen hjälper till att placera Azure AD i linje med OAuth-specifikationen och tillämpas på både v1-och v2-slutpunkter.

Om appen återanvänder auktoriseringsregler för att hämta tokens för flera resurser, rekommenderar vi att du använder koden för att hämta en uppdateringstoken, och sedan använder den uppdateringstoken för att hämta ytterligare token för andra resurser. Det går bara att använda auktoriseringsregler en gång, men uppdaterade token kan användas flera gånger i flera resurser. En app som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergerad säkerhets informations hantering för självbetjänings lösen ord (SSPR) och Multi-Factor Authentication (MFA)

**Typ:** Ny funktions **tjänst kategori:** SSPR **produkt kapacitet:** användarautentisering

Den här nya funktionen hjälper användare att hantera sina säkerhets uppgifter (t. ex. telefonnummer, mobilappar osv.) för SSPR och MFA på en enda plats och erfarenhet. jämfört med tidigare, var det gjordes på två olika platser.

Denna konvergerade upplevelse fungerar också för personer som använder antingen SSPR eller MFA. Om din organisation inte tvingar MFA-eller SSPR-registrering kan användarna fortfarande registrera alla MFA-eller SSPR säkerhets uppgifter som tillåts av din organisation från portalen Mina appar.

Det här är en valbar, offentlig för hands version. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en vald grupp eller för alla användare i en klient organisation. Mer information om konvergerad upplevelse finns i [bloggen för konvergerad upplevelse](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nya inställningar för HTTP-Only cookies i Azure AD Application Proxy-appar

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för App-proxy: Access Control

Det finns en ny inställning som kallas **http-Only cookies** i dina Application Proxy-appar. Den här inställningen ger extra säkerhet genom att inkludera flaggan HTTPOnly i HTTP-svars huvudet för både programproxy-åtkomst och sessionscookies, stoppa åtkomst till cookien från ett skript på klient sidan och ytterligare förhindra åtgärder som att kopiera eller ändra cookien. Även om den här flaggan inte har använts tidigare har dina cookies alltid krypterats och skickats via en TLS-anslutning för att skydda mot felaktiga ändringar.

Den här inställningen är inte kompatibel med appar som använder ActiveX-kontroller, t. ex. fjärr skrivbord. Om du är i den här situationen rekommenderar vi att du inaktiverar den här inställningen.

Mer information om inställningen för att HTTP-Only cookies finns i [Publicera program med Azure AD-programproxy](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) för Azure-resurser har stöd för resurs typer för hanterings grupper

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Inställningarna för just-in-Time-aktivering och tilldelning kan nu användas för resurs typer för hanterings grupper, precis som du redan har för prenumerationer, resurs grupper och resurser (till exempel virtuella datorer, App Services med mera). Dessutom kan alla med en roll som ger administratörs åtkomst till en hanterings grupp identifiera och hantera resursen i PIM.

Mer information om PIM och Azure-resurser finns i [identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Program åtkomst (för hands version) ger snabbare åtkomst till Azure AD-portalen

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

I dag, när du aktiverar en roll med hjälp av PIM, kan det ta över 10 minuter innan behörigheterna börjar gälla. Om du väljer att använda program åtkomst, som för närvarande finns i en offentlig för hands version, kan administratörer få åtkomst till Azure AD-portalen så snart aktiverings förfrågan har slutförts.

För närvarande stöder program åtkomst endast Azure AD Portal-upplevelsen och Azure-resurser. Mer information om PIM och program åtkomst finns i [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – augusti 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I augusti 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Hornbill](../saas-apps/hornbill-tutorial.md), [bridgeline-obundna](../saas-apps/bridgelineunbound-tutorial.md), [recept Labs – mobil-och Webbtestering](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), [sätt vi gör](../saas-apps/waywedo-tutorial.md), [Spotinst](../saas-apps/spotinst-tutorial.md), [Promaster (av Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [dokumentation](../saas-apps/dossier-tutorial.md), [N2F – utgifts rapporter](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Inbyggt stöd för Tableau är nu tillgängligt i Azure AD-programproxy

**Typ:** Ändrade funktions **tjänst kategori:** **produkt funktion** för App-proxy: Access Control

Med vår uppdatering från OpenID ansluter du till protokollet OAuth 2,0 Code Grant för vårt pre-Authentication-protokoll, behöver du inte längre göra någon ytterligare konfiguration för att använda Tableau med Application Proxy. Protokoll ändringen hjälper också till att programproxy bättre stöder fler moderna appar genom att bara använda HTTP-omdirigeringar som ofta stöds i Java Script-och HTML-taggar.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nytt stöd för att lägga till Google som en identitets leverantör för B2B-gäst användare i Azure Active Directory (för hands version)

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C

Genom att konfigurera Federation med Google i din organisation kan du låta inbjudna användare logga in till dina delade appar och resurser med hjälp av sitt befintliga Google-konto, utan att behöva skapa ett personligt Microsoft-konto (MSA: er) eller ett Azure AD-konto.

Det här är en valbar, offentlig för hands version. Mer information om Google Federation finns i [lägga till Google som en identitets leverantör för B2B-gäst användare](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av Azure Active Directory e-postaviseringar

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** hantering av identitets livs cykel

Azure Active Directory (Azure AD)-e-postmeddelanden har nu en uppdaterad design, samt ändringar av avsändar-e-postadressen och avsändar visnings namnet när de skickas från följande tjänster:

- Åtkomst granskningar för Azure AD
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Företags program som förfaller certifikat meddelanden
- Meddelanden om företags-app-etablering

E-postaviseringarna skickas från följande e-postadress och visnings namn:

- E-post adress: azure-noreply@microsoft.com
- Visnings namn: Microsoft Azure

Ett exempel på en del nya e-postdesigner och mer information finns i [e-postmeddelanden i Azure AD PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitets loggar är nu tillgängliga via Azure Monitor

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Azure AD-aktivitets loggarna är nu tillgängliga i den offentliga för hands versionen för Azure Monitor (Azures plattforms oberoende övervaknings tjänst). Azure Monitor erbjuder långsiktig kvarhållning och sömlös integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning genom att vidarebefordra dina loggfiler till ditt eget Azure Storage-konto.

- Sömlös SIEM-integrering, utan att du behöver skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analys verktyg eller incident hanterings lösningar.

Mer information om de här nya funktionerna finns i våra blogg [Azure AD-aktivitets loggar i Azure Monitor Diagnostics finns nu i den offentliga för hands versionen och i](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) vår dokumentation, [Azure Active Directory aktivitets loggar i Azure Monitor (för hands version)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information om villkorlig åtkomst som lagts till i rapporten Azure AD-inloggningar

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** identitets säkerhet & skydd

Med den här uppdateringen kan du se vilka principer som utvärderas när en användare loggar in, tillsammans med princip resultatet. Dessutom innehåller rapporten nu den typ av klient program som används av användaren, så att du kan identifiera äldre protokoll trafik. Nu kan du söka efter ett korrelations-ID som finns i det användar meddelande som visas och som kan användas för att identifiera och felsöka den matchande inloggnings förfrågan.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggnings aktivitets loggar

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Med introduktionen av fältet **klient app** i inloggnings aktivitets loggarna kan kunder nu se användare som använder äldre autentiseringar. Kunder kommer att ha åtkomst till den här informationen med hjälp av inloggnings Microsoft Graph-API: t eller via inloggnings aktivitets loggarna i Azure AD portal där du kan använda **klientens app** -kontroll för att filtrera på äldre autentiseringar. Mer information finns i dokumentationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juli 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I juli 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Innovation Hub](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [viss admin SSO](../saas-apps/certainadminsso-tutorial.md), PSUC-mellanlagring, [iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [skärm utsändning-O-Matic](../saas-apps/screencast-tutorial.md), Powerschool Unified klass rum, [Eli onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar support](../saas-apps/bomgarremotesupport-tutorial.md), [NimbleX](../saas-apps/nimblex-tutorial.md), [Föreställ dig webvision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [kunskaps bas](../saas-apps/skillsbase-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användar etablering SaaS app integrations – juli 2018

**Typ:** Ny funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** integration från tredje part

Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användar identiteter i SaaS program som Dropbox, Salesforce, ServiceNow och mycket annat. I juli 2018 har vi lagt till stöd för användar etablering för följande program i Azure AD App-galleriet:

- [Cisco-WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för Sync – ett enklare sätt att åtgärda överblivna och duplicerade attributvärden för synkroniseringsfel

**Typ:** Ny funktions **tjänst kategori:** AD Connect **produkt kapacitet:** övervakning & rapportering

Azure AD Connect Health introducerar självbetjänings reparation för att hjälpa dig att markera och åtgärda synkroniseringsfel. Den här funktionen felsöker dubbletter av synkroniseringsfel och korrigerar objekt som är överblivna från Azure AD. Den här diagnostiken har följande fördelar:

- Minskar synkroniseringsfel för dubblerade attribut, och tillhandahåller specifika korrigeringar

- Använder en korrigering för dedikerade Azure AD-scenarier och löser fel i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [diagnostisera och åtgärda dubblerade synkroniseringsfel](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av inloggnings upplevelser för Azure AD och MSA

**Typ:** Ändrad funktions **tjänst kategori:** Azure AD- **produkt kapacitet:** användarautentisering

Vi har uppdaterat användar gränssnittet för Microsofts onlinetjänster inloggnings upplevelse, till exempel för Office 365 och Azure. Den här ändringen gör att skärmarna blir mindre röriga och mer enkla. Mer information om den här ändringen finns i avsnittet [kommande förbättringar av inloggnings upplevelsen för Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** hantering av identitets livs cykel

Den senaste versionen av Azure AD Connect innehåller:

- Fel korrigeringar och support uppdateringar

- Allmän tillgänglighet för Ping-Federate-integrering

- Uppdateringar till den senaste SQL 2012-klienten

Mer information om den här uppdateringen finns [Azure AD Connect: versions historik](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Uppdateringar av användar gränssnittet för slutanvändaren

**Typ:** Ändrad funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Vi uppdaterar godkännande strängen i användar gränssnittet för TOU slutanvändare.

**Aktuell text.** För att få åtkomst till [tenantName]-resurser måste du godkänna användnings villkoren.<br>**Ny text.** För att få åtkomst till [tenantName]-resursen måste du läsa användnings villkoren.

**Aktuell text:** Om du väljer att acceptera innebär det att du godkänner alla ovanstående användnings villkor.<br>**Ny text:** Klicka på acceptera för att bekräfta att du har läst och förstått användnings villkoren.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Direkt autentisering stöder nu äldre protokoll och appar. Följande begränsningar stöds nu fullt ut:

- Användar inloggningar till äldre Office-klientprogram, Office 2010 och Office 2013, utan att kräva modern autentisering.

- Åtkomst till kalender delning och information om ledig/upptagen i Exchange hybrid-miljöer i Office 2010.

- Användar inloggningar till Skype för företag-klient program utan att kräva modern autentisering.

- Användar inloggningar till PowerShell version 1,0.

- Apple-Programmet för enhetsregistrering (Apple DEP) med installations assistenten för iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konvergerad hantering av säkerhets information för självbetjäning för återställning av lösen ord och Multi-Factor Authentication

**Typ:** Ny funktions **tjänst kategori:** SSPR **produkt kapacitet:** användarautentisering

Med den här nya funktionen kan användarna hantera sina säkerhets uppgifter (t. ex. telefonnummer, e-postadress, mobilapp osv.) för självbetjäning för återställning av lösen ord (SSPR) och Multi-Factor Authentication (MFA) i en och samma upplevelse. Användarna kommer inte längre behöva registrera samma säkerhets information för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har antingen SSPR eller MFA.

Om en organisation inte tillämpar MFA-eller SSPR-registrering kan användarna registrera sina säkerhets uppgifter via portalen **Mina appar** . Därifrån kan användare registrera alla metoder som är aktiverade för MFA eller SSPR.

Det här är en valbar, offentlig för hands version. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en viss grupp användare eller alla användare i en klient organisation.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd appen Microsoft Authenticator för att verifiera din identitet när du återställer ditt lösen ord

**Typ:** Ändrade funktions **tjänst kategori:** SSPR **produkt kapacitet:** användarautentisering

Med den här funktionen kan icke-administratörer verifiera sin identitet när de återställer ett lösen ord med hjälp av ett meddelande eller kod från Microsoft Authenticator (eller någon annan Authenticator-app). När administratörer aktiverar den här metoden för lösen ords återställning via självbetjäning kan användare som har registrerat en mobilapp via aka.ms/mfasetup eller aka.ms/setupsecurityinfo använda sina mobilappar som en verifierings metod när de återställer sitt lösen ord.

Mobile App-aviseringar kan bara aktive ras som en del av en princip som kräver två metoder för att återställa ditt lösen ord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändrings meddelande: säkerhets korrigering i det delegerade auktoriseringsvärdet för appar som använder Azure AD aktivitets loggar API

**Typ:** Plan för ändrings **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

På grund av vår starkare säkerhet har vi haft gjort en ändring i behörigheterna för appar som använder ett delegerat auktoriseringsarkiv för att få åtkomst till [API: er för Azure AD-aktivitets loggar](../reports-monitoring/concept-reporting-api.md). Denna ändring sker den **26 juni 2018**.

Om någon av dina appar använder Azure AD aktivitets logg-API: er, följer du dessa steg för att se till att appen inte bryts när ändringen har utförts.

**Uppdatera dina program behörigheter**

1. Logga in på Azure Portal, Välj **Azure Active Directory** och välj sedan app- **registreringar**.
2. Välj din app som använder Azure AD aktivitets loggar API, Välj **Inställningar**, Välj **nödvändiga behörigheter** och välj sedan **Windows-Azure Active Directory** -API: et.
3. I avsnittet **delegerade behörigheter** i bladet **Aktivera åtkomst** markerar du kryss rutan bredvid **Läs katalog** data och väljer sedan **Spara**.
4. Välj **bevilja behörigheter** och välj sedan **Ja**.

    >[!Note]
    >Du måste vara global administratör för att bevilja behörighet till appen.

Mer information finns i avsnittet [Granting Permissions](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) i kraven för att komma åt Azure AD repor ting API-artikeln.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS-inställningar för att ansluta till Azure AD-tjänster för PCI DSS kompatibilitet

**Typ:** Ny funktions **tjänst kategori:** N/A **produkt kapacitet:** plattform

Transport Layer Security (TLS) är ett protokoll som tillhandahåller sekretess-och data integritet mellan två kommunicerande program och är det mest distribuerade säkerhets protokollet som används idag.

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure SOCKETS Layer (SSL) måste inaktive ras för att aktivera nya och säkrare app-protokoll, med krav från och med den **30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräver PCI DSS-efterlevnad måste du inaktivera TLS 1,0. Det finns flera versioner av TLS, men TLS 1,2 är den senaste versionen som är tillgänglig för Azure Active Directory Services. Vi rekommenderar starkt att du flyttar direkt till TLS 1,2 för kombinationerna både klient/server och webbläsare/Server.

Inaktuella webbläsare kanske inte stöder nyare TLS-versioner, till exempel TLS 1,2. Om du vill se vilka versioner av TLS som stöds av din webbläsare går du till webbplatsen för [SSL-labbet Qualys](https://www.ssllabs.com/) och klickar på **testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av webbläsaren och helst bara aktiverar TLS 1,2.

**Så här aktiverar du TLS 1,2, av webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med Internet Explorer)**

    1. Öppna Internet Explorer, Välj **verktyg**  >  **Internet alternativ**  >  **Avancerat**.
    2. I avsnittet **säkerhet** väljer du **Använd TLS 1,2** och väljer sedan **OK**.
    3. Stäng alla webbläsarfönster och starta om Internet Explorer.

- **Google Chrome**

    1. Öppna Google Chrome, Skriv *Chrome://Settings/* i adress fältet och tryck på **RETUR**.
    2. Expandera **avancerade** alternativ, gå till **system** -ytan och välj **Öppna proxyinställningar**.
    3. I rutan **Internet egenskaper** väljer du fliken **Avancerat** , går till **säkerhets** avsnittet, väljer **Använd TLS 1,2** och väljer sedan **OK**.
    4. Stäng alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, Skriv *About: config* i adress fältet och tryck på **RETUR**.
    2. Sök efter termen, *TLS* och välj sedan posten **Security. TLS. version. Max** .
    3. Ställ in värdet på **3** för att tvinga webbläsaren att använda upp till version TLS 1,2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60,0 stöder TLS 1,3, så du kan också ställa in Security. TLS. version. Max-värdet på **4**.

    4. Stäng alla webb läsar fönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I juni 2018 har vi lagt till dessa 15 nya appar med stöd för federation i app-galleriet:

[Skytap](../saas-apps/skytap-tutorial.md), [att reglera musik](../saas-apps/settlingmusic-tutorial.md), [SAML 1,1 token Enabled LOB-app](../saas-apps/saml-tutorial.md), [överstämning](../saas-apps/supermood-tutorial.md), [AutoTask](../saas-apps/autotaskendpointbackup-tutorial.md), [Endpoint backup](../saas-apps/autotaskendpointbackup-tutorial.md), [skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [förutse CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD Password Protection finns i offentlig för hands version

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** användarautentisering

Använd Azure AD Password Protection för att undvika att gissa lösen ord från din miljö lätt gissas. Genom att ta bort dessa lösen ord kan du minska risken för angrepp från angrepp av lösen ord.

Mer specifikt hjälper Azure AD Password Protection dig att:

- Skydda organisationens konton både i Azure AD och Windows Server Active Directory (AD).
- Hindrar användarna från att använda lösen ord på en lista med fler än 500 av de lösen ord som används oftast och över 1 000 000 tecken ersättning för dessa lösen ord.
- Administrera Azure AD Password Protection från en enda plats i Azure AD-portalen för både Azure AD och den lokala Windows Server AD.

Mer information om lösen ords skydd i Azure AD finns i [eliminera Felaktiga lösen ord i din organisation](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nya princip mal len "alla gäster" som skapas när användnings villkoren skapas

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Under skapandet av dina användnings villkor skapas även en ny mall för villkorlig åtkomst för "alla gäster" och "alla appar". Den här nya princip mal len tillämpar den nyligen skapade ToU, vilket effektiviserar skapande och tvångs processen för gäster.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny princip mal len anpassad princip för villkorlig åtkomst som skapats när användnings villkoren skapas

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** styrning

Under skapandet av dina användnings villkor skapas även en ny princip mal len anpassad princip för villkorlig åtkomst. Med den här nya princip mal len kan du skapa ToU och sedan omedelbart gå till bladet skapa princip för villkorlig åtkomst, utan att behöva navigera manuellt i portalen.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Ny och omfattande vägledning om hur du distribuerar Azure AD Multi-Factor Authentication

**Typ:** Ny funktions **tjänst kategori:** annan **produkt kapacitet:** identitets säkerhet & skydd

Vi har publicerat nya steg-för-steg-instruktioner om hur du distribuerar Azure AD Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa distributions guiden för MFA går du till lagrings platsen för [identitets distribution](./active-directory-deployment-plans.md) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD-delegerade app Management-roller finns i offentlig för hands version

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags program: Access Control

Administratörer kan nu delegera program hanterings uppgifter utan att tilldela rollen global administratör. De nya rollerna och funktionerna är:

- **Nya standard administratörs roller för Azure AD:**

    - **Program administratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, SSO-inställningar, app-tilldelningar och licensiering, inställningar för App-proxy och godkännande (förutom för Azure AD-resurser).

    - **Moln program administratör.** Ger alla funktioner för program administratör, förutom App proxy, eftersom den inte tillhandahåller lokal åtkomst.

    - **Programutvecklare.** Ger möjlighet att skapa registrerings program, även om alternativet **Tillåt användare att registrera appar** är inaktiverat.

- **Ägarskap (Konfigurera per app-registrering och per företags-app, som liknar gruppens ägarskaps process:**

    - **Ägare av app-registrering.** Ger möjlighet att hantera alla aspekter av ägd app-registrering, inklusive app-manifestet och lägga till ytterligare ägare.

    - **Ägare av företags program.** Ger möjlighet att hantera många aspekter av ägda företags program, inklusive SSO-inställningar, app-tilldelningar och medgivande (förutom för Azure AD-resurser).

Mer information om den offentliga för hands versionen finns [i Azure AD-delegerade program hanterings roller finns i offentlig för hands version!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blogg. Mer information om roller och behörigheter finns [i tilldela administratörs roller i Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>ExpressRoute-support ändringar

**Typ:** Plan för ändrings **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** plattform

Program vara som ett tjänst erbjudande, t. ex. Azure Active Directory (Azure AD) är utformad för att fungera bäst genom att gå direkt via Internet, utan att kräva ExpressRoute eller någon annan privat VPN-tunnel. På grund av detta upphör vi att stödja ExpressRoute för Azure AD-tjänster via Azures offentliga peering och Azure-communities i Microsoft-peering, den **1 augusti 2018**. Alla tjänster som påverkas av den här ändringen kan observera att Azure AD-trafik gradvis byter från ExpressRoute till Internet.

Medan vi ändrar vår support vet vi också att det fortfarande finns situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentisering. Därför fortsätter Azure AD att ge stöd för IP-adressintervall per innehavare med hjälp av ExpressRoute och tjänster som redan ingår i Microsoft-peering med "andra Office 365 Online Services"-communityn. Om dina tjänster påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder Azures offentliga peering.** Flytta till Microsoft-peering och registrera dig för de **andra Office 365 Online Services-communityn (12076:5100)** . Mer information om hur du flyttar från offentlig Azure-peering till Microsoft-peering finns i artikeln [flytta en offentlig peering till Microsoft-peering](../../expressroute/how-to-move-peering.md) .

- **Om du använder Microsoft-peering.** Registrera dig för den **andra Office 365 online service-communityn (12076:5100)** . Mer information om krav för routning finns i [avsnittet stöd för BGP-communities](../../expressroute/expressroute-routing.md#bgp) i artikeln ExpressRoute routnings krav.

Om du måste fortsätta att använda dedikerade kretsar måste du prata med ditt Microsoft-konto team om hur du får behörighet att använda den **andra Office 365 online service-communityn (12076:5100)** . Den MS Office-hanterade gransknings tavlan kontrollerar om du behöver dessa kretsar och se till att du förstår de tekniska konsekvenserna av att hålla dem. Obehöriga prenumerationer som försöker skapa väg filter för Office 365 kommer att få ett fel meddelande.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph-API: er för administrativa scenarier för TOU

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** utvecklings miljö

Vi har lagt till Microsoft Graph-API: er för administration av Azure ADs användnings villkor. Du kan skapa, uppdatera och ta bort användnings villkoren.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till Azure AD-slutpunkt för flera klient organisationer som en identitets leverantör i Azure AD B2C

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Med anpassade principer kan du nu lägga till den vanliga Azure AD-slutpunkten som en identitets leverantör i Azure AD B2C. På så sätt kan du ha en enda post punkt för alla Azure AD-användare som loggar in i dina program. Mer information finns i [Azure Active Directory B2C: Tillåt att användare loggar in på en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använd interna URL: er för att komma åt appar från var som helst med inloggnings tillägget för Mina appar och Azure-AD-programproxy

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** SSO

Användare kan nu komma åt program via interna URL: er även när de befinner sig utanför företagets nätverk med hjälp av tillägget Mina appar säker inloggning för Azure AD. Detta fungerar med alla program som du har publicerat med Azure AD-programproxy, i alla webbläsare som också har åtkomst panels tillägget installerat. Funktionen för URL-omdirigering aktive ras automatiskt när en användare loggar in i tillägget. Tillägget är tillgängligt för hämtning på [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-data i Europa för Europa-kunder

**Typ:** Ny funktions **tjänst kategori:** annan **produkt kapacitet:** GoLocal

Kunder i Europa kräver sina data för att hållas kvar i Europa och inte replikeras utanför Europeiska Data Center för att möta sekretess och europeiska lagar. Den här [artikeln](./active-directory-data-storage-eu.md) innehåller detaljerad information om vilken identitets information som lagras i Europa och som även innehåller information om information som kommer att lagras utanför de europeiska data centren.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Ny användar etablering SaaS app integrations – maj 2018

**Typ:** Ny funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** integration från tredje part

Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användar identiteter i SaaS program som Dropbox, Salesforce, ServiceNow och mycket annat. För maj 2018 har vi lagt till stöd för användar etablering för följande program i Azure AD App-galleriet:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns i [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD åtkomst granskningar av grupper och app-åtkomst innehåller nu återkommande granskningar

**Typ:** Ny funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** styrning

Åtkomst granskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomst granskningar av grupp medlemskap och program tilldelningar för att automatiskt upprepas med jämna mellanrum, till exempel varje månad eller kvartal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitets loggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Azure AD-aktivitets loggar, som innehåller inloggningar och gransknings loggar, är nu tillgängliga via Microsoft Graph API. Vi har exponerat två slut punkter via Microsoft Graph API för att komma åt dessa loggar. Ta en titt på våra [dokument](../reports-monitoring/concept-reporting-api.md) för program mässig åtkomst till Azure AD repor ting-API: er för att komma igång.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösnings upplevelsen och lämna en organisation

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C

**Just-in-Time-inlösen:** När du delar en resurs med en gäst användare som använder B2B-API – behöver du inte skicka ut en särskild inbjudan via e-post. I de flesta fall kan gäst användaren komma åt resursen och tas genom inlösnings upplevelsen just i tiden. Ingen mer påverkan på grund av missade e-postmeddelanden. Du behöver inte längre be gäst användarna om att du klickade på den här inlösnings länken systemet skickade? ". Det innebär att när SPO använder Inbjudnings hanteraren – moln bilagor kan ha samma kanoniska URL för alla användare – internt och externt – i alla tillstånd för inlösen.

**Modern inlösnings upplevelse:** Ingen fler delnings sida för upplösning av delad skärm. Användarna kommer att se en modern medgivande upplevelse med den bjudande organisationens sekretess policy, precis som de gör för appar från tredje part.

**Gäst användare kan lämna organisationen:** När en användares relation med en organisation är över kan de själv betjäna lämna organisationen. Inga fler anrop till den svarande organisationens administratör till "tas bort", inga fler upphöjnings support biljetter.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2018

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I maj 2018 har vi lagt till dessa 18 nya appar med stöd för federation i vårt app-Galleri:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty styr, [Yodeck](../saas-apps/infogix-tutorial.md), [JAMF Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [ENVI MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate primtal](../saas-apps/adobecaptivateprime-tutorial.md), [Montagelayout online](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), openrulle, [Arc Publishing-SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [riskmaterial](../saas-apps/riskware-tutorial.md), [flock](../saas-apps/flock-tutorial.md), [ReviewSnap](../saas-apps/reviewsnap-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nya steg-för-steg-distributions guider för Azure Active Directory

**Typ:** Ny funktions **tjänst kategori:** annan **produkt kapacitet:** katalog

Nya steg-för-steg-anvisningar om hur du distribuerar Azure Active Directory (Azure AD), inklusive självbetjäning för återställning av lösen ord (SSPR), enkel inloggning (SSO), villkorlig åtkomst, App proxy, användar etablering, Active Directory Federation Services (AD FS) (ADFS) för direktautentisering (PTA) och ADFS till hash-synkronisering för lösen ord (PHS).

Om du vill visa distributions guiderna går du till lagrings platsen för [identitets distribution](./active-directory-deployment-plans.md) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Sökning efter företags program – Läs in fler appar

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Har du problem med att hitta dina program/tjänstens huvud namn? Vi har lagt till möjligheten att läsa in fler program i företags program listan Alla program. Som standard visar vi 20 program. Nu kan du klicka på, **läsa in mer** om du vill visa fler program.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maj-versionen av AADConnect innehåller en offentlig för hands version av integreringen med PingFederate, viktiga säkerhets uppdateringar, många fel korrigeringar och nya fantastiska nya fel söknings verktyg.

**Typ:** Ändrad funktions **tjänst kategori:** AD Connect **produkt kapacitet:** hantering av identitets livs cykel

Maj-versionen av AADConnect innehåller en offentlig för hands version av integreringen med PingFederate, viktiga säkerhets uppdateringar, många fel korrigeringar och nya fantastiska nya fel söknings verktyg. Du hittar viktig information [här](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Åtkomst granskningar för Azure AD: Använd automatiskt

**Typ:** Ändrad funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** styrning

Åtkomst granskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera att automatiskt tillämpa granskarens ändringar av gruppen eller appen när åtkomst granskningen är klar. Administratören kan också ange vad som händer med användarens fortsatta åtkomst om granskarna inte svarade, ta bort åtkomst, behålla åtkomst eller vidta system rekommendationer.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-token kan inte längre returneras med frågan response_mode för nya appar.

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Appar som skapats den 25 april 2018 kommer inte längre att kunna begära en **id_token** med hjälp av **fråge** response_mode.  Detta medför att Azure AD infogas med OIDC-specifikationerna och hjälper till att minska din appars attack yta.  Appar som skapats före den 25 april 2018 blockeras inte från att använda **frågan** response_mode med en response_type av **id_token**.  Det fel som returnerades när en id_token från Azure AD begärdes är **AADSTS70007: ' fråga ' är inte ett giltigt värde för ' response_mode ' när du begär en token**.

**Fragment** -och **form_posts** response_modes fortsätta att fungera – när du skapar nya program objekt (till exempel för användning av App-proxy) bör du se till att använda någon av dessa response_modes innan du skapar ett nytt program.

---

## <a name="april-2018"></a>April 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C åtkomsttoken är GA

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Nu kan du få åtkomst till webb-API: er som skyddas av Azure AD B2C att använda åtkomsttoken. Funktionen flyttas från offentlig för hands version till GA. GRÄNSSNITTs upplevelsen för att konfigurera Azure AD B2C program och webb-API: er har förbättrats och andra mindre förbättringar har gjorts.

Mer information finns i [Azure AD B2C: begär åtkomsttoken](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa enkel inloggnings konfiguration för SAML-baserade program

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

När du konfigurerar SAML-baserade SSO-program kan du testa integrationen på konfigurations sidan. Om du stöter på ett fel under inloggningen kan du ange felet i test upplevelsen och Azure AD ger dig lösnings steg för att lösa problemet.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/view-applications-portal.md)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Användnings villkoren för Azure AD har nu rapporter ATS per användare

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

Administratörer kan nu välja en specifik ToU och se alla användare som har samtyckt till den ToU och vilket datum/tid det tog.

Mer information finns i [funktionen användnings villkor för Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riskfylld IP för AD FS extra näts utelåsning

**Typ:** Ny funktions **tjänst kategori:** annan **produkt kapacitet:** övervakning & rapportering

Connect Health stöder nu möjligheten att identifiera IP-adresser som överskrider tröskelvärdet på misslyckade U/P-inloggningar per timme eller per dag. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antal misslyckade inloggningar som genererats per timme/dag med anpassningsbart tröskelvärde.
- E-postbaserade aviseringar som visar när en speciell IP-adress har överskridit tröskelvärdet för misslyckade U/P-inloggningar per timme/dag.
- Ett nedladdnings alternativ för att göra en detaljerad analys av data

Mer information finns i avsnittet om [riskfyllda IP-rapporter](../hybrid/how-to-connect-health-adfs.md).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel app-konfiguration med metadatafil eller URL

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

På sidan företags program kan administratörer Ladda upp en SAML-metadatafil för att konfigurera SAML-baserad inloggning för Azure AD-galleriet och program som inte är Galleri.

Dessutom kan du använda Azure AD Application Federation Metadata-URL för att konfigurera SSO med mål programmet.

Mer information finns i [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory program galleriet](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Användningsvillkor nu allmänt tillgänglig

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad


Användnings villkoren för Azure AD har flyttats från offentlig för hands version till allmänt tillgängliga.

Mer information finns i [funktionen användnings villkor för Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C


Nu kan du ange vilka partner organisationer du vill dela och samar beta med i Azure AD B2B-samarbete. Om du vill göra detta kan du välja att skapa en lista över vissa domäner som tillåter eller nekar. När en domän blockeras med hjälp av dessa funktioner kan anställda inte längre skicka inbjudningar till personer i den domänen.

Detta hjälper dig att kontrol lera åtkomsten till dina resurser och möjliggör en smidig upplevelse för godkända användare.

Denna B2B-samarbets funktion är tillgänglig för alla Azure Active Directory kunder och kan användas tillsammans med Azure AD Premium funktioner som villkorlig åtkomst och identitets skydd för mer detaljerad kontroll av när och hur externa företags användare loggar in och får åtkomst.

Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I april 2018 har vi lagt till dessa 13 nya appar med stöd för federation i vårt app-Galleri:

Kriterie HCM [, FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [dynamisk signal](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [konverterare nu](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta prestanda övervakaren](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), hylla, [SafetyNET](../saas-apps/safetynet-tutorial.md)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Bevilja B2B-användare i Azure AD åtkomst till dina lokala program (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** B2B **produkt kapacitet:** B2B/B2C

Som organisation som använder Azure Active Directory (Azure AD) B2B-samarbets funktioner för att bjuda in gäst användare från partner organisationer till din Azure AD, kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [bevilja B2B-användare i Azure AD åtkomst till dina lokala program](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Få självstudier om SSO-integrering från Azure Marketplace

**Typ:** Ändrad funktions **tjänst kategori:** annan **produkt kapacitet:** integration från tredje part

Om ett program som listas på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) har stöd för SAML-baserad enkel inloggning **, ger du den** integrerings självstudie som är kopplad till det programmet.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för automatisk användar etablering i Azure AD till SaaS-program

**Typ:** Ändrade funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** integration från tredje part

Tidigare kunde kunder som använder Azure Active Directory etablerings anslutningarna för SaaS program (till exempel Salesforce, ServiceNow och Box) uppleva långsamma prestanda om deras Azure AD-klienter innehåller över 100 000 kombinerade användare och grupper och de använde användar-och grupp tilldelningar för att avgöra vilka användare som ska tillhandahållas.

Den 2 april 2018 distribuerades betydande prestanda förbättringar till Azure AD Provisioning-tjänsten som avsevärt minskar den tid som krävs för att utföra inledande synkroniseringar mellan Azure Active Directory-och mål SaaS-program.

Det innebär att många kunder som hade första synkroniseringen till appar som tog flera dagar eller aldrig har slutförts, är nu inom några minuter eller timmar.

Mer information finns i [Vad händer under etableringen?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Lösen ords återställning via självbetjäning från Windows 10-Lås skärm för Hybrid Azure AD-anslutna datorer

**Typ:** Ändring av funktions **tjänst kategori:** självbetjäning för återställning av lösen ord för **produkter:** användarautentisering

Vi har uppdaterat Windows 10 SSPR-funktionen som innehåller stöd för datorer som är hybrid Azure AD-anslutna. Den här funktionen är tillgänglig i Windows 10-RS4 gör att användarna kan återställa sina lösen ord från Lås skärmen på en Windows 10-dator. Användare som är aktiverade och registrerade för lösen ords återställning via självbetjäning kan använda den här funktionen.

Mer information finns i [lösen ords återställning i Azure AD från inloggnings skärmen](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Mars 2018

### <a name="certificate-expire-notification"></a>Meddelande om förfallo datum för certifikat

**Typ:** Fast **tjänste kategori:** **produkt kapacitet** för företags appar: SSO

Azure AD skickar ett meddelande när ett certifikat för ett galleri eller ett program utanför galleriet håller på att gå ut.

Vissa användare fick inga meddelanden för företags program som kon figurer ATS för SAML-baserad enkel inloggning. Det här problemet har lösts. Azure AD skickar ett meddelande för certifikat som upphör att gälla om 7, 30 och 60 dagar. Du kan se den här händelsen i gransknings loggarna.

Mer information finns i:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-och GitHub identitets leverantörer i Azure AD B2C

**Typ:** Ny funktions **tjänst kategori:** B2C-konsument identitets hantering **produkt kapacitet:** B2B/B2C

Du kan nu lägga till Twitter eller GitHub som en identitets leverantör i Azure AD B2C. Twitter rör sig från offentlig för hands version till GA. GitHub släpps i offentlig för hands version.

Mer information finns i [Vad är Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webb läsar åtkomst med hjälp av Intune Managed Browser med Azure AD Application-based villkorlig åtkomst för iOS och Android

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

**Nu i offentlig för hands version!**

**INTUNE Managed Browser SSO:** Dina anställda kan använda enkel inloggning mellan interna klienter (t. ex. Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Stöd för Intune Managed Browser villkorlig åtkomst:** Du kan nu kräva att anställda använder Intune Managed Browser med programbaserade villkorliga åtkomst principer.

Läs mer om det här i [blogg inlägget](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns i:

- [Konfigurera programbaserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

- [Konfigurera principer för hanterade webbläsare](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdletar för App-proxy i PowerShell GA-modulen

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för App-proxy: Access Control

Stöd för Application Proxy-cmdlets finns nu i PowerShell GA-modulen! Detta kräver att du fortsätter att uppdatera i PowerShell-moduler – om du blir mer än ett år bakom kan vissa cmdlets sluta fungera.

Mer information finns i [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Inbyggda Office 365-klienter stöds av sömlös enkel inloggning med ett icke-interaktivt protokoll

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Användare som använder inbyggda Office 365-klienter (version 16.0.8730. xxxx och senare) får en tyst inloggnings upplevelse med sömlös SSO. Det här stödet tillhandahålls av tillägget för ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns i [Hur loggar du in på en intern klient med sömlös SSO-användning?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användarna får en tyst inloggning, med sömlös SSO, om ett program skickar inloggnings förfrågningar till Azure ADs klient slut punkter

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Användarna får en tyst inloggning, med sömlös SSO, om ett program (till exempel `https://contoso.sharepoint.com` ) skickar inloggnings begär anden till Azure Ads klient slut punkter – det vill säga, `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure Ads vanliga slut punkt ( `https://login.microsoftonline.com/common/<...>` ).

Mer information finns i [Azure Active Directory sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Du behöver bara lägga till en Azure AD-URL, i stället för två URL: er tidigare, till användarnas intranät zons inställningar för att distribuera sömlös SSO

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Om du vill distribuera sömlös SSO till dina användare behöver du bara lägga till en Azure AD-URL till användarnas intranät zons inställningar med hjälp av grup princip i Active Directory: `https://autologon.microsoftazuread-sso.com` . Tidigare var kunden tvungen att lägga till två URL: er.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I mars 2018 har vi lagt till dessa 15 nya appar med stöd för federation i vårt app-Galleri:

[BoxCryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant av FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), vtiger CRM, inblinkning, [amplitud](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin, Mercell](../saas-apps/bersin-tutorial.md) [, Trisotech](../saas-apps/mercell-tutorial.md) [digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgänglig

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Om du använder Azure AD Privileged Identity Management för katalog roller kan du nu använda PIM: s tidsbegränsade åtkomst-och tilldelnings funktioner för Azures resurs roller som prenumerationer, resurs grupper, Virtual Machines och andra resurser som stöds av Azure Resource Manager. Använd Multi-Factor Authentication när du aktiverar roller just-in-Time och Schemalägg aktiveringar i samordning med godkända ändrings fönster. Den här versionen lägger dessutom till förbättringar som inte är tillgängliga under den offentliga för hands versionen, inklusive ett uppdaterat gränssnitt, arbets flöden för godkännande och möjligheten att utöka roller som upphör snart att gälla och förnya förfallna roller.

Mer information finns i [PIM för Azure-resurser (för hands version)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägga till valfria anspråk till dina Apps-token (offentlig för hands version)

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Din Azure AD-App kan nu begära anpassade eller valfria anspråk i JWTs eller SAML-token.  Detta är anspråk på den användare eller klient som inte ingår som standard i token, på grund av storleks begränsningar eller tillämplighet.  Detta är för närvarande en offentlig för hands version av Azure AD-appar på v 1.0-och v 2.0-slutpunkterna.  I dokumentationen finns information om vilka anspråk som kan läggas till och hur du redigerar applikations manifestet för att begära dem.

Mer information finns i [valfria anspråk i Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Azure AD-dokument har uppdaterats för att anteckna stöd för PKCE, vilket möjliggör säkrare kommunikation under OAuth 2,0-auktoriseringskod.  Både S256 och klar text code_challenges stöds för v 1.0-och v 2.0-slutpunkter.

Mer information finns i [begära en auktoriseringskod](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Stöd för etablering av alla attributvärden för användare som är tillgängliga i Get_Workers-API: et för Workday

**Typ:** Ny funktions **tjänst kategori:** applikations etablering **produkt kapacitet:** integration från tredje part

Den allmänt tillgängliga för hands versionen av inkommande etablering från Workday till Active Directory och Azure AD har nu stöd för att extrahera och tillhandahålla alla attributvärden som är tillgängliga i Workday Get_Workers API. Detta lägger till stöd för hundratals ytterligare standard-och anpassade attribut utöver de som levererades med den ursprungliga versionen av arbets dagen inkommande etablerings anslutning.

Mer information finns i: [Anpassa listan över användar egenskaper för arbets dagar](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändra grupp medlemskap från dynamisk till statisk och vice versa

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för grupp hantering: samarbete

Det går att ändra hur medlemskap hanteras i en grupp. Detta är användbart om du vill behålla samma grupp namn och ID i systemet, så att alla befintliga referenser till gruppen fortfarande är giltiga. att skapa en ny grupp kräver uppdatering av dessa referenser.
Azure AD Admin Center har uppdaterats för att stödja den här funktionen. Kunder kan nu konvertera befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. De befintliga PowerShell-cmdletarna är också fortfarande tillgängliga.

Mer information finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat utloggnings beteende med sömlös SSO

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Även om användare uttryckligen har loggat ut från ett program som skyddas av Azure AD, kommer de automatiskt att loggas in igen med sömlös SSO om de försökte få åtkomst till ett Azure AD-program igen inom sitt företags nätverk från sina domänanslutna enheter. Med den här ändringen stöds utloggning.  Detta gör att användarna kan välja samma eller ett annat Azure AD-konto för att logga in igen i stället för att automatiskt logga in med sömlös SSO.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Version 1.5.402.0 av Application Proxy Connector lanserad

**Typ:** Ändrad funktions **tjänst kategori:** App proxy **produkt kapacitet:** identitet säkerhet & skydd

Den här anslutnings versionen distribueras gradvis till och med november. Den här nya anslutnings versionen innehåller följande ändringar:

- Nu anges cookies på domän nivå i stället för under domän nivå. Detta säkerställer en smidig SSO-upplevelse och förhindrar redundanta autentiseringsförsök.
- Stöd för chunked encoding-begäranden
- Förbättrad övervakning av anslutnings hälsa
- Flera fel korrigeringar och stabilitets förbättringar

Mer information finns i [förstå Azure AD-programproxy-kopplingar](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Februari 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Plan för ändrings **tjänst kategori:** katalog hantering **produkt kapacitet:** katalog

Navigerings miljön för att hantera användare och grupper har effektiviserats. Nu kan du navigera från katalog översikten direkt till listan över alla användare, med enklare åtkomst till listan över borttagna användare. Du kan också navigera från katalog översikten direkt till listan över alla grupper, med enklare åtkomst till inställningar för grupp hantering. Och även från sidan katalog översikt kan du söka efter en användare, grupp, företags program eller registrera appar.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggnings-och gransknings rapporter i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)

**Typ:** Ny funktions **tjänst kategori:** Azure Stack **produkt kapacitet:** övervakning av & rapportering

Azure AD aktivitets logg rapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet-instanser (Azure Kina 21Vianet). Följande loggar ingår:

- **Inloggnings loggar**  – innehåller alla inloggnings loggar som är kopplade till din klient.

- **Självbetjänings loggar för lösen ords granskning** – innehåller alla SSPR gransknings loggar.

- **Gransknings loggar för katalog hantering** – innehåller alla katalog hanterings relaterade gransknings loggar som användar hantering, program hantering och andra.

Med dessa loggar kan du få insikter om hur din miljö fungerar. Med dessa data kan du:

- Fastställ hur dina användare använder dina appar och tjänster.

- Felsök problem som hindrar användarna från att få jobbet gjort.

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använd rollen rapport läsare (icke-administrativ roll) om du vill visa Azure AD-aktivitets rapporter

**Typ:** Ny funktions **tjänst kategori:** rapportering av **produkt kapacitet:** övervakning av & rapportering

Som en del av kundernas synpunkter på att göra det möjligt för icke-administratörer att få åtkomst till Azure AD-aktivitets loggar har vi aktiverat möjligheten för användare som finns i rollen "rapport läsare" för att komma åt inloggningar och gransknings aktiviteter i Azure Portal samt använda Microsoft Graph-API.

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Anställningsnr-anspråk tillgängligt som användarattribut och användar-ID

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: SSO

Du kan konfigurera **Anställningsnr** som användar-ID och användarattribut för medlems användare och B2B-gäster i SAML-baserade inloggnings program från företags programmets användar gränssnitt.

Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad program hantering med jokertecken i Azure AD-programproxy

**Typ:** Ny funktions **tjänst kategori:** App proxy **produkt kapacitet:** användarautentisering

För att göra program distributionen enklare och minska din administrativa belastning har vi nu stöd för möjligheten att publicera program med hjälp av jokertecken. Om du vill publicera ett jokertecken, kan du följa standard flödet för program publicering, men använda ett jokertecken i interna och externa URL: er.

Mer information finns i [program med jokertecken i Azure Active Directory Application Proxy](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nya cmdletar som stöder konfiguration av programproxy

**Typ:** Ny funktions **tjänst kategori:** App proxy **produkt kapacitet:** plattform

Den senaste versionen av AzureAD PowerShell Preview-modulen innehåller nya cmdletar som gör det möjligt för kunder att konfigurera Application Proxy-program med hjälp av PowerShell.

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

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nya cmdletar som stöder konfiguration av grupper

**Typ:** Ny funktions **tjänst kategori:** App proxy **produkt kapacitet:** plattform

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdletar fanns tidigare tillgängliga i AzureADPreview-modulen och läggs nu till i AzureAD-modulen

De grupp-cmdletar som nu är tillgängliga för allmän tillgänglighet är:

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

**Typ:** Ny funktions **tjänst kategori:** AD Sync **produkt kapacitet:** plattform

Azure AD Connect är det bästa verktyget för att synkronisera data mellan Azure AD och lokala data källor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>I den här versionen införs ändringar av schema-och Synkroniseringsregel. Tjänsten Azure AD Connect-synkronisering utlöser en fullständig import och fullständig synkronisering efter en uppgradering. Information om hur du ändrar det här beteendet finns i [så här uppskjutar du fullständig synkronisering efter uppgraderingen](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Korrigerade problem**

- Åtgärda tids periods fönstret för bakgrunds aktiviteter för partitions filtrerings sidan när du växlar till nästa sida.

- En bugg har åtgärd ATS som orsakade åtkomst fel under den anpassade ConfigDB-åtgärden.

- En bugg har åtgärd ATS för att återställa från SQL-anslutningstimeout.

- Ett fel har åtgärd ATS där certifikat med SAN-jokertecken inte har förskotts kontroll.

- En bugg har åtgärd ATS som orsakar miiserver.exe krasch under exporten av Azure AD Connector.

- Ett fel där ett felaktigt lösen ord har loggat in på DOMÄNKONTROLLANTen vid körning gjorde att Azure AD Connect-guiden ändrade konfigurationen

**Nya funktioner och förbättringar**

- Programtelemetri – administratörer kan ändra den här data klassen på/av.

- Azure AD Health-data – administratörer måste besöka hälso portalen för att kontrol lera sina hälso inställningar. När tjänst principen har ändrats, kommer agenterna att läsa och tillämpa den.

- Tillagda konfigurations åtgärder för tillbakaskrivning av enheten och en förlopps indikator för sid initiering.

- Förbättrad allmän diagnostik med HTML-rapport och fullständig data insamling i en ZIP-Text/HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och ytterligare telemetri har lagts till för att säkerställa att serverns hälso tillstånd kan fastställas.

- Begränsa behörigheter som är tillgängliga för privilegierade konton på AD Connector-konto. För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när MSOL-kontot har skapats. Ändringarna påverkar Express installationer och anpassade installationer med automatiskt skapande av konto.

- Ändrade installations programmet till att inte kräva SA-behörighet vid ren installation av AADConnect.

- Nytt verktyg för att Felsöka synkroniseringsproblem för ett bestämt objekt. För närvarande söker verktyget efter följande saker:

    - UserPrincipalName matchnings fel mellan synkroniserat användar objekt och användar kontot i Azure AD-klienten.

    - Om objektet filtreras från synkroniseringen på grund av domän filtrering

    - Om objektet filtreras från synkroniseringen på grund av organisations enhetens (OU)-filtrering

- Nytt verktyg för att synkronisera den aktuella lösen ords hashen som lagras i den lokala Active Directory för ett särskilt användar konto. Verktyget kräver ingen lösen ords ändring.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune-appskydd principer som har lagts till för användning med Azure AD-programbaserad villkorlig åtkomst

**Typ:** Ändrad funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet & skydd

Vi har lagt till fler program som stöder programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med dessa godkända klient program.

Följande program kommer att läggas till i slutet av februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Mer information finns i:

- [Godkänt klient program krav](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdatering av mobil miljö

**Typ:** Ändrad funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

När användnings villkoren visas kan du nu Klicka på **Visa? Klicka här**. När du klickar på den här länken öppnas de användnings villkor som används internt på enheten. Oavsett tecken storlek i dokumentet eller skärmens storlek kan du zooma och läsa dokumentet efter behov.

---

## <a name="january-2018"></a>Januari 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I januari 2018 lades följande nya appar med stöd för federation i app-galleriet:

[IBM Openpages](../saas-apps/ibmopenpages-tutorial.md), [OneTrust Privacy Management Software](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk federerad Directory och [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Logga in med ytterligare risk upptäckt

**Typ:** Ny funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitet säkerhet & skydd

De insikter som du får för en identifierad risk identifiering är knutna till din Azure AD-prenumeration. Med Azure AD Premium P2-versionen får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte täcks av din licens som inloggning med risk identifiering med ytterligare risk upptäckt.

Mer information finns i [Azure Active Directory risk identifieringar](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomst paneler

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** SSO

Nu kan du bättre hantera hur Office 365-program visas på användarens åtkomst paneler via en ny användar inställning. Det här alternativet är användbart om du vill minska antalet appar i en användares åtkomst paneler om du bara vill visa Office-appar i Office-portalen. Inställningen finns i **användar inställningarna** och är märkt, **användare kan bara se Office 365-appar i Office 365-portalen**.

Mer information finns i [Dölj ett program från användarens upplevelse i Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning i appar aktiverade för inloggning med lösen ord direkt från appens URL

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** SSO

Webb läsar tillägget Mina appar är nu tillgängligt via ett användbart verktyg som ger dig funktionen Mina appar enkel inloggning som en genväg i din webbläsare. När användaren har installerat visas en rutmärket-ikon i webbläsaren som ger dem snabb åtkomst till appar. Användarna kan nu dra nytta av:

- Möjligheten att direkt logga in till SSO-baserade appar från appens inloggnings sida
- Starta en app med hjälp av funktionen snabb sökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.

Mer information finns i avsnittet [Mina appar säker inloggnings tillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrations upplevelsen för Azure AD i Klassisk Azure-portal har dragits tillbaka

**Typ:** Föråldrad **tjänst kategori:** Azure AD **produkt kapacitet:** katalog

Från och med den 8 januari 2018 har Azure AD-administrationen i den klassiska Azure-portalen dragits tillbaka. Detta ägde rum i samband med att den klassiska Azure-portalen upphör att gälla. I framtiden bör du använda [Azure AD administrations Center](https://aad.portal.azure.com) för all Portal-baserad administration av Azure AD.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webb portalen för PhoneFactor har dragits tillbaka

**Typ:** Föråldrad **tjänst kategori:** Azure AD **produkt kapacitet:** katalog

Från och med den 8 januari 2018 har webb portalen för PhoneFactor dragits tillbaka. Den här portalen användes för administration av MFA Server, men dessa funktioner har flyttats till Azure Portal på portal.azure.com.

MFA-konfigurationen finns på: **Azure Active Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Föråldrade Azure AD-rapporter

**Typ:** Föråldrad **tjänste kategori:** rapportering av **produkt kapacitet:** hantering av identitets livs cykel


Med den allmänna tillgängligheten för den nya Azure Active Directory-administrationskonsolen och nya API: er som nu är tillgängliga för både aktivitets-och säkerhets rapporter, har rapport-API: erna under "/Reports"-slut punkten dragits tillbaka från och med den 31 december 2017.

**Vad är tillgängligt?**

Som en del av över gången till den nya administratörs konsolen har vi gjort 2 nya API: er tillgängliga för att hämta Azure AD-aktivitets loggar. De nya API: erna ger omfattande funktioner för filtrering och sortering utöver att tillhandahålla omfattande gransknings-och inloggnings aktiviteter. Data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skyddets identifierings-API i Microsoft Graph.

Mer information finns i:

- [Kom igång med API: et för Azure Active Directory rapportering](../reports-monitoring/concept-reporting-api.md)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Användningsvillkor på åtkomst panelen

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

Nu kan du gå till åtkomst panelen och Visa användnings villkoren som du har accepterat tidigare.

Följ de här stegen:

1. Gå till portalen för mina [appar](https://myapps.microsoft.com)och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** i listan.

3. Välj **Granska användnings villkor** i din **profil**.

4. Nu kan du granska användnings villkoren som du har accepterat.

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Ny inloggnings upplevelse för Azure AD

**Typ:** Ny funktions **tjänst kategori:** Azure AD- **produkt kapacitet:** användarautentisering

Azure AD-och Microsoft-konto Identity system-UIs har gjorts om så att de har ett enhetligt utseende och känsla. Dessutom samlar inloggnings sidan för Azure AD in användar namnet först, följt av autentiseringsuppgifterna på en andra skärm.

Mer information finns i [den nya inloggnings upplevelsen för Azure AD finns nu i offentlig för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggnings meddelanden: en ny "Håll mig inloggad"-upplevelse för Azure AD-inloggning

**Typ:** Ny funktions **tjänst kategori:** Azure AD- **produkt kapacitet:** användarautentisering

Kryss rutan **Håll mig inloggad** på inloggnings sidan för Azure AD ersattes med en ny fråga som visas när du har autentiserat dig.

Om du svarar **Ja** på den här varningen ger tjänsten en beständig uppdateringstoken. Detta är samma sak som när du markerade kryss rutan **Håll mig inloggad** i den gamla upplevelsen. För federerade klienter visas den här prompten när du har autentiserat dig med den federerade tjänsten.

Mer information finns [i färre inloggnings meddelanden: den nya funktionen "Håll mig inloggad" för Azure AD är i för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration så att användnings villkoren måste expanderas innan de accepteras

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

Ett alternativ för administratörer kräver att användarna expanderar användnings villkoren innan de accepterar villkoren.

Välj antingen **på** eller **av** för att kräva att användarna expanderar användnings villkoren. Inställningen **on** kräver att användare visar användnings villkoren innan de accepterar dem.

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Omfattnings aktivering för kvalificerade roll tilldelningar

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Du kan använda omfattnings aktivering för att aktivera kvalificerade Azure-resurs roll tilldelningar med mindre självständighet än standardinställningarna för den ursprungliga tilldelningen. Ett exempel är om du är tilldelad som ägare till en prenumeration i din klient organisation. Med begränsad aktivering kan du aktivera ägar rollen för upp till fem resurser som ingår i prenumerationen (till exempel resurs grupper och virtuella datorer). Omfånget för aktiveringen kan minska möjligheten att köra oönskade ändringar av kritiska Azure-resurser.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i Azure AD App-galleriet

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för företags appar: integration från tredje part

I december 2017 har vi lagt till dessa nya appar med stöd för federation i vårt app-Galleri:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI digital butik](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [image Worker](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO för Bamboo med resolution GmbH](../saas-apps/bamboo-tutorial.md), [SAML SSO för BitBucket med resolution GmbH](../saas-apps/bitbucket-tutorial.md), [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD-integrering.

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännande arbets flöden för Azure AD-katalog roller

**Typ:** Ändrad funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Arbets flödet för godkännande för Azure AD Directory-roller är allmänt tillgängligt.

Med ett arbets flöde för godkännande kan privilegierade roll administratörer kräva att berättigade Rolls medlemmar begär roll aktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan delegera godkännande ansvar. Berättigade roll medlemmar får aviseringar när godkännande är klart och deras roll är aktiv.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Skype for Business-Support

**Typ:** Ändrad funktions **tjänst kategori:** autentiseringar (inloggningar) **produkt kapacitet:** användarautentisering

Direkt autentisering stöder nu användar inloggningar till Skype för företag-klientprogram som stöder modern autentisering, som innehåller online-och hybrid topologier.

Mer information finns i [Skype for Business-topologier som stöds med modern autentisering](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar till Azure AD Privileged Identity Management för Azure RBAC (för hands version)

**Typ:** Ändrad funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Med den offentliga förhands gransknings uppdateringen av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du nu:

* Använd bara tillräckligt med administration.
* Kräv godkännande för att aktivera resurs roller.
* Schemalägg en framtida aktivering av en roll som kräver godkännande för både Azure AD-och Azure-roller.

Mer information finns i [Privileged Identity Management för Azure-resurser (för hands version)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>November 2017

### <a name="access-control-service-retirement"></a>Access Control tjänstens upphör Ande

**Typ:** Plan för ändrings **tjänst kategori:** Access Control tjänst **produkt kapacitet:** Access Control tjänsten

Azure Active Directory Access Control (även kallat Access Control tjänsten) kommer att dras tillbaka om 2018. Mer information som innehåller ett detaljerat rikt linjer för migrering på hög nivå kommer att tillhandahållas under de kommande veckorna. Du kan lämna kommentarer på den här sidan med frågor om tjänsten Access Control och en grupp medlem besvarar dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webb läsar åtkomst till Intune Managed Browser

**Typ:** Plan för ändrings **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Du kan begränsa webbläsarens åtkomst till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som en godkänd app.

Du kan nu konfigurera följande villkor för program-baserad villkorlig åtkomst:

**Klient program:** Webbläsare

**Vad händer med ändringen?**

I dag blockeras åtkomsten när du använder det här villkoret. När för hands versionen är tillgänglig kräver all åtkomst att programmet Managed Browser används.

Titta efter den här funktionen och mer information i kommande Bloggar och viktig information.

Mer information finns i [villkorlig åtkomst i Azure AD](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Plan för ändrings **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns i:

- [Godkänt klient program krav](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användnings villkor för flera språk

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

Administratörer kan nu skapa nya användnings villkor som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med ett motsvarande språk. Användarna visar PDF-filen med det matchande språket baserat på deras inställningar. Om det inte finns någon matchning visas standard språket.

---

### <a name="real-time-password-writeback-client-status"></a>Klient status för tillbakaskrivning av lösen ord i real tid

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för självbetjänings återställning av lösen ord: användarautentisering

Nu kan du granska status för din lokala tillbakaskrivning av lösen ord. Det här alternativet är tillgängligt i avsnittet **lokal integration** på sidan [lösen ords återställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) .

Om det finns problem med anslutningen till din lokala tillbakaskrivning-klient visas ett fel meddelande som ger dig följande:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivning-klient.
- En länk till dokumentationen som hjälper dig att lösa problemet.

Mer information finns i [lokal integrering](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD App-baserad villkorlig åtkomst

**Typ:** Ny funktions **tjänst kategori:** Azure AD **produkt kapacitet:** identitets säkerhet och skydd

Nu kan du begränsa åtkomsten till Office 365 och andra Azure AD-anslutna molnappar till [godkända klient program](../conditional-access/concept-conditional-access-conditions.md#client-apps) som stöder Intune App Protection-principer med hjälp av [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md). Intune App Protection-principer används för att konfigurera och skydda företags data i de här klient programmen.

Genom att kombinera [app-baserade](../conditional-access/app-based-conditional-access.md) med [enhets](../conditional-access/require-managed-devices.md) principer för villkorlig åtkomst har du flexibiliteten att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Plattforms villkor som stöds**

- iOS
- Android

**Villkor för klient program**

- Mobilappar och skriv bords klienter

**Åtkomstkontroll**

- Kräv godkänd klientapp

Mer information finns i [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure Portal

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för enhets registrering och hantering: identitets säkerhet och skydd

Nu kan du hitta alla enheter som är anslutna till Azure AD och de enhets relaterade aktiviteterna på ett och samma ställe. Det finns en ny administrations upplevelse för att hantera alla enhets identiteter och inställningar i Azure Portal. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, som innehåller dina hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för Azure AD-anslutna enheter, hantera enheten med Intune med mera.
- Hantera inställningar för Azure AD-enhet.

Mer information finns i [Hantera enheter med hjälp av Azure Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som enhets plattform för villkorlig åtkomst i Azure AD

**Typ:** Ny funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Du kan nu inkludera (eller undanta) macOS som enhets plattforms villkor i din Azure AD-princip för villkorlig åtkomst. Med att lägga till macOS i de plattformar som stöds, kan du:

- **Registrera och hantera macOS-enheter med hjälp av Intune.** På samma sätt som för andra plattformar, t. ex. iOS och Android, är ett företags Portal program tillgängligt för macOS för att göra enhetliga registreringar. Du kan använda den nya företagsportalsappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter följer organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure Portal kan du nu konfigurera efterlevnadsprinciper för macOS-enheter.
- **Begränsa åtkomsten till program i Azure AD till endast kompatibla macOS-enheter.** Redigering av princip för villkorlig åtkomst har macOS som en separat enhets plattforms alternativ. Nu kan du skapa macOS-specifika principer för villkorlig åtkomst för mål programmet som angetts i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Villkorlig åtkomst för Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Nätverks princip Server tillägg för Azure AD Multi-Factor Authentication

**Typ:** Ny funktions **tjänst kategori:**  Multi-Factor Authentication **produkt kapacitet:** användarautentisering

Nätverks princip Server tillägget för Azure AD Multi-Factor Authentication lägger till molnbaserade Multi-Factor Authentication funktioner till din infrastruktur för autentisering med hjälp av dina befintliga servrar. Med nätverks princip Server tillägget kan du lägga till telefonsamtal, textmeddelande eller telefon programs verifiering till ditt befintliga autentiseringspaket. Du behöver inte installera, konfigurera och underhålla nya servrar.

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverks anslutningar utan att distribuera Azure-Multi-Factor Authentication-server. Nätverks princip Server tillägget fungerar som ett kort mellan RADIUS och molnbaserade Azure AD-Multi-Factor Authentication för att tillhandahålla en andra faktor för autentisering för federerade eller synkroniserade användare.

Mer information finns i [integrera din befintliga infrastruktur för nätverks Policy Server med Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare permanent

**Typ:** Ny funktions **tjänst kategori:** användar hantering **produkt kapacitet:** katalog

I Azure AD Admin Center kan du nu:

- Återställa en borttagen användare.
- Ta bort en användare permanent.

**Så här provar du:**

1. I Azure AD administrations Center väljer du [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i avsnittet **Hantera** .

2. I listan **Visa** väljer du **nyligen borttagna användare**.

3. Välj en eller flera nyligen borttagna användare och Återställ dem sedan eller ta bort dem permanent.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Ändrad funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Följande appar har lagts till i listan över [godkända klient program](../conditional-access/concept-conditional-access-conditions.md#client-apps):

- Microsoft Planner
- Azure Information Protection

Mer information finns i:

- [Godkänt klient program krav](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD App-baserad villkorlig åtkomst](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd "eller" mellan kontroller i en princip för villkorlig åtkomst

**Typ:** Ändrad funktions **tjänst kategori:** **produkt kapacitet** för villkorlig åtkomst: identitets säkerhet och skydd

Du kan nu använda "eller" (Kräv en av de valda kontrollerna) för kontroller för villkorlig åtkomst. Du kan använda den här funktionen för att skapa principer med "eller" mellan åtkomst kontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare loggar in med hjälp av Multi-Factor Authentication "eller" på en kompatibel enhet.

Mer information finns i [kontroller i villkorlig åtkomst för Azure AD](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agg regering av risk identifieringar i real tid

**Typ:** Ändrade funktions **tjänst kategori:** identitets skydd **produkt kapacitet:** identitets säkerhet och skydd

I Azure AD Identity Protection sammanställs nu alla risk identifieringar i real tid som kommer från samma IP-adress på en bestämd dag för varje typ av risk identifiering. Den här ändringen begränsar mängden risk identifieringar som visas utan några ändringar i användar säkerheten.

Den underliggande real tids identifieringen fungerar varje gången användaren loggar in. Om du har en säkerhets princip för inloggnings risker som är inställd på att Multi-Factor Authentication eller blockera åtkomst, utlöses den fortfarande under varje riskfylld inloggning.

---

## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Föråldrade Azure AD-rapporter

**Typ:** Plan för ändrings **tjänst kategori:** rapportering av **produkt kapacitet:** hantering av identitets livs cykel

Azure Portal ger dig följande:

- En ny administrations konsol för Azure AD.
- Nya API: er för aktivitets-och säkerhets rapporter.

På grund av de här nya funktionerna drogs rapport-API: erna under den/Reports-slutpunkten ut den 10 december 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk identifiering av inloggnings fält

**Typ:** Fast **tjänste kategori:** Mina appar **produkt kapacitet:** enkel inloggning

Azure AD stöder automatisk identifiering av inloggnings fält för program som återger ett fält med HTML-användarnamn och lösen ord. De här stegen beskrivs i [hur du automatiskt samlar in inloggnings fält för ett program](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). Du kan hitta den här funktionen genom att lägga till ett program som *inte är en Galleri* på sidan **företags program** i [Azure Portal](https://aad.portal.azure.com). Dessutom kan du konfigurera läget för **enkel inloggning** på det nya programmet till **lösenordsbaserad enkel inloggning**, ange en webb-URL och sedan spara sidan.

Den här funktionen har tillfälligt inaktiverats på grund av ett tjänst problem. Problemet har lösts och den automatiska identifieringen av inloggnings fält är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya Multi-Factor Authentication funktioner

**Typ:** Ny funktions **tjänst kategori:** Multi-Factor Authentication **produkt kapacitet:** identitets säkerhet och skydd

Multi-Factor Authentication (MFA) är en viktig del av att skydda din organisation. Följande funktioner har lagts till för att göra autentiseringsuppgifterna mer anpassningsbara och upplevelsen smidigare:

- Multi-Factor Challenge-resultaten integreras direkt i inloggnings rapporten för Azure AD, vilket omfattar programmerings åtkomst till MFA-resultat.
- MFA-konfigurationen är mer integrerad i konfigurations upplevelsen för Azure AD i Azure Portal.

Med den här offentliga för hands versionen är MFA Management och repor ting en integrerad del av konfigurations upplevelsen för kärnan i Azure AD. Nu kan du hantera funktionen MFA Management Portal i Azure AD-upplevelsen.

Mer information finns i [referens för MFA-rapportering i Azure Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Villkor för användning

**Typ:** Ny funktions **tjänst kategori:** användningsvillkor **produkt kapacitet:** efterlevnad

Du kan använda användnings villkoren för Azure AD för att presentera information, till exempel relevanta frifordringar för användarnas juridiska krav eller efterlevnad.

Du kan använda användnings villkoren för Azure AD i följande scenarier:

- Allmänna användnings villkor för alla användare i din organisation
- Vissa användnings villkor baserat på en användares attribut (till exempel läkare eller anställda eller inrikes och internationella anställda) som gjorts av dynamiska grupper.
- Specifika användnings villkor för att få åtkomst till affärs program med hög effekt, till exempel Salesforce

Mer information finns i [användnings villkoren för Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar för Privileged Identity Management

**Typ:** Ny funktions **tjänst kategori:** Privileged Identity Management **produkt kapacitet:** Privileged Identity Management

Med Azure AD Privileged Identity Management kan du hantera, kontrol lera och övervaka åtkomsten till Azure-resurser (för hands version) i din organisation för att:

- Prenumerationer
- Resursgrupper
- Virtuella datorer

Alla resurser inom Azure Portal som använder Azure RBAC-funktionen kan dra nytta av alla funktioner för säkerhet och livs cykel hantering som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny funktions **tjänst kategori:** åtkomst granskningar **produkt kapacitet:** efterlevnad

Organisationer kan använda åtkomst granskningar (för hands version) för att effektivt hantera grupp medlemskap och åtkomst till företags program:

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om du vill tillåta att gäster fortsätter att ha åtkomst baserat på de insikter som tillhandahålls av åtkomst granskningarna.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst granskningar för Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina appar och Office 365 App Launcher

**Typ:** Ny funktions **tjänst kategori:** Mina appar **produkt kapacitet:** enkel inloggning

Nu kan du bättre hantera appar som visas på användarnas portaler via en ny **Dölj app** -egenskap. Du kan dölja appar för att få hjälp i fall där app-paneler visas för backend-tjänster eller dubbletter av paneler och färdiga användares program lanseringar. Växlingen är i avsnittet **Egenskaper** i appen från tredje part och är märkt **synlig för användaren?** Du kan också dölja en app via programmering via PowerShell.

Mer information finns i [Dölj ett program från tredje part från en användares upplevelse i Azure AD](../manage-apps/hide-application-from-user-portal.md).


**Vad är tillgängligt?**

 Som en del av över gången till den nya administratörs konsolen är två nya API: er för att hämta Azure AD-aktivitets loggar tillgängliga. De nya API: erna ger omfattande funktioner för filtrering och sortering utöver att tillhandahålla omfattande gransknings-och inloggnings aktiviteter. De data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skydds identifieraren API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabb korrigering för Identity Manager

**Typ:** Ändrad funktions **tjänst kategori:** Identity Manager **produkt kapacitet:** hantering av identitets livs cykel

Ett samlings paket för snabb korrigeringar (build 4.4.1642.0) är tillgängligt från och med 25 september 2017 för Identity Manager 2016 Service Pack 1. Detta sammanslagnings paket:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla uppdateringar för Identity Manager 2016 Service Pack 1 upp till build 4.4.1459.0 för Identity Manager 2016.
- Kräver att du har Identity Manager 2016 build-4.4.1302.0.

Mer information finns i [samlat snabb korrigerings paket (build 4.4.1642.0) tillgängligt för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---