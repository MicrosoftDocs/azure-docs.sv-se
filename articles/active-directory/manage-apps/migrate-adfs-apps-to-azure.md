---
title: Flytta programautentisering från AD FS till Azure Active Directory
description: Lär dig hur du använder Azure Active Directory för att Active Directory Federation Services (AD FS) (AD FS), vilket ger användarna enkel inloggning till alla sina program.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 83e506c0a3d0b9718f94d48ea8e6b23f43e811f3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377946"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Flytta programautentisering från Active Directory Federation Services (AD FS) till Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) erbjuder en universell identitetsplattform som ger dina personer, partner och kunder en enda identitet för att få åtkomst till program och samarbeta från valfri plattform och enhet. Azure AD har en [fullständig uppsättning identitetshanteringsfunktioner.](../fundamentals/active-directory-whatis.md) Genom att standardisera din programautentisering och auktorisering till Azure AD får du dessa fördelar.

> [!TIP]
> Den här artikeln är skriven för utvecklare. Projektledare och administratörer som planerar att flytta ett program till Azure AD bör läsa [Migrera programautentisering till Azure AD.](migrate-application-authentication-to-azure-active-directory.md)

## <a name="azure-ad-benefits"></a>Fördelar med Azure AD

Om du har en lokal katalog som innehåller användarkonton har du förmodligen många program som användarna autentiseras för. Var och en av dessa appar är konfigurerad för användare att komma åt med hjälp av deras identiteter.

Användare kan också autentisera direkt med din lokal Active Directory. Active Directory Federation Services (AD FS) (AD FS) är en standardbaserad lokal identitetstjänst. Det utökar möjligheten att använda funktioner för enkel inloggning (SSO) mellan betrodda affärspartner så att användarna inte behöver logga in separat i varje program. Detta kallas federerad identitet.

