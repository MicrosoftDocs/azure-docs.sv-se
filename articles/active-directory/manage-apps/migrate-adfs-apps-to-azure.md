---
title: Flytta programautentisering från AD FS till Azure Active Directory
description: Lär dig hur du använder Azure Active Directory för att ersätta Active Directory Federation Services (AD FS) (AD FS), vilket ger användare enkel inloggning till alla sina program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418465"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Flytta programautentisering från Active Directory Federation Services (AD FS) till Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) erbjuder en Universal Identity-plattform som ger dina personer, partners och kunder en enda identitet för att komma åt program och samar beta från vilken plattform och enhet som helst. Azure AD har en [fullständig uppsättning funktioner för identitets hantering](../fundamentals/active-directory-whatis.md). Att standardisera din programautentisering och auktorisering till Azure AD ger dessa fördelar.

> [!TIP]
> Den här artikeln är avsedd för en utvecklare. Projektledare och administratörer som planerar att flytta ett program till Azure AD bör läsa [migrering av programautentisering till Azure AD](migrate-application-authentication-to-azure-active-directory.md).

## <a name="azure-ad-benefits"></a>Fördelar med Azure AD

Om du har en lokal katalog som innehåller användar konton har du förmodligen många program som användarna autentiserar. Var och en av dessa appar har kon figurer ATS för användare att komma åt med sina identiteter.

Användare kan också autentisera direkt med din lokala Active Directory. Active Directory Federation Services (AD FS) (AD FS) är en standard-baserad lokal identitets tjänst. Det utökar möjligheten att använda enkel inloggning (SSO) mellan betrodda affärs partner så att användarna inte behöver logga in separat till varje program. Detta kallas federerad identitet.

