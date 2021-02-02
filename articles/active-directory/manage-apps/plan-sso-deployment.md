---
title: Planera en Azure Active Directory distribution av enkel inloggning
description: Guide som hjälper dig att planera, distribuera och hantera SSO i din organisation.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 89f234ece2f7437e4a9d4092334e3dd3690e288d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258107"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planera en distribution för enkel inloggning

Enkel inloggning (SSO) innebär åtkomst till alla program och resurser som användaren behöver genom att bara logga in en gång med ett enda användar konto. Med enkel inloggning kan användare komma åt alla nödvändiga program utan att behöva autentisera en andra gång.

## <a name="benefits-of-sso"></a>Fördelar med SSO

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in på program i Azure Active Directory (Azure AD). 

Många organisationer förlitar sig på SaaS-program (program vara som en tjänst), till exempel Microsoft 365, Box och Salesforce, för slutanvändarens produktivitet. Tidigare var IT-personal tvungen att individuellt skapa och uppdatera användar konton i varje SaaS-program och användare som behövde komma ihåg ett lösen ord för varje.

Azure Marketplace har över 3000 program med förintegrerade SSO-anslutningar, vilket gör det enkelt att integrera dem i din klient.

## <a name="licensing"></a>Licensiering

- **Azure AD-licensiering** – SSO för förintegrerade SaaS-program är kostnads fri. Antalet objekt i din katalog och de funktioner som du vill distribuera kan dock kräva ytterligare licenser. En fullständig lista över licens krav finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).
- **Program licens** – du behöver lämpliga licenser för dina SaaS-program för att uppfylla dina affärs behov. Arbeta med program ägaren för att avgöra om de användare som har tilldelats programmet har rätt licenser för deras roller i programmet. Om Azure AD hanterar automatisk etablering baserat på roller måste rollerna som tilldelats i Azure AD justeras med antalet licenser som ägs i programmet. Felaktigt antal licenser som ägs i programmet kan leda till fel vid etablering/uppdatering av en användare.

## <a name="plan-your-sso-team"></a>Planera ditt SSO-team

- **Engagera rätt intressenter** – när teknik projekt kraschar, är det vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att intressenterna förstår sina roller.
- **Planera kommunikation** – kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt till dina användare om hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om det uppstår problem. Granska alternativen för [hur slutanvändare kommer åt sina SSO-aktiverade program](end-user-experiences.md)och hantverk din kommunikation så att den matchar ditt val. 

## <a name="plan-your-sso-protocol"></a>Planera SSO-protokollet