Många organisationer har SaaS (Programvara som en tjänst) eller anpassade verksamhetsbaserade appar federerade direkt till AD FS tillsammans med Microsoft 365- och Azure AD-baserade appar.

  ![Program som är anslutna direkt lokalt](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> För att öka programsäkerheten är målet att ha en enda uppsättning åtkomstkontroller och principer i dina lokala och molnbaserade miljöer.

  ![Program som är anslutna via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typer av appar som ska migreras

Det är optimalt att migrera all programautentisering till Azure AD eftersom det ger dig ett enda kontrollplan för identitets- och åtkomsthantering.

Dina program kan använda moderna eller äldre protokoll för autentisering. När du planerar migreringen till Azure AD bör du överväga att migrera de appar som använder moderna autentiseringsprotokoll (till exempel SAML och Open ID Connect) först. Dessa appar kan konfigureras om för att autentisera med Azure AD antingen via en inbyggd anslutningsapp från Azure App-galleriet eller genom att registrera programmet i Azure AD. Appar som använder äldre protokoll kan integreras med hjälp av Programproxy.

Mer information finns i:

* [Använda Azure AD Programproxy att publicera lokala appar för fjärranvändare.](what-is-application-proxy.md)
* [Vad är programhantering?](what-is-application-management.md)
* [AD FS programaktivitetsrapport för att migrera program till Azure AD.](migrate-adfs-application-activity.md)
* [Övervaka AD FS med Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Migreringsprocessen

Testa dina appar och konfiguration under flytten av din appautentisering till Azure AD. Vi rekommenderar att du fortsätter att använda befintliga testmiljöer för migreringstestning när du flyttar till produktionsmiljön. Om en testmiljö inte är tillgänglig för närvarande kan du konfigurera en med [hjälp av Azure App Service](https://azure.microsoft.com/services/app-service/) eller Azure [Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), beroende på programmets arkitektur.

Du kan välja att konfigurera ett separat test av Azure AD-klientorganisationen där du kan utveckla dina appkonfigurationer.

Migreringsprocessen kan se ut så här:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Steg 1 – Aktuellt tillstånd: Produktionsappen autentiseras med AD FS

  ![Migreringssteg 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Steg 2 – (valfritt) Peka en testinstans av appen på Azure AD-testklienten

Uppdatera konfigurationen så att testinstansen av appen pekas mot en Azure AD-testklient och gör nödvändiga ändringar. Appen kan testas med användare i Azure AD-testklienten. Under utvecklingsprocessen kan du använda verktyg som [Fiddler](https://www.telerik.com/fiddler) för att jämföra och verifiera begäranden och svar.

Om det inte är möjligt att konfigurera en separat testklient kan du hoppa över det här steget och peka en testinstans av appen till din Azure AD-produktionsklient enligt beskrivningen i steg 3 nedan.

  ![Migreringssteg 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Steg 3 – Peka en testinstans av appen till Azure AD-produktionsklienten

Uppdatera konfigurationen så att den pekar din testinstans av appen till din Azure AD-produktionsklient. Nu kan du testa med användare i produktionsklienten. Om det behövs kan du läsa avsnittet i den här artikeln om att övergå till användare.

  ![Migreringssteg 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Steg 4 – Peka produktionsappen till Azure AD-produktionsklienten

Uppdatera konfigurationen av din produktionsapp så att den pekar på azure AD-produktionsklienten.

  ![Migreringssteg 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Appar som autentiserar med AD FS kan använda Active Directory-grupper för behörigheter. Använd [Azure AD Connect synkronisera](../hybrid/how-to-connect-sync-whatis.md) identitetsdata mellan din lokala miljö och Azure AD innan du påbörjar migreringen. Kontrollera dessa grupper och medlemskap före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

### <a name="line-of-business-apps"></a>Verksamhetsapplikationer

Dina verksamhetsapplikationer är de som din organisation har utvecklat eller de som är en standardpaketerad produkt. Exempel är appar som bygger på Windows Identity Foundation och SharePoint-appar (inte SharePoint Online).

Verksamhetsapplikationer som använder OAuth 2.0, OpenID Connect eller WS-Federation kan integreras med Azure AD som [appregistreringar.](../develop/quickstart-register-app.md) Integrera anpassade appar som använder SAML 2.0 eller WS-Federation som [icke-galleriprogram](add-application-portal.md) på sidan för [företagsprogram](https://portal.azure.com/)i Azure Portal .

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning

Appar som använder SAML 2.0 för autentisering kan konfigureras för [SAML-baserad enkel inloggning](what-is-single-sign-on.md) (SSO). Med SAML-baserad enkel inloggning kan du mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk.

Information om hur du konfigurerar ett SaaS-program för SAML-baserad enkel inloggning finns i [Snabbstart: Konfigurera SAML-baserad enkel inloggning.](add-application-portal-setup-sso.md)

  ![Skärmbilder av SAML-användare med enkel inloggning ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Många SaaS-program har [en programspecifik självstudie](../saas-apps/tutorial-list.md) som steg för steg går igenom konfigurationen för SAML-baserad enkel inloggning.

  ![app-självstudie](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Vissa appar är enkla att migrera. Appar med mer komplexa krav, till exempel anpassade anspråk, kan kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect. Information om anspråksmappningar som stöds finns i Gör så här: Anpassa anspråk som genereras i token för en specifik app i en [klientorganisation (förhandsversion).](../develop/active-directory-claims-mapping.md)

Tänk på följande begränsningar när du mappar attribut:

* Alla attribut som kan utfärdas i AD FS visas i Azure AD som attribut som ska sändas till SAML-token, även om dessa attribut synkroniseras. När du redigerar attributet visar **listrutan** Värde de olika attribut som är tillgängliga i Azure AD. Kontrollera [Azure AD Connect för synkroniseringsämnen](../hybrid/how-to-connect-sync-whatis.md) för att se till att ett obligatoriskt attribut, till exempel **samAccountName,** synkroniseras med Azure AD. Du kan använda tilläggsattributen för att generera alla anspråk som inte ingår i standardanvändarschemat i Azure AD.
* I de vanligaste scenarierna behöver endast **NameID**-anspråk och andra vanliga anspråk för användaridentifierare anges för en app. För att avgöra om det krävs ytterligare anspråk undersöker du vilka anspråk som du utfärdar från AD FS.
* Alla anspråk kan inte utfärdas eftersom vissa anspråk skyddas i Azure AD.
* Möjligheten att använda krypterade SAML-token är nu i förhandsversion. Se [Gör så här: anpassa anspråk som utfärdats i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)

### <a name="software-as-a-service-saas-apps"></a>SaaS-appar (programvara som en tjänst)

Om dina användare loggar in på SaaS-appar som Salesforce, ServiceNow eller Workday och är integrerade med AD FS använder du federerad inloggning för SaaS-appar.

De flesta SaaS-program kan konfigureras i Azure AD. Microsoft har många förkonfigurerade anslutningar till SaaS-appar i  [Azure AD-appgalleriet,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)vilket gör övergången enklare. SAML 2.0-program kan integreras med Azure AD via Azure AD-appgalleriet eller som [icke-galleriprogram.](add-application-portal.md)

Appar som använder OAuth 2.0 eller OpenID Connect kan integreras på liknande sätt med Azure AD som [appregistreringar.](../develop/quickstart-register-app.md) Appar som använder äldre protokoll kan använda [Azure AD-Programproxy](application-proxy.md) för att autentisera med Azure AD.

Om du har problem med att registrera saaS-appar kan du kontakta [supportaliaset för SaaS-programintegrering.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

### <a name="saml-signing-certificates-for-sso"></a>SAML-signeringscertifikat för enkel inloggning

Signeringscertifikat är en viktig del av all distribution med enkel inloggning. Azure AD skapar signeringscertifikaten för att upprätta SAML-baserad federerad enkel inloggning till dina SaaS-program. När du lägger till galleriprogram eller program som inte är gallerier konfigurerar du det tillagda programmet med hjälp av alternativet för federerad enkel inloggning. Se [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>SAML-tokenkryptering

Både AD FS och Azure AD ger tokenkryptering – möjligheten att kryptera SAML-säkerhetskontroller som går till program. Försäkran krypteras med en offentlig nyckel och dekrypteras av det mottagande programmet med matchande privat nyckel. När du konfigurerar tokenkryptering laddar du upp X.509-certifikatfiler för att tillhandahålla de offentliga nycklarna.

Information om Azure AD SAML-tokenkryptering och hur du konfigurerar det finns i Så här konfigurerar du [Azure AD SAML-tokenkryptering.](howto-saml-token-encryption.md)  

> [!NOTE]
> Tokenkryptering är en premium Azure Active Directory funktion (Azure AD). Mer information om Azure AD-utgåvor, -funktioner och -priser finns i [Priser för Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Appar och konfigurationer som kan flyttas idag

Appar som du enkelt kan flytta idag omfattar SAML 2.0-appar som använder standarduppsättningen med konfigurationselement och anspråk. Dessa standardobjekt är:

* UPN (User Principal Name)
* E-postadress
* Tilltalsnamn
* Efternamn
* Alternativa attribut som SAML **NameID**, inklusive e-postattribut för Azure AD, e-postprefix, anställnings-ID, tilläggsattribut 1–15 eller lokalt **SamAccountName**-attribut. Mer information finns i [Redigera NameIdentifier-anspråket](../develop/active-directory-saml-claims-customization.md).
* Anpassade anspråk.

Följande kräver ytterligare konfigurationssteg för att migrera till Azure AD:

* Anpassade regler för auktorisering eller multifaktorautentisering (MFA) i AD FS. Du konfigurerar dem med hjälp [av funktionen villkorsstyrd åtkomst i Azure AD.](../conditional-access/overview.md)
* Appar med flera svars-URL-slutpunkter. Du konfigurerar dem i Azure AD med powershell eller Azure Portal gränssnittet.
* WS-Federation-appar, till exempel SharePoint-appar som kräver SAML version 1.1-tokens. Du kan konfigurera dem manuellt med hjälp av PowerShell. Du kan också lägga till en förintegrerad allmän mall för SharePoint- och SAML 1.1-program från galleriet. Vi stöder SAML 2.0-protokollet.
* Transformeringsregler för utfärdande av komplexa anspråk. Information om anspråksmappningar som stöds finns i:
   *  [Anspråksmappning i Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Appar och konfigurationer som inte stöds i Azure AD i nuläget

Appar som kräver vissa funktioner kan inte migreras i dag.

#### <a name="protocol-capabilities"></a>Protokollfunktioner

Appar som kräver följande protokollfunktioner kan inte migreras i dag:

* Stöd för WS-Trust ActAs
* SAML-artefaktmatchning
* Signature verification of signed SAML requests ‎
  > [!Note]
  > Signerade begäranden accepteras, men signaturen är inte verifierad.

  ‎Given that Azure AD only returns the token to endpoints preconfigured in the application, signature verification probably isn't required in most cases.

#### <a name="claims-in-token-capabilities"></a>Anspråk i tokenfunktioner

Appar som kräver följande anspråk i tokenfunktioner kan inte migreras i dag.

* Anspråk från andra attributarkiv än Azure AD-katalogen, såvida inte dessa data synkroniseras med Azure AD. Mer information finns i Översikt över [AZURE AD-synkroniserings-API:et.](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
* Utfärdande av katalogattribut med flera värden. Vi kan till exempel inte utfärda ett flervärdesanspråk för proxyadresser just nu.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mappa appinställningar från AD FS till Azure AD

Migreringen kräver en utvärdering av hur programmet konfigureras lokalt och sedan mappas konfigurationen till Azure AD. AD FS och Azure AD fungerar på samma sätt, så koncepten för att konfigurera webbadresser för förtroende, in- och utloggning samt identifierare gäller i båda fallen. Dokumentera de AD FS konfigurationsinställningarna för dina program så att du enkelt kan konfigurera dem i Azure AD.

### <a name="map-app-configuration-settings"></a>Mappa appkonfigurationsinställningar

I följande tabell beskrivs några av de vanligaste mappningarna av inställningar mellan en AD FS förlitande part till Azure AD Enterprise-program:

* AD FS – Hitta inställningen i AD FS förlitande partsförtroende för appen. Högerklicka på den förlitande parten och välj Egenskaper.
* Azure AD – Inställningen konfigureras i [Azure Portal](https://portal.azure.com/) i varje programs egenskaper för enkel inloggning.

| Konfigurationsuppsättning| AD FS| Så här konfigurerar du i Azure AD| SAML-token |
| - | - | - | - |
| **Inloggnings-URL för appen** <p>URL:en för användaren att logga in på appen i ett SAML-flöde som initierats av en tjänstleverantör (SP).| Ej tillämpligt| Öppna Grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appens svars-URL** <p>Appens URL ur identitetsproviderns perspektiv (IdP). IdP:n skickar användaren och token här när användaren har loggat in på IdP:n.  Detta kallas även FÖR **SAML-konsumentslutpunkt för försäkran.**| Välj **fliken Slutpunkter**| Öppna Grundläggande SAML-konfiguration från SAML-baserad inloggning| Målelement i SAML-token. Exempelvärde: `https://contoso.my.salesforce.com` |
| **Appens webbadress för utloggning** <p>Det här är den URL som rensningsbegäranden för utdata skickas till när en användare loggar ut från en app. IdP:n skickar även en begäran om att logga ut användaren från alla andra appar.| Välj **fliken Slutpunkter**| Öppna Grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appidentifierare** <p>Det här är appidentifieraren ur IdP:ns perspektiv. Inloggningens URL-värde används ofta för identifierare (men inte alltid).  ‎Sometimes the app calls this the "entity ID."| Välj **fliken Identifierare**|Öppna Grundläggande SAML-konfiguration från SAML-baserad inloggning| Mappar till **audience-elementet** i SAML-token. |
| **Federationsmetadata för appen** <p>Det här är platsen för appens federationsmetadata. IdP:n använder den till att automatiskt uppdatera specifika konfigurationsinställningar, som t.ex. slutpunkter eller krypteringscertifikat.| Välj **fliken** Övervakning| Ej tillämpligt. Azure AD stöder inte användning av programfederationens metadata direkt. Du kan importera federationsmetadata manuellt.| Ej tillämpligt |
| **Användaridentifierare/namn-ID** <p>Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.  ‎This attribute is typically either the UPN or the email address of the user.| Anspråksregler. I de flesta fall utfärdar anspråksregeln ett anspråk med en typ som slutar med **NameIdentifier**.| Du hittar identifieraren under rubriken **Användarattribut och Anspråk**. Som standard används UPN| Mappar till **NameID-elementet** i SAML-token. |
| **Andra anspråk** <p>Exempel på annan anspråksinformation som vanligtvis skickas från IdP:n till appen är förnamn, efternamn, e-postadress och gruppmedlemskap.| I AD FS finns detta som övriga anspråksregler hos den förlitande parten.| Du hittar identifieraren under rubriken **Användarattribut & Anspråk**. Välj **Visa** och redigera alla andra användarattribut.| Ej tillämpligt |

### <a name="map-identity-provider-idp-settings"></a>Mappa inställningar för identitetsprovider (IdP)

Konfigurera dina program så att de pekar på Azure AD jämfört AD FS för enkel inloggning. Här fokuserar vi på SaaS-appar som använder SAML-protokollet. Det här konceptet gäller dock även för anpassade verksamhetsapplikationer.

> [!NOTE]
> Konfigurationsvärdena för Azure AD följer mönstret där ditt Azure-klientorganisations-ID ersätter {tenant-id} och Program-ID ersätter {application-id}. Du hittar den här informationen i [Azure Portal](https://portal.azure.com/) under **Azure Active Directory > Egenskaper:**

* Välj Katalog-ID för att se ditt klientorganisations-ID.
* Välj Program-ID för att se ditt program-ID.

 På en hög nivå mappar du följande viktiga Konfigurationselement för SaaS-appar till Azure AD.

| Element| Konfigurationsvärde |
| - | - |
| Utfärdare av identitetsprovider| https: \/ /sts.windows.net/{tenant-id}/ |
| Inloggnings-URL för identitetsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Utloggnings-URL för identitetsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Plats för federationsmetadata| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Mappa inställningar för enkel inloggning för SaaS-appar

SaaS-appar behöver veta var autentiseringsbegäranden ska skickas och hur de mottagna token ska verifieras. I följande tabell beskrivs elementen för att konfigurera inställningar för enkel inloggning i appen och deras värden eller platser i AD FS och Azure AD

| Konfigurationsuppsättning| AD FS| Så här konfigurerar du i Azure AD |
| - | - | - |
| **IdP-inloggnings-URL** <p>Inloggnings-URL för IdP:n från appens perspektiv (där användaren omdirigeras för inloggning).| Den AD FS inloggnings-URL:en är AD FS namnet på federationstjänsten följt av "/adfs/ls/". <p>Exempelvis: `https://fs.contoso.com/adfs/ls/`| Ersätt {tenant-id} med ditt klientorganisations-ID. <p> ‎For apps that use the SAML-P protocol: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>‎For apps that use the WS-Federation protocol: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP-ut logga ut-URL**<p>Utdata-URL för IdP:n från appens perspektiv (där användaren omdirigeras när de väljer att logga ut från appen).| Utlåsnings-URL:en är antingen samma som inloggnings-URL:en eller samma URL med "wa=wsignout1.0" tillagt. Exempelvis: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Ersätt {tenant-id} med ditt klientorganisations-ID.<p>För appar som använder SAML-P-protokollet:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> ‎For apps that use the WS-Federation protocol: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certifikat för tokensignering**<p>IdP:n använder den privata nyckeln för certifikatet för att signera utfärdade token. Den kontrollerar att token kom från samma IdP som appen är konfigurerad att ha förtroende för.| AD FS-certifikatet för tokensignering finns i AD FS-hanteringen under **Certifikat**.| Du hittar det Azure Portal i programmets egenskaper för enkel inloggning under rubriken **SAML-signeringscertifikat**.  Därifrån kan du ladda ner certifikatet för uppladdning till appen.  <p>‎If the application has more than one certificate, you can find all certificates in the federation metadata XML file. |
| **Identifierare/utfärdare**<p>IdP:ns identifierare ur appens perspektiv (kallas ibland för "utfärdar-ID").<p>I SAML-token visas värdet som elementet Issuer.| Identifieraren för AD FS är vanligtvis federationstjänstens identifierare i AD FS Management under **Service > Edit Federationstjänst Properties**. Exempelvis: `http://fs.contoso.com/adfs/services/trust`| Ersätt {tenant-id} med ditt klientorganisations-ID.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **IdP-federationsmetadata**<p>Plats för IdP:ns offentligt tillgängliga federationsmetadata. (Federationsmetadata används av vissa appar som ett alternativ för administratören och konfigurerar URL:er, identifierare och certifikat för tokensignering individuellt.)| Leta reda AD FS URL för federationsmetadata i AD FS Management under **Service > Endpoints > Metadata > Type: Federation Metadata**. Exempelvis: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Motsvarande värde för Azure AD följer mönstret [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Ersätt {TenantDomainName} med klientorganisationens namn i formatet "contoso.onmicrosoft.com".   <p>Mer information finns i [Federationsmetadata](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Representera AD FS säkerhetsprinciper i Azure AD

När du flyttar din appautentisering till Azure AD skapar du mappningar från befintliga säkerhetsprinciper till motsvarande eller alternativa varianter som är tillgängliga i Azure AD. Genom att se till att dessa mappningar kan utföras samtidigt som du uppfyller de säkerhetsstandarder som krävs av appägarna blir resten av appmigreringen betydligt enklare.

För varje regelexempel visar vi hur regeln ser ut i AD FS, motsvarande kod AD FS-regelspråk och hur den mappar till Azure AD.

### <a name="map-authorization-rules"></a>Mappa auktoriseringsregler

Följande är exempel på olika typer av auktoriseringsregler i AD FS och hur du mappar dem till Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Exempel 1: Tillåt åtkomst för alla användare

Bevilja åtkomst till alla användare i AD FS:

  ![Skärmbild som visar dialogrutan Konfigurera enkel Sign-On med SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Detta mappar till Azure AD på något av följande sätt:

1. Ange **Användartilldelning som krävs** till **Nej.**

    ![redigera åtkomstkontrollprincip för SaaS-appar ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Om **du vill ställa in Användartilldelning** **som** krävs till Ja måste användarna ha tilldelats till programmet för att få åtkomst. När det här **är inställt** på Nej har alla användare åtkomst. Den här växeln styr inte vad användarna ser i **Mina appar** upplevelsen.

1. På fliken **Användare och grupper tilldelar** du programmet till den **automatiska gruppen Alla** användare. Du måste [aktivera dynamiska grupper i](../enterprise-users/groups-create-rule.md) din Azure AD-klientorganisation för att **standardgruppen Alla** användare ska vara tillgänglig.

    ![Mina SaaS-appar i Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Exempel 2: Tillåt en grupp explicit

Explicit gruppauktorisering i AD FS:

  ![Skärmbild som visar dialogrutan Redigera regel för anspråksregeln Tillåt domänadministratörer.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Så här mappar du den här regeln till Azure AD:

1. I [Azure Portal](https://portal.azure.com/)skapar [du en användargrupp](../fundamentals/active-directory-groups-create-azure-portal.md) som motsvarar gruppen med användare från AD FS.
1. Tilldela appbehörigheter till gruppen:

    ![Lägg till tilldelning ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Exempel 3: Auktorisera en specifik användare

Explicit användarauktorisering i AD FS:

  ![Skärmbild som visar dialogrutan Redigera regel för tillåt en specifik användares anspråksregel med den inkommande anspråkstypen Primär S-ID.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Så här mappar du den här regeln till Azure AD:

* I [Azure Portal](https://portal.azure.com/)lägger du till en användare i appen via fliken Lägg till tilldelning i appen enligt nedan:

    ![Mina SaaS-appar i Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mappa regler för multifaktorautentisering

En lokal distribution av [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) och AD FS fungerar fortfarande efter migreringen eftersom du är federerad med AD FS. Överväg dock att migrera till Azures inbyggda MFA-funktioner som är knutna till Azure AD:s arbetsflöden för villkorsstyrd åtkomst.

Följande är exempel på typer av MFA-regler i AD FS och hur du kan mappa dem till Azure AD baserat på olika villkor.

MFA-regelinställningar i AD FS:

  ![Skärmbild som visar Villkor för Azure A D i Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exempel 1: Framtvinga MFA baserat på användare/grupper

Väljaren för användare/grupper är en regel som gör att du kan tillämpa MFA per grupp (grupp-SID) eller per användare (primärt SID). Förutom tilldelningar av användare/grupper fungerar alla ytterligare kryssrutor i användargränssnittet för AD FS MFA-konfiguration som ytterligare regler som utvärderas efter att regeln för användare/grupper har verkställts.

Ange MFA-regler för en användare eller en grupp i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Välj **Tilldelningar**. Lägg till de användare eller grupper som du vill använda MFA för.
1. Konfigurera alternativen **för Åtkomstkontroller** enligt nedan:

    ‎![Skärmbild som visar fönstret Bevilja där du kan bevilja åtkomst.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exempel 2: Framtvinga MFA för oregistrerade enheter

Ange MFA-regler för oregistrerade enheter i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Ange **Tilldelningar till** **Alla användare.**
1. Konfigurera alternativen **för Åtkomstkontroller** enligt nedan:

    ![Skärmbild som visar fönstret Bevilja där du kan bevilja åtkomst och ange andra begränsningar.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

När du  ställer in alternativet För flera kontroller på Kräv en av de valda kontrollerna **innebär** det att om något av de villkor som anges i kryssrutan uppfylls av användaren, beviljas användaren åtkomst till din app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exempel 3: Framtvinga MFA baserat på plats

Ange MFA-regler baserat på en användares plats i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Ange **Tilldelningar till** **Alla användare.**
1. [Konfigurera namngivna platser i Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). Annars är federation inifrån företagsnätverket betrott.
1. Konfigurera **villkorsreglerna** för att ange de platser som du vill framtvinga MFA för.

    ![Skärmbild som visar fönstret Platser för Villkorsregler.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Konfigurera alternativen **för Åtkomstkontroller** enligt nedan:

    ![Mappa principer för åtkomstkontroll](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mappa Emit-attribut som anspråksregel

Generera attribut som anspråksregel i AD FS:

  ![Skärmbild som visar dialogrutan Redigera regel för Generera attribut som anspråk.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Så här mappar du regeln till Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **Företagsprogram** och sedan **Enkel inloggning för** att visa den SAML-baserade inloggningskonfigurationen:

    ![Skärmbild som visar sidan Enkel inloggning för ditt företagsprogram.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Välj **Redigera** (markerad) för att ändra attributen:

    ![Det här är sidan för att redigera användarattribut och anspråk](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mappa inbyggda principer för åtkomstkontroll

Inbyggda principer för åtkomstkontroll i AD FS 2016:

  ![Inbyggd åtkomstkontroll i Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Om du vill implementera inbyggda principer [](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) i Azure AD använder du en ny princip för villkorlig åtkomst och konfigurerar åtkomstkontrollerna, eller använder den anpassade principdesignern i AD FS 2016 för att konfigurera principer för åtkomstkontroll. Regelredigeraren har en fullständig lista över alternativen Tillåt och Förutom som kan hjälpa dig att göra alla typer av permutationer.

  ![Principer för Azure AD-åtkomstkontroll](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

I den här tabellen har vi listat några användbara alternativ för Tillåt och Förutom och hur de mappar till Azure AD.

| Alternativ | Hur konfigurerar jag alternativet Tillåt i Azure AD?| Hur konfigurerar jag förutom alternativet i Azure AD? |
| - | - | - |
| Från specifika nätverk| Mappar till [namngiven plats](../reports-monitoring/quickstart-configure-named-locations.md) i Azure AD| Använd alternativet **Exkludera** för [betrodda platser](../conditional-access/location-condition.md) |
| Från specifika grupper| [Ange en tilldelning för användare/grupper](assign-user-or-group-access-portal.md)| Använd alternativet **Exkludera** i Användare och grupper |
| Från enheter med specifik förtroendenivå| Ange detta från **kontrollen Enhetstillstånd** under Tilldelningar -> villkor| Använd alternativet **Undanta** under Villkor för enhetstillstånd och Inkludera **alla enheter** |
| Med specifika anspråk i begäran| Den här inställningen kan inte migreras| Den här inställningen kan inte migreras |

Här är ett exempel på hur du konfigurerar alternativet Undanta för betrodda platser i Azure Portal:

  ![Skärmbild av mappning av principer för åtkomstkontroll](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Övergång av användare från AD FS till Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synkronisera AD FS grupper i Azure AD

När du mappar auktoriseringsregler kan appar som autentiserar AD FS använda Active Directory-grupper för behörigheter. I sådana fall använder du [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) att synkronisera dessa grupper med Azure AD innan du migrerar programmen. Kontrollera att du verifierar dessa grupper och medlemskap före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

Mer information finns i Krav [för att använda gruppattribut som synkroniseras från Active Directory.](../hybrid/how-to-connect-fed-group-claims.md)

### <a name="set-up-user-self-provisioning"></a>Konfigurera självetablering av användare

Vissa SaaS-program stöder möjligheten att självetablerera användare när de loggar in i programmet för första gången. I Azure AD syftar appetablering på att automatiskt skapa användaridentiteter och roller i molnet[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)program som användarna behöver åtkomst till. Användare som migreras har redan ett konto i SaaS-programmet. Alla nya användare som läggs till efter migreringen måste etableras. Testa [SaaS-appetablering](../app-provisioning/user-provisioning.md) när programmet har migrerats.

### <a name="sync-external-users-in-azure-ad"></a>Synkronisera externa användare i Azure AD

Dina befintliga externa användare kan konfigureras på följande två sätt på AD FS:

* **Externa användare med ett lokalt konto i din organisation**– Du fortsätter att använda dessa konton på samma sätt som dina interna användarkonton fungerar. Dessa externa användarkonton har ett huvudnamn i din organisation, även om kontots e-postadress kan peka externt. När du går vidare med migreringen kan du dra nytta av fördelarna som [Azure AD B2B](../external-identities/what-is-b2b.md) erbjuder genom att migrera dessa användare till att använda sin egen företagsidentitet när en sådan identitet är tillgänglig. Detta effektiviserar inloggningsprocessen för dessa användare, eftersom de ofta är inloggade med sin egen företagsinloggning. Din organisations administration är också enklare genom att inte behöva hantera konton för externa användare.
* **Federerade externa identiteter**– Om du för närvarande federerar med en extern organisation har du några metoder att använda:
  * [Lägg Azure Active Directory B2B-samarbetsanvändare i Azure Portal](../external-identities/add-users-administrator.md). Du kan skicka B2B-samarbetsinbjudningar proaktivt från Den administrativa Azure AD-portalen till partnerorganisationen så att enskilda medlemmar kan fortsätta att använda de appar och tillgångar som de är vana vid.
  * [Skapa ett arbetsflöde för B2B-registrering via](../external-identities/self-service-portal.md) självbetjäning som genererar en begäran för enskilda användare i din partnerorganisation med hjälp av B2B-inbjudnings-API:et.

Oavsett hur dina befintliga externa användare har konfigurerats har de förmodligen behörigheter som är associerade med deras konto, antingen i gruppmedlemskap eller specifika behörigheter. Utvärdera om dessa behörigheter behöver migreras eller rensas. Konton i din organisation som representerar en extern användare måste inaktiveras när användaren har migrerats till en extern identitet. Migreringsprocessen bör diskuteras med dina affärspartner, eftersom det kan uppstå ett avbrott i deras möjlighet att ansluta till dina resurser.

## <a name="migrate-and-test-your-apps"></a>Migrera och testa dina appar

Följ migreringsprocessen som beskrivs i den här artikeln. Gå sedan till [Azure Portal](https://aad.portal.azure.com/) för att testa om migreringen lyckades.

Följ dessa anvisningar:

1. Välj **Företagsprogram**  >  **Alla program** och leta upp din app i listan.
1. Välj **Hantera**  >  **användare och grupper** för att tilldela minst en användare eller grupp till appen.
1. Välj **Hantera villkorlig**  >  **åtkomst.** Granska listan över principer och se till att du inte blockerar åtkomsten till programmet med en princip [för villkorlig åtkomst.](../conditional-access/overview.md)

Kontrollera att enkel inloggning fungerar korrekt beroende på hur du konfigurerar din app.

| Autentiseringstyp| Testning |
| :- | :- |
| OAuth/OpenID Connect| Välj **Företagsprogram > behörigheter** och se till att du har samtyckt till programmet i användarinställningarna för din app.|
| SAML-baserad SSO | Använd knappen [Testa SAML-inställningar](debug-saml-sso-issues.md) under **Enkel inloggning**. |
| Password-Based enkel inloggning |  Ladda ned och installera [tillägget för säker inloggning i MyApps.](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md) Det här tillägget hjälper dig att starta någon av organisationens molnappar som kräver att du använder en process för enkel inloggning. |
| Programproxy | Se till att anslutningsappen körs och tilldelas till ditt program. Mer hjälp [finns Programproxy felsökningsguiden.](application-proxy-troubleshoot.md) |

> [!NOTE]
> Cookies från den gamla AD FS-miljön bevaras på användardatorerna. Dessa cookies kan orsaka problem med migreringen eftersom användarna kan dirigeras till den gamla AD FS inloggningsmiljön jämfört med den nya Azure AD-inloggningen. Du kan behöva rensa webbläsarens cookies manuellt eller använda ett skript. Du kan också använda System Center Konfigurationshanteraren eller en liknande plattform.

### <a name="troubleshoot"></a>Felsöka

Om det finns några fel från testet av de migrerade programmen kan felsökning vara det första steget innan du går tillbaka till de AD FS förlitande parterna. Se [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Återställningsmigrering

Om migreringen misslyckas rekommenderar vi att du lämnar de befintliga förlitande parterna på de AD FS-servrarna och tar bort åtkomsten till de förlitande parterna. Detta möjliggör en snabb återställning om det behövs under distributionen.

### <a name="employee-communication"></a>Medarbetarkommunikation

Även om själva det planerade avbrottsfönstret kan vara minimalt bör du ändå planera att kommunicera dessa tidsramar proaktivt till anställda samtidigt som du växlar från AD FS till Azure AD. Se till att din appupplevelse har en feedbackknapp eller pekare till supporten för problem.

När distributionen är klar kan du informera användarna om den lyckade distributionen och påminna dem om de steg som de behöver vidta.

* Instruera användarna att använda Mina appar [att](https://myapps.microsoft.com) komma åt alla migrerade program.
* Påminn användarna om att de kan behöva uppdatera sina MFA-inställningar.
* Om Self-Service lösenordsåterställning har distribuerats kan användarna behöva uppdatera eller verifiera sina autentiseringsmetoder. Se [MFA-](https://aka.ms/mfatemplates) och [SSPR-kommunikationsmallar](https://aka.ms/ssprtemplates) för slutanvändare.

### <a name="external-user-communication"></a>Extern användarkommunikation

Den här gruppen av användare är vanligtvis den mest ytterst påverkade i händelse av problem. Detta gäller särskilt om din säkerhetsstatus kräver en annan uppsättning regler för villkorlig åtkomst eller riskprofiler för externa partner. Se till att externa partner är medvetna om molnmigreringsschemat och har en tidsram under vilken de uppmuntras att delta i en pilotdistribution som testar alla flöden som är unika för externt samarbete. Kontrollera slutligen att de har ett sätt att komma åt supporten om det uppstår problem.

## <a name="next-steps"></a>Nästa steg

* Läs [Migrera programautentisering till Azure AD.](https://aka.ms/migrateapps/whitepaper)
* Konfigurera villkorlig [åtkomst och](../conditional-access/overview.md) [MFA](../authentication/concept-mfa-howitworks.md).
* Prova ett stegvis kodexempel: gå[AD FS azure AD-programmigreringsspelbok för utvecklare.](https://aka.ms/adfsplaybook)