Många organisationer har SaaS (program vara som en tjänst) eller anpassade verksamhetsspecifika appar direkt till AD FS, tillsammans med Microsoft 365 och Azure AD-baserade appar.

  ![Program som är anslutna direkt lokalt](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> För att öka program säkerheten är målet att ha en enda uppsättning åtkomst kontroller och principer i dina lokala miljöer och moln miljöer.

  ![Program som är anslutna via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typer av appar som ska migreras

Migrering av all programautentisering till Azure AD är optimal, eftersom det ger dig ett enda kontroll plan för identitets-och åtkomst hantering.

Dina program kan använda moderna eller äldre protokoll för autentisering. När du planerar din migrering till Azure AD bör du först migrera appar som använder moderna autentiseringsprotokoll (till exempel SAML och Open ID Connect). Dessa appar kan konfigureras om för att autentisera med Azure AD antingen via en inbyggd koppling från Azure App galleriet, eller genom att registrera programmet i Azure AD. Appar som använder äldre protokoll kan integreras med hjälp av Application Proxy.

Mer information finns i:

* [Använd Azure AD-programproxy för att publicera lokala appar för fjärran vändare](what-is-application-proxy.md).
* [Vad är programhantering?](what-is-application-management.md)
* [AD FS program aktivitets rapport för att migrera program till Azure AD](migrate-adfs-application-activity.md).
* [Övervaka AD FS att använda Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Migreringsprocessen

Testa dina appar och konfiguration under processen för att flytta din app-autentisering till Azure AD. Vi rekommenderar att du fortsätter att använda befintliga test miljöer för migrations testning när du flyttar till produktions miljön. Om en test miljö inte är tillgänglig för närvarande kan du konfigurera en med [Azure App Service](https://azure.microsoft.com/services/app-service/) eller [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), beroende på programmets arkitektur.

Du kan välja att konfigurera en separat test-Azure AD-klient där du utvecklar dina AppData.

Migreringsprocessen kan se ut så här:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Steg 1 – aktuellt tillstånd: produktions programmet autentiseras med AD FS

  ![Steg 1 för migrering 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Steg 2 – (valfritt) peka en test instans av appen till test-Azure AD-klienten

Uppdatera konfigurationen för att peka din test instans av appen till en test-Azure AD-klient och gör nödvändiga ändringar. Appen kan testas med användare i testa Azure AD-klienten. Under utvecklings processen kan du använda verktyg som [Fiddler](https://www.telerik.com/fiddler) för att jämföra och kontrol lera begär Anden och svar.

Om det inte är möjligt att konfigurera en separat test klient, hoppar du över det här steget och pekar på en test instans av appen till din Azure AD-klient för produktion enligt beskrivningen i steg 3 nedan.

  ![Steg 2 i migreringen ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Steg 3 – peka en test instans av appen till Azure AD-klienten för produktion

Uppdatera konfigurationen för att peka din test instans av appen till din produktions-Azure AD-klient. Nu kan du testa med användare i produktions klient organisationen. Om det behövs kan du läsa avsnittet i den här artikeln om att överföra användare.

  ![Migrerings steg 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Steg 4 – peka produktions programmet till Azure AD-klienten för produktion

Uppdatera konfigurationen av din webbapp så att den pekar på din produktions-Azure AD-klient.

  ![Migrerings steg 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Appar som autentiseras med AD FS kan använda Active Directory grupper för behörigheter. Använd [Azure AD Connect Sync](../hybrid/how-to-connect-sync-whatis.md) för att synkronisera identitets data mellan din lokala miljö och Azure AD innan du påbörjar migreringen. Verifiera grupperna och medlemskapet före migreringen så att du kan ge åtkomst till samma användare när programmet migreras.

### <a name="line-of-business-apps"></a>Branschspecifika appar

Verksamhetsspecifika appar är de som din organisation har utvecklat eller som är en standardiserad paketerad produkt. Exempel är appar som bygger på Windows Identity Foundation och SharePoint-appar (inte SharePoint Online).

Branschspecifika appar som använder OAuth 2,0, OpenID Connect eller WS-Federation kan integreras med Azure AD som [app-registreringar](../develop/quickstart-register-app.md). Integrera anpassade appar som använder SAML 2,0 eller WS-Federation som [ej Galleri program](add-application-portal.md) på sidan företags program i [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning

Appar som använder SAML 2,0 för autentisering kan konfigureras för [SAML-baserad enkel inloggning](what-is-single-sign-on.md) (SSO). Med SAML-baserad SSO kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk.

Om du vill konfigurera ett SaaS-program för SAML-baserad SSO, se [snabb start: Konfigurera SAML-baserad enkel inloggning](add-application-portal-setup-sso.md).

  ![Användare av SSO SAML-användare ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Många SaaS-program har en [programspecifik självstudie](../saas-apps/tutorial-list.md) som vägleder dig genom konfigurationen för SAML-baserad SSO.

  ![själv studie kurs om appar](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Vissa appar är enkla att migrera. Appar med mer komplexa krav, till exempel anpassade anspråk, kan kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect. Information om anspråks mappningar som stöds finns i [så här gör du: anpassa anspråk som skickas i token för en angiven app i en klient (för hands version)](../develop/active-directory-claims-mapping.md).

Tänk på följande begränsningar när du mappar attribut:

* Alla attribut som kan utfärdas i AD FS visas inte i Azure AD som attribut för att generera till SAML-token, även om dessa attribut synkroniseras. När du redigerar attributet visas de olika attribut som är tillgängliga i Azure AD i list rutan **värde** . Kontrol lera [Azure AD Connect synkronisera ämnen](../hybrid/how-to-connect-sync-whatis.md) -konfigurationen för att säkerställa att ett attribut som krävs, till exempel **sAMAccountName**, synkroniseras till Azure AD. Du kan använda tilläggets attribut för att generera eventuella anspråk som inte ingår i standard användar schemat i Azure AD.
* I de vanligaste scenarierna behöver endast **NameID**-anspråk och andra vanliga anspråk för användaridentifierare anges för en app. Ta reda på om det krävs ytterligare anspråk genom att undersöka vilka anspråk du utfärdar från AD FS.
* Det går inte att utfärda alla anspråk eftersom vissa anspråk skyddas i Azure AD.
* Möjligheten att använda krypterade SAML-token är nu i för hands version. Mer information finns i [så här gör du: anpassa anspråk som utfärdats i SAML-token för företags program](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>SaaS-appar (program vara som en tjänst)

Om dina användare loggar in på SaaS-appar, till exempel Salesforce, ServiceNow eller Workday, och är integrerade med AD FS, använder du federerad inloggning för SaaS-appar.

De flesta SaaS-program kan konfigureras i Azure AD. Microsoft har många förkonfigurerade anslutningar till SaaS-appar i  [Azure AD App-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), vilket gör att över gången blir enklare. SAML 2,0-program kan integreras med Azure AD via Azure AD App Gallery eller som [icke-galleriprogram](add-application-portal.md).

Appar som använder OAuth 2,0 eller OpenID Connect kan integreras på samma sätt med Azure AD som [app-registreringar](../develop/quickstart-register-app.md). Appar som använder äldre protokoll kan använda [Azure-AD-programproxy](application-proxy.md) för att autentisera med Azure AD.

Om du har problem med att registrera dina SaaS-appar kan du kontakta [SaaS Application Integration support alias](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>SAML-signerings certifikat för SSO

Signering av certifikat är en viktig del av alla SSO-distributioner. Azure AD skapar signerings certifikat för att upprätta SAML-baserade federerad enkel inloggning till dina SaaS-program. När du har lagt till Galleri eller program som inte är gallerier, konfigurerar du det tillagda programmet med alternativet federerad SSO. Se [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>Kryptering av SAML-token

Både AD FS och Azure AD tillhandahåller token-kryptering – möjligheten att kryptera SAML-säkerhetskontroller som går till program. Intygen krypteras med en offentlig nyckel och dekrypteras av det mottagande programmet med den matchande privata nyckeln. När du konfigurerar token-kryptering laddar du upp filer för X. 509-certifikat för att tillhandahålla offentliga nycklar.

Information om kryptering av Azure AD SAML-token och hur du konfigurerar den finns i [How to: Configure Azure AD SAML token Encryption](howto-saml-token-encryption.md).  

> [!NOTE]
> Token Encryption är en Azure Active Directory (Azure AD) Premium-funktion. Mer information om Azure AD-utgåvor, funktioner och priser finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Appar och konfigurationer som kan flyttas idag

Appar som du kan flytta enkelt idag inkluderar SAML 2,0-appar som använder standard uppsättningen med konfigurations element och anspråk. Dessa standard objekt är:

* UPN (User Principal Name)
* E-postadress
* Tilltalsnamn
* Efternamn
* Alternativa attribut som SAML **NameID**, inklusive e-postattribut för Azure AD, e-postprefix, anställnings-ID, tilläggsattribut 1–15 eller lokalt **SamAccountName**-attribut. Mer information finns i [Redigera NameIdentifier-anspråket](../develop/active-directory-saml-claims-customization.md).
* Anpassade anspråk.

Följande kräver ytterligare konfigurations steg för att migrera till Azure AD:

* Anpassad auktorisering eller Multi-Factor Authentication (MFA) regler i AD FS. Du konfigurerar dem med hjälp av funktionen för [villkorlig åtkomst i Azure AD](../conditional-access/overview.md) .
* Appar med flera svars-URL-slutpunkter. Du konfigurerar dem i Azure AD med hjälp av PowerShell eller Azure Portal-gränssnittet.
* WS-Federation-appar, till exempel SharePoint-appar som kräver SAML version 1.1-tokens. Du kan konfigurera dem manuellt med PowerShell. Du kan också lägga till en förintegrerad generisk mall för SharePoint-och SAML 1,1-program från galleriet. Vi har stöd för SAML 2,0-protokollet.
* Uttryck för utfärdande av komplexa anspråk. Information om anspråks mappningar som stöds finns i:
   *  [Anspråks mappning i Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Appar och konfigurationer som inte stöds i Azure AD i nuläget

Appar som kräver vissa funktioner kan inte migreras idag.

#### <a name="protocol-capabilities"></a>Protokoll funktioner

Appar som kräver följande protokoll funktioner kan inte migreras idag:

* Stöd för WS-Trust ActAs-mönstret
* SAML-artefaktmatchning
* Signaturverifiering för signerade SAML-begäranden
  > [!Note]
  > Signerade begär Anden accepteras, men signaturen är inte verifierad.

  Eftersom Azure AD bara returnerar token till förkonfigurerade slut punkter i programmet, krävs det förmodligen inte signaturverifiering i de flesta fall.

#### <a name="claims-in-token-capabilities"></a>Anspråk i token-funktioner

Appar som kräver följande anspråk i token-funktioner kan inte migreras idag.

* Anspråk från andra attributarkiv än Azure AD-katalogen, om inte data synkroniseras med Azure AD. Mer information finns i [Översikt över Azure AD Synchronization API](/graph/api/resources/synchronization-overview?view=graph-rest-beta).
* Utfärdande av katalogens attribut med flera värden. Vi kan till exempel inte utfärda ett multivärdeat-anspråk för proxy-adresser just nu.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Avbilda appinställningar från AD FS till Azure AD

Migreringen kräver att du bedömer hur programmet är konfigurerat lokalt och sedan mappar konfigurationen till Azure AD. AD FS och Azure AD fungerar på samma sätt, så koncepten för att konfigurera webbadresser för förtroende, in- och utloggning samt identifierare gäller i båda fallen. Dokumentera AD FS konfigurations inställningar för dina program så att du enkelt kan konfigurera dem i Azure AD.

### <a name="map-app-configuration-settings"></a>Mappa appens konfigurations inställningar

I följande tabell beskrivs några av de vanligaste mappningen av inställningar mellan en AD FS förlitande parts förtroende till Azure AD Enterprise-program:

* AD FS – hitta inställningen i AD FS förtroende för förlitande part för appen. Högerklicka på den förlitande parten och välj egenskaper.
* Azure AD – inställningen konfigureras i [Azure Portal](https://portal.azure.com/) i varje PROGRAMs SSO-egenskaper.

| Konfigurationsuppsättning| AD FS| Konfigurera i Azure AD| SAML-token |
| - | - | - | - |
| **Inloggnings-URL för appen** <p>URL: en för användaren att logga in i appen i ett SAML-flöde som initieras av en tjänst leverantör (SP).| Ej tillämpligt| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appens svars-URL** <p>Appens URL från identitets leverantörens perspektiv (IdP). IdP skickar användaren och token hit när användaren har loggat in på IdP.  Detta kallas även för **slut punkt för SAML Assertion-konsumenten**.| Välj fliken **slut punkter**| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Mål element i SAML-token. Exempelvärde: `https://contoso.my.salesforce.com` |
| **Appens webbadress för utloggning** <p>Detta är URL: en som utloggnings förfrågningar skickas till när en användare loggar ut från en app. IdP skickar begäran om att logga ut användaren från alla andra appar också.| Välj fliken **slut punkter**| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appidentifierare** <p>Detta är app-ID: n från IdP perspektiv. Inloggningens URL-värde används ofta för identifierare (men inte alltid).  Ibland anropar appen detta "entitets-ID".| Välj fliken **identifierare**|Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Mappar till **Audience** -ELEMENTET i SAML-token. |
| **Federationsmetadata för appen** <p>Det här är platsen för appens federationsmetadata. IdP:n använder den till att automatiskt uppdatera specifika konfigurationsinställningar, som t.ex. slutpunkter eller krypteringscertifikat.| Välj fliken **övervakning**| Ej tillämpligt. Azure AD stöder inte användning av program federationens metadata direkt. Du kan importera federationsmetadata manuellt.| Ej tillämpligt |
| **Användar identifierare/namn-ID** <p>Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.  Det här attributet är vanligt vis antingen UPN eller e-postadress för användaren.| Anspråks regler. I de flesta fall utfärdar anspråks regeln ett anspråk med en typ som slutar med **NameIdentifier**.| Du kan hitta identifieraren under rubriken **användarattribut och anspråk**. Som standard används UPN| Mappar till **NameID** -ELEMENTET i SAML-token. |
| **Andra anspråk** <p>Exempel på annan anspråks information som ofta skickas från IdP till appen inkluderar förnamn, efter namn, e-postadress och grupp medlemskap.| I AD FS finns detta som övriga anspråksregler hos den förlitande parten.| Du kan hitta identifieraren under rubriken **användarattribut & anspråk**. Välj **Visa** och redigera alla andra användarattribut.| Ej tillämpligt |

### <a name="map-identity-provider-idp-settings"></a>IdP-inställningar (Map Identity Provider)

Konfigurera dina program så att de pekar på Azure AD kontra AD FS för SSO. Här fokuserar vi på SaaS-appar som använder SAML-protokollet. Detta begrepp utökar dock även anpassade branschspecifika appar.

> [!NOTE]
> Konfigurations värden för Azure AD följer mönstret där ditt Azure-klient-ID ersätter {Tenant-ID} och program-ID ersätter {program-ID}. Du hittar den här informationen i [Azure Portal](https://portal.azure.com/) under **Azure Active Directory > egenskaper**:

* Välj katalog-ID för att se ditt klient-ID.
* Välj program-ID för att se ditt program-ID.

 På en hög nivå mappar du följande viktiga konfigurations element för SaaS-appar till Azure AD.

| Element| Konfigurationsvärde |
| - | - |
| Utfärdare av identitets leverantör| https: \/ /STS.Windows.net/{Tenant-ID}/ |
| Inloggnings-URL för identitetsprovider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Utloggnings-URL för identitets leverantör| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Plats för federationsmetadata| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Mappa SSO-inställningar för SaaS-appar

SaaS-appar behöver veta vart du ska skicka autentiseringsbegäranden och hur du validerar mottagna token. I följande tabell beskrivs elementen för att konfigurera SSO-inställningar i appen och deras värden eller platser inom AD FS och Azure AD

| Konfigurationsuppsättning| AD FS| Konfigurera i Azure AD |
| - | - | - |
| **IdP-inloggnings-URL** <p>Inloggnings-URL för IdP från appens perspektiv (där användaren omdirigeras för inloggning).| Den AD FS inloggnings-URL: en är AD FS Federations tjänstens namn följt av "/adfs/ls/." <p>Exempelvis: `https://fs.contoso.com/adfs/ls/`| Ersätt {Tenant-ID} med klient-ID: t. <p> För appar som använder SAML-P-protokollet: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>För appar som använder WS-Federation-protokollet: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP-utloggnings-URL**<p>Utloggnings-URL för IdP från appens perspektiv (där användaren omdirigeras när de väljer att logga ut från appen).| Den utloggnings-URL: en är antingen samma som inloggnings-URL: en, eller samma URL med "WA = wsignout 1.0". Exempelvis: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Ersätt {Tenant-ID} med klient-ID: t.<p>För appar som använder SAML-P-protokollet:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> För appar som använder WS-Federation-protokollet: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Token signerings certifikat**<p>IdP använder certifikatets privata nyckel för att signera utfärdade token. Den kontrollerar att token kom från samma IdP som appen är konfigurerad att ha förtroende för.| AD FS-certifikatet för tokensignering finns i AD FS-hanteringen under **Certifikat**.| Hitta den i Azure Portal i programmets **Egenskaper för enkel inloggning** under rubriken **SAML-signeringscertifikat**. Därifrån kan du ladda ner certifikatet för uppladdning till appen.  <p>Om programmet har fler än ett certifikat kan du hitta alla certifikat i XML-filen för federationsmetadata. |
| **Identifierare/"utfärdare"**<p>Identifierare för IdP från appens perspektiv (kallas ibland för "Issuer ID").<p>I SAML-token visas värdet som Issuer-element.| Identifieraren för AD FS är vanligt vis Federations tjänst identifieraren i AD FS hantering under **tjänst > redigera federationstjänst egenskaper**. Exempelvis: `http://fs.contoso.com/adfs/services/trust`| Ersätt {Tenant-ID} med klient-ID: t.<p>https: \/ /STS.Windows.net/{Tenant-ID}/ |
| **IdP Federation-Metadata**<p>Platsen för IdP: s allmänt tillgängliga federationsmetadata. (Federationsmetadata används av vissa appar som ett alternativ för administratören och konfigurerar URL:er, identifierare och certifikat för tokensignering individuellt.)| Hitta URL: en för AD FS federationens metadata i AD FS hantering under **tjänst > slut punkter > metadata > typ: federationsmetadata**. Exempelvis: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Motsvarande värde för Azure AD följer mönstret [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Ersätt {TenantDomainName} med klient organisationens namn i formatet "contoso.onmicrosoft.com".   <p>Mer information finns i [Federationsmetadata](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Visa AD FS säkerhets principer i Azure AD

När du flyttar din app-autentisering till Azure AD skapar du mappningar från befintliga säkerhets principer till motsvarande eller alternativa varianter som är tillgängliga i Azure AD. Att se till att dessa mappningar kan göras samtidigt som de säkerhets standarder som krävs av dina app-ägare gör resten av app-migreringen betydligt enklare.

För varje regel exempel visar vi hur regeln ser ut i AD FS, den AD FS regelns språkmotsvarighets kod och hur det mappas till Azure AD.

### <a name="map-authorization-rules"></a>Mappa auktoriseringsregler

Följande är exempel på olika typer av auktoriseringsregler i AD FS och hur du mappar dem till Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Exempel 1: Tillåt åtkomst till alla användare

Tillåt åtkomst till alla användare i AD FS:

  ![Skärm bild som visar dialog rutan konfigurera enskilda Sign-On med SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Detta mappar till Azure AD på något av följande sätt:

1. Ange **användar tilldelning krävs** till **Nej**.

    ![Redigera princip för åtkomst kontroll för SaaS-appar ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Att ange **användar tilldelning krävs** till **Ja** kräver att användare tilldelas till programmet för att få åtkomst. När det är inställt på **Nej** har alla användare åtkomst. Den här växeln styr inte vad användarna ser i erfarenheten av **Mina appar** .

1. På **fliken användare och grupper** tilldelar du ditt program till gruppen **alla användare** automatiskt. Du måste [Aktivera dynamiska grupper](../enterprise-users/groups-create-rule.md) i Azure AD-klienten för att standard gruppen **alla användare** ska vara tillgänglig.

    ![Mina SaaS-appar i Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Exempel 2: Tillåt en grupp uttryckligen

Explicit grupp auktorisering i AD FS:

  ![Skärm bild som visar dialog rutan Redigera regel för anspråks regeln Tillåt domän administratörer.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Så här mappar du den här regeln till Azure AD:

1. I [Azure Portal](https://portal.azure.com/)skapar du [en användar grupp](../fundamentals/active-directory-groups-create-azure-portal.md) som motsvarar gruppen med användare från AD FS.
1. Tilldela app-behörigheter till gruppen:

    ![Lägg till tilldelning ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Exempel 3: auktorisera en speciell användare

Explicit användarauktorisering i AD FS:

  ![Skärm bild som visar dialog rutan Redigera regel för Tillåt en specifik användar anspråks regel med en inkommande anspråks typ för primärt I/D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Så här mappar du den här regeln till Azure AD:

* I [Azure Portal](https://portal.azure.com/)lägger du till en användare till appen via fliken Lägg till tilldelning i appen som visas nedan:

    ![Mina SaaS-appar i Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Mappa Multi-Factor Authentication-regler

En lokal distribution av [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) och AD FS fungerar fortfarande efter migreringen eftersom du är federerad med AD FS. Du kan dock överväga att migrera till Azures inbyggda MFA-funktioner som är knutna till Azure ADs arbets flöden för villkorlig åtkomst.

Följande är exempel på typer av MFA-regler i AD FS och hur du kan mappa dem till Azure AD baserat på olika villkor.

Inställningar för MFA-regel i AD FS:

  ![Skärm bilden visar villkor för Azure A D i Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exempel 1: framtvinga MFA baserat på användare/grupper

Väljaren användare/grupper är en regel som gör att du kan genomdriva MFA på per grupp (grupp-SID) eller per användare (primär-SID). Förutom tilldelningarna användare/grupper visas alla ytterligare kryss rutor i gränssnittet AD FS MFA-konfiguration som ytterligare regler som utvärderas efter att regeln användare/grupper tillämpas.

Ange MFA-regler för en användare eller grupp i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Välj **Tilldelningar**. Lägg till användare eller grupper som du vill använda MFA för.
1. Konfigurera **åtkomst kontroll** alternativen som visas nedan:

    ‎![Skärm bild som visar fönstret beviljande där du kan ge åtkomst.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exempel 2: tillämpa MFA för oregistrerade enheter

Ange MFA-regler för oregistrerade enheter i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Ange **tilldelningarna** till **alla användare**.
1. Konfigurera **åtkomst kontroll** alternativen som visas nedan:

    ![Skärm bild som visar fönstret beviljande där du kan ge åtkomst och ange andra begränsningar.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

När du ställer in alternativet **för flera kontroller** för att **kräva en av de valda kontrollerna**, innebär det att om något av villkoren som anges i kryss rutan uppfylls av användaren, beviljas användaren åtkomst till din app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exempel 3: tillämpa MFA baserat på plats

Ange MFA-regler baserat på en användares plats i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Ange **tilldelningarna** till **alla användare**.
1. [Konfigurera namngivna platser i Azure AD](../reports-monitoring/quickstart-configure-named-locations.md). Annars är federationen inifrån företags nätverket betrott.
1. Konfigurera **villkors reglerna** för att ange de platser som du vill använda MFA för.

    ![Skärm bild som visar fönstret platser för villkors regler.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Konfigurera **åtkomst kontroll** alternativen som visas nedan:

    ![Mappa principer för åtkomst kontroll](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Mappa genererar attribut som anspråks regel

Genererar attribut som anspråks regel i AD FS:

  ![Skärm bild som visar dialog rutan Redigera regel för att generera attribut som anspråk.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Så här mappar du regeln till Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **företags program** och sedan **enkel inloggning** för att visa den SAML-baserade inloggnings konfigurationen:

    ![Skärm bild som visar sidan för enkel inloggning för ditt företags program.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Välj **Redigera** (markerat) om du vill ändra attributen:

    ![Detta är sidan för att redigera användarattribut och anspråk](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Mappa inbyggda principer för åtkomst kontroll

Inbyggda principer för åtkomst kontroll i AD FS 2016:

  ![Azure AD inbyggd i åtkomst kontroll](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Om du vill implementera inbyggda principer i Azure AD använder du en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) och konfigurerar åtkomst kontroller eller använder den anpassade princip designern i AD FS 2016 för att konfigurera principer för åtkomst kontroll. Regel redigeraren har en fullständig lista över Tillåt-och utom-alternativ som kan hjälpa dig att göra alla typer av permutationer.

  ![Principer för åtkomst kontroll för Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

I den här tabellen har vi listat några användbara tillstånd och andra alternativ och hur de mappar till Azure AD.

| Alternativ | Så här konfigurerar du alternativet Tillåt i Azure AD?| Hur konfigurerar du förutom alternativet i Azure AD? |
| - | - | - |
| Från specifika nätverk| Mappar till [namngiven plats](../reports-monitoring/quickstart-configure-named-locations.md) i Azure AD| Använd alternativet **exkludera** för [Betrodda platser](../conditional-access/location-condition.md) |
| Från specifika grupper| [Ange en tilldelning av användare/grupper](assign-user-or-group-access-portal.md)| Använd alternativet **exkludera** i användare och grupper |
| Från enheter med en angiven förtroende nivå| Ange detta från **enhets tillstånds** kontrollen under tilldelningar – > villkor| Använd alternativet **exkludera** under enhets tillstånds villkor och inkludera **alla enheter** |
| Med vissa anspråk i begäran| Det går inte att migrera den här inställningen| Det går inte att migrera den här inställningen |

Här är ett exempel på hur du konfigurerar alternativet exkludera för betrodda platser i Azure Portal:

  ![Skärm bild av mappning av principer för åtkomst kontroll](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Överföra användare från AD FS till Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synkronisera AD FS grupper i Azure AD

När du mappar auktoriseringsregler kan appar som autentiseras med AD FS använda Active Directory grupper för behörigheter. I sådana fall använder du [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) för att synkronisera grupperna med Azure AD innan du migrerar programmen. Kontrol lera att du verifierar grupperna och medlemskapet före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

Mer information finns i [krav för att använda Gruppattribut synkroniserade från Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="set-up-user-self-provisioning"></a>Konfigurera själv etablering av användare

Vissa SaaS-program stöder möjligheten att själv etablera användare när de loggar in på programmet första gången. I Azure AD, avser app-etablering att automatiskt skapa användar identiteter och roller i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) som användare behöver åtkomst till. Användare som migreras har redan ett konto i SaaS-programmet. Nya användare som lagts till efter migreringen måste tillhandahållas. Testa [SaaS app-etablering](../app-provisioning/user-provisioning.md) när programmet har migrerats.

### <a name="sync-external-users-in-azure-ad"></a>Synkronisera externa användare i Azure AD

Dina befintliga externa användare kan konfigureras på följande två sätt i AD FS:

* **Externa användare med ett lokalt konto i din organisation**– du fortsätter att använda dessa konton på samma sätt som dina interna användar konton fungerar. Dessa externa användar konton har ett princip namn i din organisation, även om kontots e-post kan peka externt. När du fortskrider med migreringen kan du dra nytta av de fördelar som [Azure AD B2B](../external-identities/what-is-b2b.md) erbjuder genom att migrera dessa användare till att använda sin egen företags identitet när sådan identitet är tillgänglig. Detta effektiviserar processen med att logga in för dessa användare eftersom de ofta är inloggade med sin egen företags inloggning. Organisationens administration är enklare och behöver inte hantera konton för externa användare.
* **Externa externa identiteter**– om du för närvarande federerar med en extern organisation har du några metoder att vidta:
  * [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md). Du kan proaktivt skicka B2B-samarbets inbjudningar från Azure AD-administrationskonsolen till partner organisationen för enskilda medlemmar för att fortsätta använda appar och till gångar som de används för.
  * [Skapa ett självbetjänings arbets flöde för B2B-registrering](../external-identities/self-service-portal.md) som genererar en begäran för enskilda användare i din partner organisation med hjälp av API: et för inbjudan i B2B.

Oavsett hur dina befintliga externa användare konfigureras, har de förmodligen behörigheter som är associerade med sitt konto, antingen i grupp medlemskap eller i vissa behörigheter. Utvärdera om dessa behörigheter måste migreras eller rensas. Konton i din organisation som representerar en extern användare måste inaktive ras när användaren har migrerats till en extern identitet. Migreringsprocessen bör diskuteras med dina affärs partner, eftersom det kan uppstå avbrott i deras möjlighet att ansluta till dina resurser.

## <a name="migrate-and-test-your-apps"></a>Migrera och testa dina appar

Följ migreringsprocessen som beskrivs i den här artikeln. Gå sedan till [Azure Portal](https://aad.portal.azure.com/) för att testa om migreringen lyckades.

Följ dessa anvisningar:

1. Välj **företags program**  >  **alla program** och hitta din app från listan.
1. Välj **Hantera**  >  **användare och grupper** för att tilldela minst en användare eller grupp till appen.
1. Välj **Hantera**  >  **villkorlig åtkomst**. Granska listan med principer och se till att du inte blockerar åtkomsten till programmet med en [princip för villkorlig åtkomst](../conditional-access/overview.md).

Beroende på hur du konfigurerar din app, kontrol lera att SSO fungerar korrekt.

| Autentiseringstyp| Testning |
| :- | :- |
| OAuth/OpenID Connect| Välj **företags program > behörigheter** och se till att du har samtyckt till programmet i användar inställningarna för din app.|
| SAML-baserad SSO | Använd knappen [testa SAML-inställningar](debug-saml-sso-issues.md) under **enkel inloggning**. |
| Password-Based SSO |  Ladda ned och installera [säkerhets inloggnings](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [tillägget](../user-help/my-apps-portal-end-user-access.md)för Mina appar. Med det här tillägget kan du starta valfri organisations molnappar som kräver att du använder en SSO-process. |
| Programproxy | Se till att din anslutning körs och är tilldelad till ditt program. Besök [fel söknings guiden för programproxy](application-proxy-troubleshoot.md) för ytterligare hjälp. |

> [!NOTE]
> Cookies från den gamla AD FSs miljön finns kvar på användarens datorer. Dessa cookies kan orsaka problem med migreringen, eftersom användarna kan dirigeras till den gamla AD FS inloggnings miljön jämfört med den nya Azure AD-inloggningen. Du kan behöva rensa användarens cookies manuellt eller använda ett skript. Du kan också använda System Center Configuration Manager eller en liknande plattform.

### <a name="troubleshoot"></a>Felsöka

Om det finns några fel från testet av de migrerade programmen kan fel sökningen vara det första steget innan du återgår till befintliga AD FS förlitande parter. Se [FELSÖKA SAML-baserad enkel inloggning till program i Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Rollback-migrering

Om migreringen Miss lyckas rekommenderar vi att du lämnar de befintliga förlitande parterna på AD FS-servrar och tar bort åtkomsten till de förlitande parterna. Detta möjliggör en snabb återgång vid behov under distributionen.

### <a name="employee-communication"></a>Medarbetare-kommunikation

Även om fönstret för planerat avbrott kan vara minimalt, bör du fortfarande planera för att kommunicera dessa tidsram proaktivt till anställda när du växlar från AD FS till Azure AD. Se till att din app-upplevelse har en feedback-knapp eller pekare till supportavdelningen för problem.

När distributionen är klar kan du informera användarna om distributionen och påminna dem om de åtgärder de behöver vidta.

* Instruera användarna att använda [Mina appar](https://myapps.microsoft.com) för att få åtkomst till alla migrerade program.
* Påminn användarna att de kan behöva uppdatera sina MFA-inställningar.
* Om Self-Service återställning av lösen ord har distribuerats kan användarna behöva uppdatera eller verifiera sina autentiseringsmetoder. Se [MFA](https://aka.ms/mfatemplates) -och [SSPR](https://aka.ms/ssprtemplates) för slut användar kommunikation.

### <a name="external-user-communication"></a>Extern användar kommunikation

Den här användar gruppen är vanligt vis den mest kritiska i händelse av problem. Detta gäller särskilt om din säkerhets position dikterar en annan uppsättning regler för villkorlig åtkomst eller risk profiler för externa partner. Se till att externa partners är medvetna om schemat för moln migrering och har en tidsram under vilken de uppmuntras att delta i en pilot distribution som testar alla flöden som är unika för det externa samarbetet. Se slutligen till att de har ett sätt att komma åt supportavdelningen om det är problem.

## <a name="next-steps"></a>Nästa steg

* Läs  [migrering av programautentisering till Azure AD](https://aka.ms/migrateapps/whitepaper).
* Konfigurera [villkorlig åtkomst](../conditional-access/overview.md) och [MFA](../authentication/concept-mfa-howitworks.md).
* Prova ett stegvis kod exempel:[AD FS till Spelbok för Azure AD Application migration för utvecklare](https://aka.ms/adfsplaybook).
