---
title: Planera en Azure Active Directory distribution med enkel inloggning
description: Guide som hjälper dig att planera, distribuera och hantera enkel inloggning i din organisation.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f395eaf21dce0077c1239ef2251973aeb5faa70
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375618"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planera en distribution för enkel inloggning

Enkel inloggning (SSO) innebär åtkomst till alla program och resurser som en användare behöver genom att bara logga in en gång med ett enda användarkonto. Med enkel inloggning kan användarna komma åt alla nödvändiga program utan att behöva autentisera en andra gång.

## <a name="benefits-of-sso"></a>Fördelar med enkel inloggning

Enkel inloggning (SSO) ökar säkerheten och bekvämligheten när användare loggar in på program i Azure Active Directory (Azure AD). 

Många organisationer förlitar sig på SaaS-program (programvara som en tjänst), till exempel Microsoft 365, Box och Salesforce, för slutanvändares produktivitet. Tidigare behövde IT-personal skapa och uppdatera användarkonton individuellt i varje SaaS-program, och användarna behövde komma ihåg ett lösenord för var och en.

Den Azure Marketplace har över 3 000 program med förintegrerade SSO-anslutningar, vilket gör det enkelt att integrera dem i din klientorganisation.

## <a name="licensing"></a>Licensiering

- **Azure AD-licensiering** – SSO för förintegrerade SaaS-program är kostnadsfri. Antalet objekt i katalogen och de funktioner som du vill distribuera kan dock kräva ytterligare licenser. En fullständig lista över licenskrav finns i [Azure Active Directory Priser.](https://azure.microsoft.com/pricing/details/active-directory/)
- **Programlicensiering** – Du behöver rätt licenser för dina SaaS-program för att uppfylla dina affärsbehov. Ta hjälp av programägaren för att avgöra om de användare som är tilldelade till programmet har rätt licenser för sina roller i programmet. Om Azure AD hanterar den automatiska etableringen baserat på roller måste de roller som tilldelats i Azure AD justeras med antalet licenser som ägs i programmet. Felaktigt antal licenser som ägs i programmet kan leda till fel under etablering/uppdatering av en användare.

## <a name="plan-your-sso-team"></a>Planera ditt SSO-team

- **Engagera rätt intressenter –** När teknikprojekt misslyckas beror det vanligtvis på felaktiga förväntningar på påverkan, resultat och ansvarsområden. För att undvika dessa fallgropar bör du se till att du [engagerar rätt intressenter och](../fundamentals/active-directory-deployment-plans.md) att intressenterna förstår sina roller.
- **Planera kommunikation** – Kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt med användarna om hur deras upplevelse kommer att ändras, när den kommer att ändras och hur de kan få support om de får problem. Granska alternativen för hur [slutanvändarna kommer åt sina SSO-aktiverade](end-user-experiences.md)program och skapa din kommunikation för att matcha ditt val. 

## <a name="plan-your-sso-protocol"></a>Planera ditt protokoll för enkel inloggning

En implementering av enkel inloggning som baseras på federationsprotokoll förbättrar säkerheten, tillförlitligheten och slutanvändarupplevelsen och är enklare att implementera. Många program är förintegrerade i Azure AD [med stegvisa guider tillgängliga.](../saas-apps/tutorial-list.md) Du hittar dem på vår [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Detaljerad information om varje metod för enkel inloggning finns i artikeln [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).

Det finns två huvudsakliga sätt som du kan göra så att dina användare kan logga in på dina appar:

- **Med federerad enkel inloggning** Azure AD autentiserar användaren till programmet med hjälp av deras Azure AD-konto. Den här metoden stöds för program som stöder protokoll som SAML 2.0, WS-Federation eller OpenID Connect och är det bästa läget för enkel inloggning. Vi rekommenderar att du använder federerad enkel inloggning med Azure AD när ett program stöder det, i stället för lösenordsbaserad enkel inloggning och ADFS.

- **Med lösenordsbaserad enkel inloggning loggar användarna** in på programmet med ett användarnamn och lösenord första gången de får åtkomst till det. Efter den första inloggningen tillhandahåller Azure AD användarnamnet och lösenordet till programmet. Lösenordsbaserad enkel inloggning möjliggör säker lagring av programlösenord och återuppspelning med hjälp av ett webbläsartillägg eller en mobilapp. Det här alternativet utnyttjar den befintliga inloggningsprocessen som tillhandahålls av programmet, gör det möjligt för en administratör att hantera lösenorden och kräver inte att användaren känner till lösenordet.

### <a name="considerations-for-federation-based-sso"></a>Överväganden för federationsbaserad enkel inloggning

- **Använda OpenID Connect och OAuth** – Om programmet som du ansluter till stöder det använder du metoden OIDC/OAuth 2.0 för att aktivera enkel inloggning till programmet. Den här metoden kräver mindre konfiguration och ger en mer omfattande användarupplevelse. Mer information finns i [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md) [, OpenID Connect 1.0](../develop/v2-protocols-oidc.md) [och Azure Active Directory utvecklarhandbok](../develop/index.yml).
- **Slutpunktskonfigurationer för SAML-baserad enkel** inloggning – Om du använder SAML behöver utvecklarna specifik information innan de konfigurerar programmet. Mer information finns i [Konfigurera SAML-baserad enkel inloggning.](configure-saml-single-sign-on.md)
- **Certifikathantering för SAML-baserad enkel** inloggning – När du aktiverar federerad enkel inloggning för ditt program skapar Azure AD ett certifikat som är giltigt i tre år som standard. Du kan anpassa förfallodatumet för certifikatet om det behövs. Se till att du har processer för att förnya certifikat innan de upphör att gälla. Mer information finns i Hantera [certifikat i Azure AD.](./manage-certificates-for-federated-single-sign-on.md)

### <a name="considerations-for-password-based-sso"></a>Överväganden för lösenordsbaserad enkel inloggning

Om du vill använda Azure AD för lösenordsbaserad enkel inloggning måste du distribuera ett webbläsartillägg som på ett säkert sätt hämtar autentiseringsuppgifterna och fyller i inloggningsformulären. Definiera en mekanism för att distribuera tillägget i stor skala med webbläsare [som stöds.](../user-help/my-apps-portal-end-user-access.md) Alternativen är:

- [grupprincip för Internet Explorer](my-apps-deployment-plan.md)
- [Konfigurationshanteraren för Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Användardriven nedladdning och konfiguration för Chrome, Firefox, Microsoft Edge eller IE](../user-help/my-apps-portal-end-user-access.md)

Mer information finns i Så [här konfigurerar du enkel inloggning med lösenord på](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Samla in metadata för inloggningsformulär för program som inte finns i galleriet

Microsoft har stöd för att samla in metadata på en webbapp för lösenordsvalv (att samla in fälten för användarnamn och lösenord). Gå till inloggnings-URL:en när du konfigurerar programmet för att samla in formulärmetadata. Be programägaren om den exakta inloggnings-URL:en. Den här informationen används under inloggningsprocessen och mappar Azure AD-autentiseringsuppgifter till programmet under inloggningen.

Mer information finns i [Vad är programåtkomst och enkel inloggning med Azure AD? – lösenordsbaserad enkel inloggning.](./what-is-single-sign-on.md)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Tecken på att metadata i formulär måste kapslas in på ett annat sätt

När program ändrar sin HTML-layout kan du behöva ta tillbaka metadata för att justera ändringarna. Vanliga symptom som indikerar att HTML-layouten har ändrats är:

- Användare som rapporterar att klicka på programmet "fastnar" på inloggningssidan
- Användare som rapporterar att användarnamnet eller lösenordet inte har fyllts i

#### <a name="shared-accounts"></a>Delade konton

Från inloggningsperspektivet skiljer sig inte program med delade konton från ett galleriprogram som använder enkel inloggning med lösenord för enskilda användare. Det finns dock vissa ytterligare steg som krävs när du planerar och konfigurerar ett program som är avsett att använda delade konton:

1. Arbeta med användare i programföretag för att dokumentera följande:
   1. En uppsättning användare i organisationen som ska använda programmet
   1. Befintlig uppsättning autentiseringsuppgifter i programmet som är associerad med uppsättningen användare 
1. Skapa en säkerhetsgrupp i molnet eller lokalt baserat på dina krav för varje kombination av användaruppsättning och autentiseringsuppgifter.
1. Återställ de delade autentiseringsuppgifterna. När appen har distribuerats i Azure AD behöver enskilda användare inte lösenordet för det delade kontot. Eftersom Lösenordet lagras i Azure AD bör du överväga att ange det som mycket långt och komplext. 
1. Konfigurera automatisk förnyelse av lösenordet om programmet stöder det. På så sätt känner inte ens administratören som gjorde den första installationen till lösenordet för det delade kontot. 

## <a name="plan-your-authentication-method"></a>Planera din autentiseringsmetod

Att välja rätt autentiseringsmetod är ett viktigt första beslut när du ska konfigurera en Azure AD-hybrididentitetslösning. Implementera den autentiseringsmetod som konfigureras med hjälp av Azure AD Connect, som även konfigurerar användare i molnet.

Om du vill välja en autentiseringsmetod måste du tänka på tid, befintlig infrastruktur, komplexitet och kostnaden för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan ändras med tiden. Du bör välja den som bäst matchar ditt specifika scenario. Mer information finns i Välja [rätt autentiseringsmetod för din Azure Active Directory hybrididentitetslösning.](../hybrid/choose-ad-authn.md)

## <a name="plan-your-security-and-governance"></a>Planera din säkerhet och styrning 

Identitet är den nya primära pivoten för säkerhets uppmärksamhet och investeringar eftersom nätverksnätverkets perimeter har blivit allt mer sporadiska och mindre effektiva tack vare en explosion av BYOD-enheter och molnprogram. 

### <a name="plan-access-reviews"></a>Planera åtkomstgranskningar

[Med åtkomstgranskningar](../governance/create-access-review.md) kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Du bör planera att regelbundet granska användaråtkomst för att se till att endast rätt personer har fortsatt åtkomst.

Några av de viktigaste ämnena att planera för när du ställer in åtkomstgranskningar är:

1. Identifiera en takt för åtkomstgranskningar som passar dina affärs behov. Detta kan ske så ofta som en gång i veckan, varje månad, varje år eller som en övning på begäran.

1. Skapa grupper som representerar granskarna av appåtkomstrapporterna. Du måste se till att intressenter som är mest bekanta med appen och dess målanvändare och användningsfall är deltagare i dina åtkomstgranskningar

1. I en åtkomstgranskning ingår att ta bort appåtkomstbehörigheter till användare som inte längre behöver åtkomst. Planera för hantering av potentiella supportbegäranden från nekade användare. En borttagna användare förblir borttagna i Azure AD i 30 dagar under vilken de kan återställas av en administratör om det behövs. Efter 30 dagar tas användaren bort permanent. Med hjälp Azure Active Directory-portalen kan en global administratör uttryckligen permanent ta bort en nyligen borttagna användare innan den tidsperioden nås.

### <a name="plan-auditing"></a>Planera granskning

Azure AD innehåller [rapporter som innehåller tekniska och affärsinsikter.](../reports-monitoring/overview-reports.md) 

Både säkerhets- och aktivitetsrapporter är tillgängliga. Säkerhetsrapporter visar användare som har flaggats för risk och riskfyllda inloggningar. Aktivitetsrapporter hjälper dig att förstå beteendet för användare i din organisation genom att beskriva inloggningsaktiviteten och tillhandahålla granskningsloggar för alla inloggningar. Du kan använda rapporter för att hantera risker, öka produktiviteten och övervaka efterlevnad.

| Rapporttyp | Åtkomstgranskning | Säkerhetsrapporter | Inloggningsrapport |
|-------------|---------------|------------------|----------------|
| Använd för att granska | Programbehörigheter och användning. | Potentiellt komprometterade konton | Vem som har åtkomst till programmen |
| Potentiella åtgärder | Granska åtkomst; återkalla behörigheter | Återkalla åtkomst; tvinga säkerhetsåterställning | Återkalla åtkomst |

Azure AD behåller de flesta granskningsdata i 30 dagar och gör data tillgängliga via Azure-administratörsportalen eller via ett API som du kan ladda ned till dina analyssystem.

### <a name="consider-using-microsoft-cloud-application-security"></a>Överväg att använda Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) är Cloud Access en casb-lösning (Security Broker). Det ger dig insyn i dina molnappar och molntjänster, ger avancerade analyser för att identifiera och bekämpa cyberhot och gör att du kan styra hur dina data färdas.

Genom att distribuera MCAS kan du:

- Använd Cloud Discovery för att mappa och identifiera din molnmiljö och de molnappar som din organisation använder.
- Sanktionera och ta bort sanktionering av appar i molnet
- Använd lättanvända appanslutningsprogram som utnyttjar provider-API:er för synlighet och styrning av appar som du ansluter till
- Använd Appkontroll för villkorsstyrd åtkomst för att få insyn i realtid och kontroll över åtkomst och aktiviteter i dina molnappar
- Hjälper dig att ha kontinuerlig kontroll genom att ställa in och sedan kontinuerligt finjustera principer.

Sessionskontroll för Microsoft Cloud Application Security (MCAS) är tillgänglig för alla webbläsare på alla större plattformar i alla operativsystem. Mobilappar och skrivbordsappar kan också blockeras eller tillåtas. Genom intern integrering med Azure AD kan alla appar som har konfigurerats med SAML eller Open ID Connect-appar med enkel inloggning i Azure AD stödjas, inklusive flera [aktuella appar.](/cloud-app-security/proxy-intro-aad)

Information om MCAS finns i Microsoft Cloud App Security [översikt.](/cloud-app-security/what-is-cloud-app-security) MCAS är en användarbaserad prenumerationstjänst. Du kan granska licensinformationen i [databladet för MCAS-licensiering.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Använd villkorlig åtkomst

Med villkorsstyrd åtkomst kan du automatisera villkorsbaserade beslut om åtkomstkontroll för dina molnappar.

Principer för villkorlig åtkomst tillämpas när den första faktorautentisering har slutförts. Villkorsstyrd åtkomst är därför inte avsett som ett förstalinje försvar för scenarier som DoS-attacker (Denial-of-Service), men kan använda signaler från dessa händelser för att fastställa åtkomst. Till exempel risknivån för inloggning, platsen för begäran och så vidare kan användas. Mer information om villkorlig åtkomst finns i [översikten](../conditional-access/plan-conditional-access.md) och [distributionsplanen.](../conditional-access/plan-conditional-access.md)

## <a name="azure-sso-technical-requirements"></a>Tekniska krav för enkel inloggning i Azure

Följande avsnitt beskriver kraven för att konfigurera ditt specifika program, inklusive nödvändiga miljöer, slutpunkter, anspråksmappning, nödvändiga attribut, certifikat och protokoll som används. Du behöver den här informationen för att konfigurera enkel inloggning i [Azure AD-portalen.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Information om autentiseringsmekanismen

För alla förintegrerade SaaS-appar tillhandahåller Microsoft en självstudie och du behöver inte den här informationen. Om programmet inte finns på vår marknadsplats för program/galleriet kan du behöva samla in följande data:

- **Aktuell identitetsprovider som programmet använder för enkel** inloggning om tillämpligt – till exempel: AD FS, PingFederate, Okta
- **Protokoll som stöds av målprogrammet** – till exempel SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protokoll som konfigureras** med Azure AD – till exempel SAML 2.0 eller 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Attributkrav

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och varje SaaS-appanvändarobjekt. Vissa appar hanterar andra typer av objekt, till exempel grupper. Planera mappningen av användarattribut från Azure AD till ditt program och [anpassa standardattributmappningarna](../app-provisioning/customize-application-attributes.md) efter dina affärsbehov.

### <a name="certificate-requirements"></a>Certifikatkrav

Certifikatet för programmet måste vara uppdaterat, eller så finns det en risk för att användarna inte kan komma åt programmet. De flesta SaaS-programcertifikat är bra i 36 månader. Du ändrar certifikatets varaktighet på programbladet. Se till att dokumentera förfallodatumet och veta hur du kommer att hantera din certifikatförnyelse. 

Det finns två sätt att hantera dina certifikat. 

- **Automatisk certifikats förnyelse –** Microsoft stöder förnyelse [av signeringsnyckel i Azure AD.](../develop/active-directory-signing-key-rollover.md) Även om det här är vår föredragna metod för att hantera certifikat så stöder inte alla ISV:er det här scenariot.

- **Uppdatera manuellt** – Varje program har ett eget certifikat som upphör att gälla baserat på hur det definieras. Innan programmets certifikat upphör att gälla skapar du ett nytt certifikat och skickar det till ISV:en. Den här informationen kan hämtas från federationsmetadata. [Läs mer om federationsmetadata här.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Implementera enkel inloggning

Använd följande faser för att planera för och distribuera din lösning i din organisation:

### <a name="user-configuration-for-sso"></a>Användarkonfiguration för enkel inloggning

- **Identifiera dina testanvändare**

   Kontakta appägaren och be dem att skapa minst tre testanvändare i programmet. Se till att informationen som du använder som primär identifierare fylls i korrekt och matchar ett attribut som är tillgängligt i Azure AD. I de flesta fall mappar detta till "NameID" för SAML-baserade program. För JWT-token är det "preferred_username".
   
   Skapa användaren i Azure AD manuellt som en molnbaserad användare eller synkronisera användaren lokalt med hjälp av Azure AD Connect synkroniseringsmotorn. Se till att informationen matchar anspråken som skickas till programmet.

- **Konfigurera enkel inloggning**

   I listan [över program letar](../saas-apps/tutorial-list.md)du upp och öppnar självstudien om enkel inloggning för ditt program och följer sedan självstudiens steg för att konfigurera SaaS-programmet.

   Om du inte hittar ditt program kan du läsa dokumentationen [för anpassat program.](./configure-saml-single-sign-on.md) Här får du lära dig hur du lägger till ett program som inte finns i Azure AD-galleriet.

   Du kan också använda anspråk som utfärdats i SAML-token för företagsprogrammet med hjälp av [Microsofts vägledningsdokumentation.](../develop/active-directory-claims-mapping.md) Se till att detta mappar till det du förväntar dig att få i SAML-svaret för ditt program. Om du stöter på problem under konfigurationen kan du använda vår vägledning [om hur du felsöker SSO-integrering.](./debug-saml-sso-issues.md)

### <a name="provide-sso-change-communications-to-end-users"></a>Tillhandahålla kommunikation om ändring av enkel inloggning till slutanvändare

Implementera din kommunikationsplan. Se till att du låter slutanvändarna veta att en ändring kommer, när den har anlänt, vad de ska göra nu och hur de kan söka hjälp.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifiera slutanvändarscenarier för enkel inloggning

Du kan använda följande testfall för att utföra tester på företagsägda och personliga enheter för att säkerställa att dina SSO-konfigurationer fungerar som förväntat. Scenarierna nedan förutsätter att en användare navigerar till en program-URL och går igenom ett autentiseringsflöde som initieras av tjänstleverantören (SP-initierat autentiseringsflöde).

| Scenario | Förväntat resultat för SP-initierat authflöde av användare |
|----------|---------------------------------------------------|
| Logga in på programmet med IE i corpnet. | Integrerad Windows-autentisering (IWA) sker utan ytterligare uppmaningar. |
| Logga in på programmet med Internet-internet när du är utanför corpnet med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och webbläsaren frågar efter MFA. |
| Logga in på programmet med IE när du är utanför corpnet med en aktuell session och har aldrig utfört MFA. | Användaren uppmanas inte att ange en första faktor. Användaren uppmanas att ange MFA. |
| Logga in på programmet med IE när du är utanför corpnet med en aktuell session och har redan utfört MFA i den här sessionen. | Användaren uppmanas inte att ange en första faktor. Användaren får inte MFA. Användarens SSO:er i programmet. |
| Logga in på programmet med Chrome/Firefox/Safari när du är utanför corpnet med en aktuell session och har redan utfört MFA i den här sessionen. | Användaren uppmanas inte att ange en första faktor. Användaren får inte MFA. Användarens enkel inloggning används i programmet. |
| Logga in på programmet med Chrome/Firefox/Safari när du är utanför corpnet med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och webbläsaren frågar efter MFA. |
| Logga in på programmet med Chrome/Firefox i företagsnätverket med en aktuell session. | Användaren uppmanas inte att ange den första faktorn. Användaren får inte MFA. Användarens enkel inloggning används i programmet. |
| Logga in på programmet med appens mobilapp med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och ADAL-klienten frågar efter MFA. |
| Obehöriga användare försöker logga in i programmet med inloggnings-URL: en. | Formulärbaserad prompt på AD FS Server. Användaren kan inte logga in med första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösenord. | Användaren navigerar till programmets URL och får felmeddelandet om felaktigt användarnamn/lösenord. |
| Behörig användare klickar på länken i ett e-postmeddelande och har redan autentiserats. | Användaren klickar på URL:en och är inloggad i programmet utan ytterligare frågor. |
| Behörig användare klickar på länken i ett e-postmeddelande och har ännu inte autentiserats. | Användaren klickar på URL:en och uppmanas att autentisera med första faktorn. |
| Behörig användare loggar in i programmet med en appmobilapp (SP-initierad) med ett nytt inloggningsförsök. | Formulärbaserad prompt på AD FS Server. Användaren loggar in och ADAL-klienten frågar efter MFA. |
| Obehörig användare försöker logga in i programmet med inloggnings-URL (SP-initierad). | Formulärbaserad prompt på AD FS Server. Användaren kan inte logga in med första faktorn. |
| Behörig användare försöker logga in men anger ett felaktigt lösenord.| Användaren navigerar till programmets URL och får felmeddelandet felaktigt användarnamn/lösenord. |
| Behöriga användare loggar ut och loggar sedan in igen. | Om utloggnings-URL:en har konfigurerats loggas användaren ut från alla tjänster och uppmanas att autentisera. |
| Behöriga användare loggar ut och loggar sedan in igen. | Om utloggnings-URL:en inte har konfigurerats loggas användaren in automatiskt igen med hjälp av en befintlig token från den befintliga Azure AD-webbläsarsessionen. |
| Behörig användare klickar på länken i ett e-postmeddelande och har redan autentiserats. | Användaren klickar på URL:en och loggas in i programmet utan ytterligare uppmaningar. |
| Behörig användare klickar på länken i ett e-postmeddelande och har ännu inte autentiserats. | Användaren klickar på URL:en och uppmanas att autentisera med första faktorn. |

## <a name="manage-sso"></a>Hantera enkel inloggning

I det här avsnittet beskrivs kraven och rekommendationerna för att hantera enkel inloggning.

### <a name="required-administrative-roles"></a>Administrativa roller som krävs

Använd alltid rollen med de minsta tillgängliga behörigheterna för att utföra den nödvändiga uppgiften inom Azure Active Directory. Microsoft rekommenderar att [du granskar de olika roller som](../roles/permissions-reference.md) är tillgängliga och väljer rätt för att lösa dina behov för varje person för det här programmet. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Persona| Roller | Azure AD-roll (om det behövs) |
|--------|-------|-----------------------------|
| Supportavdelningsadministratör | Nivå 1-stöd | Inget |
| Identitetsadministratör | Konfigurera och felsöka när problem påverkar Azure AD | Global administratör |
| Programadministratör | Atterstation av användare i program, konfiguration av användare med behörigheter | Inget |
| Infrastrukturadministratörer | Cert rollover owner (Ägare av certifikatsdeover) | Global administratör |
| Företagsägare/intressent | Atterstation av användare i program, konfiguration av användare med behörigheter | Inget |

Vi rekommenderar att [du använder Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) för att hantera dina roller för att tillhandahålla ytterligare granskning, kontroll och åtkomstgranskning för användare med katalogbehörigheter.

### <a name="sso-certificate-lifecycle-management"></a>Livscykelhantering för SSO-certifikat

Det är viktigt att identifiera rätt roller och e-postdistributionslistor med uppgiften att hantera livscykeln för signeringscertifikatet mellan Azure AD och det program som konfigureras med enkel inloggning. Här är några av de viktigaste rollerna som vi rekommenderar att du har på plats:

- Ägare för uppdatering av användaregenskaper i programmet
- Ägarens jour för support om programbrytning/korrigering
- Noggrant övervakad e-postdistributionslista för certifikatrelaterade ändringsmeddelanden

Den maximala livslängden för ett certifikat är tre år. Vi rekommenderar att du upprättar en process för hur du hanterar en certifikatändring mellan Azure AD och ditt program. Detta kan hjälpa till att förhindra eller minimera ett avbrott på grund av att ett certifikat upphör att gälla eller tvinga fram en certifikatomställning.

### <a name="rollback-process"></a>Återställningsprocess

När du har slutfört testningen baserat på dina testfall är det dags att gå vidare till produktion med ditt program. När du flyttar till produktion implementerar du dina planerade och testade konfigurationer i produktionsklienten och distribuerar dem till dina användare. Det är dock viktigt att planera vad du ska göra om distributionen inte går som planerat. Om SSO-konfigurationen misslyckas under distributionen måste du förstå hur du kan minimera eventuella avbrott och minska påverkan på dina användare.

Tillgängligheten för autentiseringsmetoder i programmet avgör vilken strategi du bör använda. Se alltid till att du har detaljerad dokumentation för appägare om exakt hur du kommer tillbaka till det ursprungliga konfigurationstillståndet för inloggning om distributionen får problem.

- **Om din app stöder flera identitetsproviders,** till exempel LDAP och AD FS Ping, ska du inte ta bort den befintliga SSO-konfigurationen under utrullningen. I stället inaktiverar du den under migreringen om du behöver växla tillbaka den senare. 

- **Om din app** inte stöder flera IDE:er men tillåter användare att logga in med formulärbaserad autentisering (användarnamn/lösenord) ska du se till att användarna kan använda den här metoden om den nya SSO-konfigurationskonfigurationen misslyckas.

### <a name="access-management"></a>Åtkomsthantering

Vi rekommenderar att du väljer en skalad metod när du hanterar åtkomst till resurser. Vanliga metoder är att använda lokala grupper genom att synkronisera via Azure AD Connect, skapa dynamiska grupper i [](../enterprise-users/groups-self-service-management.md) [Azure AD](../enterprise-users/groups-dynamic-membership.md)baserat på användarattribut eller skapa självbetjäningsgrupper i Azure AD som hanteras av en resursägare.

### <a name="monitor-security"></a>Övervaka säkerhet

Vi rekommenderar att du ställer in en regelbunden takt där du granskar de olika aspekterna av SaaS-appens säkerhet och utför de reparationsåtgärder som krävs.

### <a name="troubleshooting"></a>Felsökning

Följande länkar visar felsökningsscenarier. Du kanske vill skapa en specifik guide för supportpersonalen som innehåller de här scenarierna och stegen för att åtgärda dem.

#### <a name="consent-issues"></a>Problem med medgivande

- [Oväntat medgivandefel](./application-sign-in-unexpected-user-consent-prompt.md)

- [Fel vid användarmedgivande](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Inloggningsproblem

- [Problem med att logga in från en anpassad portal](./application-sign-in-other-problem-access-panel.md)

- [Problem vid inloggning från Mina appar](./application-sign-in-other-problem-access-panel.md)

- [Fel på inloggningssidan för programmet](./application-sign-in-problem-application-error.md)

- [Problem med att logga in i ett Microsoft-program](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>Problem med enkel inloggning för program

- [Problem med lösenords-SSO för program](./troubleshoot-password-based-sso.md) 

- [Problem med att logga in på SAML-baserade appar som konfigurerats för enkel inloggning](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Nästa steg

[Felsöka SAML-baserad enkel inloggning](./debug-saml-sso-issues.md)

[Anspråksmappning för appar via PowerShell](../develop/active-directory-claims-mapping.md)

[Anpassa anspråk som utfärdats i SAML-token](../develop/active-directory-saml-claims-customization.md)

[SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md)

[SAMLSign-Out protokoll för enskild enhet](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (för externa användare, till exempel partner och leverantörer)

[Villkorsstyrd åtkomst i Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Åtkomst med enkel inloggning](./what-is-single-sign-on.md)

[Självstudie om enkel inloggning för program](../saas-apps/tutorial-list.md)

[Whitepaper Download – distributionsplan för enkel inloggning](https://aka.ms/SSODeploymentPlan)