En SSO-implementering baserad på Federations protokoll förbättrar säkerheten, tillförlitligheten och slutanvändarna och är enklare att implementera. Många program är förintegrerade i Azure AD med [stegvisa guider som är tillgängliga](../saas-apps/tutorial-list.md). Du kan hitta dem på vår [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Detaljerad information om varje SSO-metod hittar du i artikeln [enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

Det finns två huvudsakliga sätt som du kan använda för att göra det möjligt för användarna att logga in på dina appar:

- **Med federerad enkel inloggning** Azure AD autentiserar användaren för programmet med hjälp av deras Azure AD-konto. Den här metoden stöds för program som stöder protokoll som SAML 2,0, WS-Federation eller OpenID Connect och är det mest avancerade läget för enkel inloggning. Vi rekommenderar att du använder federerad enkel inloggning med Azure AD när ett program stöder det, i stället för lösenordsbaserad SSO och ADFS.

- **Med lösenordsbaserade enkel inloggnings** användare loggar du in på programmet med ett användar namn och lösen ord första gången de kommer åt dem. Efter den första inloggningen skickar Azure AD användar namn och lösen ord till programmet. Lösenordsbaserad enkel inloggning möjliggör säker lagring av lösen ord för program och uppspelning med hjälp av ett webb läsar tillägg eller en mobilapp. Det här alternativet utnyttjar den befintliga inloggnings processen som tillhandahålls av programmet, gör det möjligt för en administratör att hantera lösen orden och kräver inte att användaren känner till lösen ordet.

### <a name="considerations-for-federation-based-sso"></a>Överväganden för Federation-baserad SSO

- Använd **OpenID Connect och OAuth** – om det program som du ansluter till stöder det använder du metoden OIDC/OAuth 2,0 för att aktivera SSO till det programmet. Den här metoden kräver mindre konfiguration och ger en bättre användar upplevelse. Mer information finns i [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1,0](../develop/v2-protocols-oidc.md)och [Azure Active Directory Developer ' s guide](../develop/index.yml).
- **Slut punkts konfiguration för SAML-baserad SSO** – om du använder SAML behöver utvecklarna specifik information innan du konfigurerar programmet. Mer information finns i [Konfigurera SAML-baserad enkel inloggning](configure-saml-single-sign-on.md).
- **Certifikat hantering för SAML-baserad SSO** – när du aktiverar federerad enkel inloggning för ditt program skapar Azure AD ett certifikat som är som standard giltigt i tre år. Du kan anpassa förfallo datumet för certifikatet vid behov. Se till att du har processer på plats för att förnya certifikat innan de upphör att gälla. Mer information finns i [Azure AD-hantering av certifikat](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>Att tänka på vid lösenordsbaserad SSO

Att använda Azure AD för lösenordsbaserad SSO kräver att du distribuerar ett webb läsar tillägg som på ett säkert sätt hämtar autentiseringsuppgifterna och fyller i inloggnings formulären. Definiera en mekanism för att distribuera tillägget i skala med [webbläsare som stöds](../user-help/my-apps-portal-end-user-access.md). Alternativen är:

- [grupprincip för Internet Explorer](my-apps-deployment-plan.md)
- [Configuration Manager för Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Användar driven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](../user-help/my-apps-portal-end-user-access.md)

Mer information finns i [så här konfigurerar du lösen ord enkel inloggning](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Hämta metadata för inloggnings formulär för program som inte finns i galleriet

Microsoft har stöd för att hämta metadata i ett webb program för lösen ords valv (som fångar fälten username och Password). Navigera till inloggnings webb adressen under konfigureringen av programmet för att avbilda formulärets metadata. Be program ägaren om den exakta inloggnings-URL: en. Den här informationen används vid inloggnings processen och mappar autentiseringsuppgifter för Azure AD till programmet under inloggningen.

Mer information finns i [Vad är program åtkomst och enkel inloggning med Azure AD? – LÖSENORDSBASERAD SSO](./what-is-single-sign-on.md).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indikationer på att metadata i formulär måste refångas

När program ändrar sin HTML-layout kan du behöva fånga in metadata för att justera ändringarna. Vanliga symtom som anger att HTML-layouten har ändrats är:

- Användare som rapporterar att klicka på programmet får "fastnat" på inloggnings Sidan
- Användare som rapporterar att användar namnet eller lösen ordet inte har fyllts i

#### <a name="shared-accounts"></a>Delade konton

I inloggnings perspektiv skiljer sig program med delade konton inte från ett galleri program som använder SSO SSO för enskilda användare. Det finns dock vissa ytterligare steg som krävs när du planerar och konfigurerar ett program som är avsett att använda delade konton:

1. Arbeta med program företags användare för att dokumentera följande:
   1. Uppsättning användare i organisationen som ska använda programmet
   1. Befintlig uppsättning autentiseringsuppgifter i programmet som är associerat med användarens uppsättning 
1. Skapa en säkerhets grupp i molnet eller lokalt utifrån dina krav för varje kombination av användar uppsättning och autentiseringsuppgifter.
1. Återställ delade autentiseringsuppgifter. När appen har distribuerats i Azure AD behöver inte användarna lösen ordet till det delade kontot. Eftersom Azure AD kommer att lagra lösen ordet bör du överväga att ange att det ska vara mycket långt och komplicerat. 
1. Konfigurera automatisk förnyelse av lösen ordet om programmet stöder det. Det innebär att inte ens administratören som gjorde den första installationen känner till lösen ordet för det delade kontot. 

## <a name="plan-your-authentication-method"></a>Planera din autentiseringsmetod

Att välja rätt autentiseringsmetod är ett viktigt första beslut när du konfigurerar en Azure AD hybrid Identity-lösning. Implementera autentiseringsmetoden som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod måste du ta hänsyn till tiden, den befintliga infrastrukturen, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för alla organisationer och kan ändras med tiden. Du bör välja den som bäst motsvarar ditt speciella scenario. Mer information finns i [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../hybrid/choose-ad-authn.md).

## <a name="plan-your-security-and-governance"></a>Planera din säkerhet och styrning 

Identitet är den nya primära pivottabellen för säkerhets åtgärder och investeringar eftersom nätverks perimeter har blivit allt poröst och mindre effektivt med explosionen av BYOD-enheter och moln program. 

### <a name="plan-access-reviews"></a>Planera åtkomst granskningar

Med [åtkomst granskningar](../governance/create-access-review.md) kan organisationer effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Du bör regelbundet planera för att granska användar åtkomsten och se till att endast rätt personer har fortsatt åtkomst.

Några av de viktiga ämnen som du bör planera för när du konfigurerar åtkomst granskningar är:

1. Identifiera en takt för åtkomst granskningar som passar dina affärs behov. Detta kan vara så frekvent som en gång i veckan, varje månad, varje år eller som en övning på begäran.

1. Skapa grupper som representerar granskarna i appens åtkomst rapporter. Du måste se till att de intressenter som är bekanta med appen och dess mål användare och användnings fall är deltagare i dina åtkomst granskningar

1. Genom att slutföra en åtkomst granskning kan du ta bort åtkomst behörighet för appar till användare som inte längre behöver åtkomst. Planera för att hantera potentiella support förfrågningar från nekade användare. En borttagen användare fortsätter att tas bort i Azure AD i 30 dagar under den tid då de kan återställas av en administratör vid behov. Efter 30 dagar tas användaren bort permanent. Med hjälp av Azure Active Directory Portal kan en global administratör uttryckligen ta bort en nyligen borttagen användare innan tids perioden uppnås.

### <a name="plan-auditing"></a>Planera granskning

Azure AD tillhandahåller [rapporter som innehåller tekniska och affärs insikter](../reports-monitoring/overview-reports.md). 

Både säkerhets-och aktivitets rapporter är tillgängliga. Säkerhets rapporter visar användare som har flaggats för risk och riskfyllda inloggningar. Aktivitets rapporter hjälper dig att förstå beteendet för användare i din organisation genom detaljerad inloggnings aktivitet och att tillhandahålla gransknings historik för alla inloggningar. Du kan använda rapporter för att hantera risker, öka produktiviteten och övervaka efterlevnad.

| Rapporttyp | Åtkomst granskning | Säkerhetsrapporter | Inloggnings rapport |
|-------------|---------------|------------------|----------------|
| Använd för att granska | Program behörigheter och användning. | Potentiellt komprometterade konton | Vem som har åtkomst till programmen |
| Möjliga åtgärder | Granska åtkomst; återkalla behörigheter | Återkalla åtkomst; Framtvinga säkerhets återställning | Återkalla åtkomst |

Azure AD behåller de flesta gransknings data i 30 dagar och gör data tillgängliga via Azure admin-portalen eller via ett API som du kan ladda ned till dina analys system.

### <a name="consider-using-microsoft-cloud-application-security"></a>Överväg att använda Microsoft Cloud program säkerhet

Microsoft Cloud App Security (MCAS) är en CASB-lösning (Cloud Access Security Broker). Det ger dig insyn i dina molnappar och tjänster, ger avancerad analys för att identifiera och bekämpa cyberhot, vilket gör att du kan styra hur dina data överförs.

Genom att distribuera MCAS kan du:

- Använd Cloud Discovery för att mappa och identifiera din moln miljö och de molnappar som din organisation använder.
- Sanktionera och ta bort sanktionerande appar i molnet
- Använd lättanvända app-kopplingar som utnyttjar Provider-API: er, för synlighet och styrning av appar som du ansluter till
- Använd Appkontroll för villkorsstyrd åtkomst skydd för att få insyn i real tid och kontroll över åtkomst och aktiviteter i era molnappar
- Hjälper dig att få kontinuerlig kontroll genom att ställa in och sedan kontinuerligt finjustera principer.

MCAS-kontroll för program säkerhet () är tillgänglig för alla webbläsare på alla större plattformar på alla operativ system. Microsoft Cloud Mobilappar och skrivbordsappar kan också blockeras eller tillåtas. Genom inbyggd integrering med Azure AD kan alla appar som är konfigurerade med SAML eller öppna ID Connect-appar med enkel inloggning i Azure AD stödjas, inklusive [flera aktuella appar](/cloud-app-security/proxy-intro-aad).

Mer information om MCAS finns i [Översikt över Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security). MCAS är en användarbaserad prenumerations tjänst. Du kan granska licens informationen i [MCAS License-databladet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Använd villkorlig åtkomst

Med villkorlig åtkomst kan du automatisera kriterier baserade åtkomst kontroll beslut för dina molnappar.

Principer för villkorlig åtkomst tillämpas när den första faktorn har slutförts. Därför är villkorlig åtkomst inte avsedd som en första rad för scenarier som denial-of-service (DoS)-attacker, men kan använda signaler från dessa händelser för att fastställa åtkomst. Till exempel kan inloggnings risk nivån, platsen för begäran och så vidare användas. Mer information om villkorlig åtkomst finns i [översikten](../conditional-access/plan-conditional-access.md) och [distributions planen](../conditional-access/plan-conditional-access.md).

## <a name="azure-sso-technical-requirements"></a>Tekniska krav för Azure SSO

I följande avsnitt beskrivs kraven för att konfigurera ditt specifika program, inklusive nödvändig (a) miljö (er), slut punkter, anspråks mappning, obligatoriska attribut, certifikat och protokoll som används. Du behöver den här informationen för att konfigurera SSO i [Azure AD-portalen](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Information om autentiseringsmekanism

För alla förintegrerade SaaS-appar ger Microsoft en själv studie kurs och du behöver inte den här informationen. Om programmet inte finns i vår Marketplace/Galleri för program, kan du behöva samla in följande typer av data:

- **Aktuell identitetsprovider som programmet använder för SSO om det är tillämpligt** – till exempel: AD FS, PingFederate, okta
- **Protokoll som stöds av mål programmet** , till exempel SAML 2,0, OpenID Connect, OAuth, Forms-Based auth, WS-utfodras, WS-Trust
- **Protokoll som konfigureras med Azure AD** – till exempel SAML 2,0 eller 1,1, OpenID Connect, OAuth, Forms-Based WS-Fed

### <a name="attribute-requirements"></a>Krav för attribut

Det finns en förkonfigurerad uppsättning attribut och attribut mappningar mellan Azure AD-användarkonton och varje SaaS-Apps användar objekt. Vissa appar hanterar andra typer av objekt, till exempel grupper. Planera mappningen av användarattribut från Azure AD till ditt program och [Anpassa standardattributen – mappningar](../app-provisioning/customize-application-attributes.md) efter dina affärs behov.

### <a name="certificate-requirements"></a>Certifikatkrav

Certifikatet för programmet måste vara uppdaterat eller så finns det risk för att användare inte kan komma åt programmet. De flesta SaaS program certifikat är lämpliga i 36 månader. Du ändrar certifikatets varaktighet i program bladet. Se till att dokumentera förfallo datum och veta hur du ska hantera certifikat förnyelsen. 

Det finns två sätt att hantera dina certifikat. 

- **Automatisk certifikat förnyelse** – Microsoft stöder [förnyelse av signerings nyckel i Azure AD](../develop/active-directory-signing-key-rollover.md). Detta är vår bästa metod för att hantera certifikat, men alla ISV: s har inte stöd för det här scenariot.

- **Uppdatera manuellt** – varje program har sitt eget certifikat som upphör att gälla baserat på hur det har definierats. Innan programmets certifikat upphör att gälla skapar du ett nytt certifikat och skickar det till ISV. Den här informationen kan hämtas från federationsmetadata. [Läs mer om federationsmetadata här.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Implementera SSO

Använd följande faser för att planera för och distribuera din lösning i din organisation:

### <a name="user-configuration-for-sso"></a>Användar konfiguration för SSO

- **Identifiera dina test användare**

   Kontakta appens ägare och be dem att skapa minst tre test användare i programmet. Se till att den information som du ska använda när den primära identifieraren är korrekt ifylld och matchar ett attribut som är tillgängligt i Azure AD. I de flesta fall kommer detta att mappas till "NameID" för SAML-baserade program. För JWT-token är det "preferred_username".
   
   Skapa användaren i Azure AD antingen manuellt som en molnbaserad användare eller synkronisera användaren från den lokala miljön med hjälp av Azure AD Connect Sync-motorn. Se till att informationen matchar de anspråk som skickas till programmet.

- **Konfigurera enkel inloggning**

   I [listan med program](../saas-apps/tutorial-list.md)letar du upp och öppnar SSO-självstudien för ditt program och följer sedan själv studie kursen om hur du konfigurerar SaaS-programmet.

   Om du inte kan hitta programmet, se [dokumentationen för anpassade program](./configure-saml-single-sign-on.md). På så sätt får du stegvisa anvisningar om hur du lägger till ett program som inte finns i Azure AD-galleriet.

   Du kan också använda anspråk som utfärdats i SAML-token för företags program med hjälp av [Microsofts väglednings dokumentation](../develop/active-directory-claims-mapping.md). Se till att mappningen till det du förväntar dig att ta emot i SAML-svaret för ditt program. Om du stöter på problem under konfigurationen kan du använda vår vägledning om [hur du](./debug-saml-sso-issues.md)felsöker SSO-integrering.

### <a name="provide-sso-change-communications-to-end-users"></a>Tillhandahålla SSO-kommunikation till slutanvändare

Implementera kommunikations planen. Se till att du låter dina slutanvändare veta att en ändring kommer, när den har anlänt, vad du gör nu och hur du söker efter hjälp.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifiera scenarier för slutanvändare för SSO

Du kan använda följande test fall för att utföra tester på företagsägda och personliga enheter för att se till att dina SSO-konfigurationer fungerar som förväntat. Scenarierna nedan förutsätter att en användare navigerar till en program-URL och går igenom ett autentiseringspaket som initieras av tjänst leverantören (SP-initierat auth Flow).

| Scenario | Förväntat resultat på SP-initierat auth-flöde av användare |
|----------|---------------------------------------------------|
| Logga in på programmet med IE på Corpnet. | Integrerad Windows-autentisering (IWA) sker utan ytterligare prompter. |
| Logga in på programmet med Internet Explorer samtidigt som du har ett nytt inloggnings försök. | Forms-baserad prompt på AD FS server. Användaren har loggat in och webbläsarens prompter för MFA. |
| Logga in på programmet med Internet Explorer samtidigt som du har en aktuell session och aldrig utfört MFA. | Användaren får inget meddelande om den första faktorn. Användaren får en uppvarning för MFA. |
| Logga in på programmet med Internet Explorer samtidigt som du har en aktuell session och redan utfört MFA i den här sessionen. | Användaren får inget meddelande om den första faktorn. Användaren får inte MFA. Användarens SSOs i program. |
| Logga in på program med Chrome/Firefox/Safari på företags nätverket med en aktuell session och redan har utfört MFA i den här sessionen. | Användaren får inget meddelande om den första faktorn. Användaren får inte MFA. Användarens SSO-program. |
| Logga in på program med Chrome/Firefox/Safari på företags nätverket med nytt inloggnings försök. | Forms-baserad prompt på AD FS server. Användaren har loggat in och webbläsarens prompter för MFA. |
| Logga in på program med Chrome/Firefox samtidigt som i företags nätverket med en aktuell session. | Användaren får inget meddelande om den första faktorn. Användaren får inte MFA. Användarens SSO-program. |
| Logga in på programmet med appen mobilapp med ett nytt inloggnings försök. | Forms-baserad prompt på AD FS server. Användaren har loggat in och ADAL klient meddelanden för MFA. |
| Obehörig användare försöker logga in på programmet med inloggnings-URL: en. | Forms-baserad prompt på AD FS server. Användaren kan inte logga in med den första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösen ord. | Användaren navigerar till programmets URL och får ett felaktigt användar namn/lösen ord. |
| Behörig användare klickar på länken i ett e-postmeddelande och har redan autentiserats. | Användaren klickar på URL och är inloggad i programmet utan ytterligare prompter. |
| Behörig användare klickar på länken i ett e-postmeddelande och har inte autentiserats än. | Användaren klickar på URL och kan bli ombedd att autentisera med den första faktorn. |
| Auktoriserade användare loggar in i programmet med appens mobilapp (SP-initierad) med ett nytt inloggnings försök. | Forms-baserad prompt på AD FS server. Användaren har loggat in och ADAL klient meddelanden för MFA. |
| Obehörig användare försöker logga in i programmet med inloggnings-URL (SP-initierad). | Forms-baserad prompt på AD FS server. Användaren kan inte logga in med den första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösen ord.| Användaren navigerar till programmets URL och får ett felaktigt användar namn/lösen ord. |
| Auktoriserade användare loggar ut och loggar sedan in igen. | Om inloggnings-URL har kon figurer ATS loggas användaren ut från alla tjänster och du ombeds att autentisera. |
| Auktoriserade användare loggar ut och loggar sedan in igen. | Om URL för utloggning inte har kon figurer ATS loggas användaren automatiskt in igen med hjälp av befintlig token från den befintliga Azure AD-webbläsarsessionen. |
| Behörig användare klickar på länken i ett e-postmeddelande och har redan autentiserats. | Användaren klickar på URL och är inloggad i programmet utan ytterligare prompter. |
| Behörig användare klickar på länken i ett e-postmeddelande och har inte autentiserats än. | Användaren klickar på URL och kan bli ombedd att autentisera med den första faktorn. |

## <a name="manage-sso"></a>Hantera SSO

Det här avsnittet beskriver kraven och rekommendationerna för att kunna hantera SSO.

### <a name="required-administrative-roles"></a>Administrativa roller som krävs

Använd alltid rollen med minst de behörigheter som krävs för att utföra den nödvändiga uppgiften i Azure Active Directory. Microsoft rekommenderar att [du granskar de olika roller som är tillgängliga](../roles/permissions-reference.md) och väljer rätt en för att lösa dina behov för varje person i det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Person| Roller | Azure AD-roll (om det behövs) |
|--------|-------|-----------------------------|
| Support administratör | Nivå 1-stöd | Inget |
| Identitets administratör | Konfigurera och Felsök när problem påverkar Azure AD | Global administratör |
| Program administratör | Användar attestering i program, konfiguration av användare med behörigheter | Inget |
| Infrastruktur administratörer | Certifikat förnyelse ägare | Global administratör |
| Företags ägare/från intressenter | Användar attestering i program, konfiguration av användare med behörigheter | Inget |

Vi rekommenderar att du använder [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) för att hantera dina roller för att ge ytterligare gransknings-, kontroll-och åtkomst granskning för användare med katalog behörigheter.

### <a name="sso-certificate-lifecycle-management"></a>Livs cykel hantering för SSO-certifikat

Det är viktigt att identifiera rätt roller och e-postdistributionslistor som beskrivs i hantera livs cykeln för signerings certifikatet mellan Azure AD och det program som konfigureras med enkel inloggning. Här följer några av de viktiga roller som vi rekommenderar att du har på plats:

- Ägare för att uppdatera användar egenskaper i programmet
- Owner On-Call för program Break/Fix support
- Noga övervakad lista över e-postdistributioner för certifikat-relaterade ändrings meddelanden

Det maximala livs längden för ett certifikat är tre år. Vi rekommenderar att du skapar en process för hur du hanterar en certifikats förändring mellan Azure AD och ditt program. Detta kan hjälpa till att förhindra eller minimera ett avbrott på grund av ett certifikat som upphör att gälla eller tvinga certifikat förnyelse.

### <a name="rollback-process"></a>Återställnings process

När du har slutfört testningen baserat på dina testfall är det dags att gå över till produktion med ditt program. Genom att flytta till produktion kan du implementera dina planerade och testade konfigurationer i din produktions klient och distribuera dem till användarna. Det är dock viktigt att planera vad som ska göras om distributionen inte fungerar som planerat. Om SSO-konfigurationen Miss lyckas under distributionen måste du förstå hur du minimerar eventuella avbrott och minskar påverkan på dina användare.

Tillgängligheten för autentiseringsmetoder i programmet avgör den bästa strategin. Se alltid till att du har detaljerad dokumentation för app-ägare på exakt hur du ska återgå till det ursprungliga inloggnings konfigurations läget, om distributionen körs i problem.

- **Om din app stöder flera identitets leverantörer**, till exempel LDAP och AD FS och ping, ska du inte ta bort den befintliga SSO-konfigurationen under distributionen. Inaktivera det i stället för migrering om du skulle behöva växla tillbaka senare. 

- **Om appen inte har stöd för flera IDP: er** , men tillåter att användare loggar in med formulärbaserad autentisering (användar namn/lösen ord), bör du se till att användarna kan gå tillbaka till den här metoden om den nya konfigurationen för enkel inloggning Miss lyckas.

### <a name="access-management"></a>Åtkomsthantering

Vi rekommenderar att du väljer en skalad metod när du hanterar åtkomst till resurser. Vanliga metoder är bland annat användning av lokala grupper genom synkronisering via Azure AD Connect, att [skapa dynamiska grupper i Azure AD baserat på användarattribut](../enterprise-users/groups-dynamic-membership.md)eller skapa självbetjänings [grupper](../enterprise-users/groups-self-service-management.md) i Azure AD som hanteras av en resurs ägare.

### <a name="monitor-security"></a>Övervaka säkerhet

Vi rekommenderar att du konfigurerar en vanlig takt där du kan granska de olika aspekterna av SaaS App Security och utföra eventuella åtgärder som krävs.

### <a name="troubleshooting"></a>Felsökning

Följande länkar visar fel söknings scenarier. Du kanske vill skapa en speciell guide för support personalen som införlivar de här scenarierna och stegen för att åtgärda dem.

#### <a name="consent-issues"></a>Medgivande problem

- [Oväntat medgivande fel](./application-sign-in-unexpected-user-consent-prompt.md)

- [Fel vid användarmedgivande](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Inloggningsproblem

- [Problem med att logga in från en anpassad Portal](./application-sign-in-other-problem-access-panel.md)

- [Problem vid inloggning från Mina appar](./application-sign-in-other-problem-access-panel.md)

- [Fel på inloggningssidan för programmet](./application-sign-in-problem-application-error.md)

- [Problem med att logga in på ett Microsoft-program](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>SSO-problem för program som listas i Azure Application galleriet

- [Problem med inloggning med lösen ord för program som listas i Azure Application galleriet](./troubleshoot-password-based-sso.md) 

- [Problem med federerade SSO för program som listas i Azure Application galleriet](./application-sign-in-problem-federated-sso-gallery.md)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>SSO-problem för program som inte visas i Azure Application galleriet

- [Problem med inloggning med lösen ord för program som inte visas i Azure Application galleriet](./troubleshoot-password-based-sso.md) 

- [Problem med federerad SSO för program som inte visas i Azure Application galleriet](./application-sign-in-problem-federated-sso-gallery.md)

## <a name="next-steps"></a>Nästa steg

[Felsöka SAML-baserad enkel inloggning](./debug-saml-sso-issues.md)

[Anspråks mappning för appar via PowerShell](../develop/active-directory-claims-mapping.md)

[Anpassa anspråk som utfärdats i SAML-token](../develop/active-directory-saml-claims-customization.md)

[SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md)

[Single Sign-Out SAML-protokoll](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (för externa användare som partner och leverantörer)

[Villkorsstyrd åtkomst i Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Åtkomst med enkel inloggning](./what-is-single-sign-on.md)

[Själv studie kurs om Application SSO](../saas-apps/tutorial-list.md)

[Hämtning av whitepaper – distributions plan för enkel inloggning](https://aka.ms/SSODeploymentPlan)