---
title: Flytta programautentisering från AD FS till Azure Active Directory
description: Den här artikeln är avsedd att hjälpa organisationer att förstå hur de flyttar program till Azure AD, med fokus på federerade SaaS-program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27ccf5a861295ae83b5ddc021e77de75962de48
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258396"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Flytta programautentisering från Active Directory Federation Services (AD FS) till Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) erbjuder en Universal Identity-plattform som ger dina personer, partners och kunder en enda identitet för att komma åt program och samar beta från vilken plattform och enhet som helst. Azure AD har en [fullständig uppsättning funktioner för identitets hantering](../fundamentals/active-directory-whatis.md). Genom att standardisera din program-autentisering och auktorisering till Azure AD kan du använda fördelarna med dessa funktioner.

> [!TIP]
> Den här artikeln är avsedd för en utvecklare. Projektledare och administratörer som planerar ett programs flytt till Azure AD bör läsa vår [migrering av programautentisering till Azure ad](https://aka.ms/migrateapps/whitepaper) White Paper (PDF).

## <a name="introduction"></a>Introduktion

Om du har en lokal katalog som innehåller användar konton har du förmodligen många program som användarna autentiserar. Var och en av dessa appar har kon figurer ATS för användare att komma åt med sina identiteter.


Användare kan också autentisera direkt med din lokala Active Directory. Active Directory Federation Services (AD FS) (AD FS) är en standard som baseras på lokala identitets tjänster. AD FS utökar möjligheten att använda enkel inloggning (SSO) mellan betrodda affärs partner utan att kräva att användarna loggar in separat för varje program. Detta kallas för federation.

Många organisationer har SaaS (program vara som en tjänst) eller anpassade verksamhetsspecifika appar direkt till AD FS, tillsammans med Microsoft 365 och Azure AD-baserade appar.

![Program som är anslutna direkt lokalt](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**För att öka program säkerheten är målet att ha en enda uppsättning åtkomst kontroller och principer i dina lokala miljöer och moln miljöer**.

![Program som är anslutna via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typer av appar som ska migreras

Migrering av all programautentisering till Azure AD är optimal, eftersom det ger dig ett enda kontroll plan för identitets-och åtkomst hantering.

Dina program kan använda moderna eller äldre protokoll för autentisering. Överväg att först migrera program som använder moderna autentiseringsprotokoll (till exempel SAML och Open ID Connect). Dessa appar kan konfigureras om så att de autentiseras med Azure AD via antingen en inbyggd koppling i vårt app-Galleri, eller genom att registrera programmet i Azure AD. Appar som använder äldre protokoll kan integreras med [Application Proxy](./what-is-application-proxy.md).

Mer information finns i [vilka typer av program kan jag integrera med Azure AD](./what-is-application-management.md)?

Du kan använda [rapporten AD FS program aktivitet för att migrera program till Azure AD](./migrate-adfs-application-activity.md) om du har [Azure AD Connect Health aktiverat](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Migreringsprocessen

Testa appar och konfiguration på ett bra sätt under processen med att flytta din app-autentisering till Azure AD. Vi rekommenderar att du fortsätter att använda befintliga test miljöer för migrations testning som flyttas till produktions miljön. Om en test miljö inte är tillgänglig för närvarande kan du konfigurera en med [Azure App Service](https://azure.microsoft.com/services/app-service/) eller [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), beroende på programmets arkitektur.

Du kan välja att konfigurera en separat test-Azure AD-klient som ska användas när du utvecklar dina AppData.

Migreringsprocessen kan se ut så här:

**Steg 1 – aktuellt tillstånd: produktions program som autentiseras med AD FS**

![Steg 1 för migrering 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Steg 2 – valfritt: test instans av app som pekar på testa Azure-klient**

Uppdatera konfigurationen för att peka din test instans av appen till en test-Azure AD-klient och gör nödvändiga ändringar. Appen kan testas med användare i testa Azure AD-klienten. Under utvecklings processen kan du använda verktyg som [Fiddler](https://www.telerik.com/fiddler) för att jämföra och kontrol lera begär Anden och svar.

Om det inte är möjligt att konfigurera en separat test klient, hoppar du över det här steget och skapar en test instans av en app och pekar den på din Azure AD-klient för produktion enligt beskrivningen i steg 3 nedan.

![Steg 2 i migreringen ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Steg 3 – testa appen som pekar på produktion av Azure-klient**

Uppdatera konfigurationen för att peka din test instans av appen till din produktions instans av Azure. Nu kan du testa med användare i produktions instansen. Om det behövs läser du avsnittet i den här artikeln om att överföra användare.

![Migrerings steg 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Steg 4 – produktions program som pekar på produktions AD-klienten**

Uppdatera konfigurationen av ditt produktions program så att den pekar på din Azure-klient för produktion.

![Migrerings steg 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Appar som autentiseras med AD FS kan använda Active Directory grupper för behörigheter. Använd [Azure AD Connect Sync](../hybrid/how-to-connect-sync-whatis.md) för att synkronisera identitets data mellan din lokala miljö och Azure AD innan du påbörjar migreringen. Verifiera grupperna och medlemskapet före migreringen så att du kan ge åtkomst till samma användare när programmet migreras.

### <a name="line-of-business-lob-apps"></a>LOB-appar (Line of Business)

LOB-appar utvecklas internt av din organisation eller är tillgängliga som en standard paketerad produkt som installeras i ditt data Center. Exempel är appar som bygger på Windows Identity Foundation och SharePoint-appar (inte SharePoint Online).

LOB-appar som använder OAuth 2,0, OpenID Connect eller WS-Federation kan integreras med Azure AD som [app-registreringar](../develop/quickstart-register-app.md). Integrera anpassade appar som använder SAML 2,0 eller WS-Federation som [ej Galleri program](./add-application-portal.md) på sidan företags program i [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning

Appar som använder SAML 2,0 för autentisering kan konfigureras för [SAML-baserad enkel inloggning](./what-is-single-sign-on.md) (SAML-baserad SSO). Med [SAML-baserad SSO](./what-is-single-sign-on.md)kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk.

Information om hur du konfigurerar ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](./view-applications-portal.md).

![Användare av SSO SAML-användare ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Många SaaS-program har en [programspecifik självstudie](../saas-apps/tutorial-list.md) som steg för steg hjälper dig att konfigurera SAML-baserad enkel inloggning.

![själv studie kurs om appar](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Vissa appar är enkla att migrera. Andra appar med mer komplexa krav, som t.ex. anpassade anspråk, kan kräva ytterligare konfiguration i Azure AD och/eller Azure AD Connect. Information om anspråks mappningar som stöds finns i [anspråks mappning i Azure Active Directory](../develop/active-directory-claims-mapping.md).

Tänk på följande begränsningar när du mappar attribut:

* Alla attribut som kan utfärdas i AD FS visas inte i Azure AD som attribut för att generera till SAML-token, även om dessa attribut synkroniseras. När du redigerar attributet visas de olika attribut som är tillgängliga i Azure AD i list rutan värde. Kontrol lera [Azure AD Connect Sync](../hybrid/how-to-connect-sync-whatis.md) -konfigurationen för att säkerställa att ett obligatoriskt attribut, t. ex. sAMAccountName, synkroniseras till Azure AD. Du kan använda tilläggets attribut för att generera eventuella anspråk som inte ingår i standard användar schemat i Azure AD.

* I de vanligaste scenarierna behöver endast NameID-anspråk och andra vanliga anspråk för användaridentifierare anges för en app. Ta reda på om det krävs ytterligare anspråk genom att undersöka vilka anspråk du utfärdar från AD FS.

* Det är inte alla anspråk som kan vara problem eftersom vissa anspråk skyddas i Azure AD.

* Möjligheten att använda krypterade SAML-token är nu i för hands version. Mer information finns i [så här gör du: anpassa anspråk som utfärdats i SAML-token för företags program](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>SaaS-appar (program vara som en tjänst)

Om användarens inloggning till SaaS-appar, till exempel Salesforce, ServiceNow eller Workday, och är integrerade med AD FS, använder du federerad inloggning för SaaS-appar.

De flesta SaaS-program kan redan konfigureras i Azure AD. Microsoft har många förkonfigurerade anslutningar till SaaS-appar i  [Azure AD App-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), vilket gör din över gång enklare. SAML 2,0-program kan integreras med Azure AD via Azure AD App Gallery eller som [icke-galleriprogram](./add-application-portal.md).

Appar som använder OAuth 2.0 eller OpenID Connect kan integreras med Azure AD på samma sätt som [appregistreringar](../develop/quickstart-register-app.md). Appar som använder äldre protokoll kan använda [Azure-AD-programproxy](./application-proxy.md) för att autentisera med Azure AD.

Om du har problem med att registrera dina SaaS-appar kan du kontakta [SaaS Application Integration support alias](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**SAML-signerings certifikat för SSO**: signering av certifikat är en viktig del av alla SSO-distributioner. Azure AD skapar signerings certifikat för att upprätta SAML-baserade federerad enkel inloggning till dina SaaS-program. När du har lagt till Galleri eller program som inte är gallerier, konfigurerar du det tillagda programmet med alternativet federerad SSO. Se [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Appar och konfigurationer som kan flyttas idag

Appar som du kan flytta enkelt idag inkluderar SAML 2,0-appar som använder standard uppsättningen konfigurations element och anspråk:

* UPN (User Principal Name)

* E-postadress

* Tilltalsnamn

* Efternamn

* Alternativa attribut som SAML **NameID**, inklusive e-postattribut för Azure AD, e-postprefix, anställnings-ID, tilläggsattribut 1–15 eller lokalt **SamAccountName**-attribut. Mer information finns i [Redigera NameIdentifier-anspråket](../develop/active-directory-saml-claims-customization.md).

* Anpassade anspråk.

Följande kräver ytterligare konfigurations steg för att migrera till Azure AD:

* Regler för anpassad auktorisering eller Multi-Factor Authentication (MFA) i AD FS. Du konfigurerar dem med hjälp av funktionen för [villkorlig åtkomst i Azure AD](../conditional-access/overview.md) .

* Appar med flera svars-URL-slutpunkter. Du konfigurerar dem i Azure AD med hjälp av PowerShell eller i Azure Portal-gränssnittet.

* WS-Federation-appar, till exempel SharePoint-appar som kräver SAML version 1.1-tokens. Du kan konfigurera dem manuellt med PowerShell. Du kan också lägga till en förintegrerad generisk mall för SharePoint-och SAML 1,1-program från galleriet. Vi har stöd för SAML 2,0-protokollet.

* Uttryck för utfärdande av komplexa anspråk. Information om anspråks mappningar som stöds finns i:
   *  [Anspråksmappning i Azure Active Directory](../develop/active-directory-claims-mapping.md)
   * [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Appar och konfigurationer som inte stöds i Azure AD i nuläget

Appar som kräver följande funktioner kan inte migreras idag.

**Protokoll funktioner**

* Stöd för WS-Trust ActAs-mönstret

* SAML-artefaktmatchning

* Signatur verifiering av signerade SAML-begäranden Observera att signerade begär Anden accepteras, men signaturen verifieras inte.
Eftersom Azure AD bara returnerar token till förkonfigurerade slut punkter i programmet, krävs inte signaturverifiering i de flesta fall.

**Anspråk i token-funktioner**

* Anspråk från andra attributarkiv än Azure AD-katalogen, om inte data synkroniseras med Azure AD. Mer information finns i [Översikt över Azure AD Synchronization API](/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Utfärdande av katalogens attribut med flera värden. Vi kan till exempel inte utfärda ett multivärdeat-anspråk för proxy-adresser just nu.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Avbilda appinställningar från AD FS till Azure AD

Migreringen börjar med att utvärdera hur programmet är konfigurerat lokalt och mappar konfigurationen till Azure AD. AD FS och Azure AD fungerar på samma sätt, så koncepten för att konfigurera förtroende, inloggnings-och utloggnings-URL: er och identifierare gäller i båda fallen. Dokumentera AD FS konfigurations inställningar för dina program så att du enkelt kan konfigurera dem i Azure AD.

### <a name="map-app-configuration-settings"></a>Mappa appens konfigurations inställningar

I följande tabell beskrivs några av de vanligaste mappningen av inställningar mellan en AD FS förlitande parts förtroende till Azure AD Enterprise-program:

* AD FS – hitta inställningen i AD FS förtroende för förlitande part för appen. Högerklicka på den förlitande parten och välj egenskaper.

* Azure AD – inställningen konfigureras i [Azure Portal](https://portal.azure.com/) i varje programs egenskaper för enkel inloggning.

| Konfigurationsuppsättning| AD FS| Konfigurera i Azure AD| SAML-token |
| - | - | - | - |
| **Inloggnings-URL för appen** <p>URL: en som användaren kan använda för att logga in på appen i ett SP-initierat SAML-flöde (Service Provider).| Ej tillämpligt| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appens svars-URL** <p>Appens URL från identitets leverantörens (IdP) perspektiv. IdP skickar användaren och token hit när användaren har loggat in på IdP.  Detta kallas även för **slut punkt för SAML Assertion-konsumenten**.| Välj fliken **slut punkter**| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Mål element i SAML-token. Exempelvärde: `https://contoso.my.salesforce.com` |
| **Appens webbadress för utloggning** <p>Det här är URL: en till vilken "utloggnings rensning"-begär Anden skickas när en användare loggar ut från en app. IdP skickar begäran om att logga ut användaren från alla andra appar också.| Välj fliken **slut punkter**| Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Ej tillämpligt |
| **Appidentifierare** <p>Detta är app-ID: n från IdP perspektiv. Inloggnings-URL-värdet används ofta för identifieraren (men inte alltid).  Ibland anropar appen detta "entitets-ID".| Välj fliken **identifierare**|Öppna en grundläggande SAML-konfiguration från SAML-baserad inloggning| Mappar till **Audience** -ELEMENTET i SAML-token. |
| **Federationsmetadata för appen** <p>Det här är platsen för appens federationsmetadata. IdP:n använder den till att automatiskt uppdatera specifika konfigurationsinställningar, som t.ex. slutpunkter eller krypteringscertifikat.| Välj fliken **övervakning**| Ej tillämpligt. Azure AD stöder inte användning av program federationens metadata direkt. Du kan importera federationsmetadata manuellt.| Ej tillämpligt |
| **Användar identifierare/namn-ID** <p>Attribut som används för att unikt ange användarens identitet från Azure AD eller AD FS till din app.  Det här attributet är vanligt vis antingen UPN eller e-postadress för användaren.| Anspråks regler. I de flesta fall utfärdar anspråks regeln ett anspråk med en typ som slutar med NameIdentifier.| Du kan hitta identifieraren under rubriken **användarattribut och anspråk**. Som standard används UPN| Mappar till **NameID** -ELEMENTET i SAML-token. |
| **Andra anspråk** <p>Exempel på annan anspråks information som ofta skickas från IdP till appen inkluderar förnamn, efter namn, e-postadress och grupp medlemskap.| I AD FS finns detta som övriga anspråksregler hos den förlitande parten.| Du kan hitta identifieraren under rubriken **användarattribut & anspråk**. Välj **Visa** och redigera alla andra användarattribut.| Ej tillämpligt |


### <a name="map-identity-provider-idp-settings"></a>IdP-inställningar (Map Identity Provider)

Konfigurera dina program så att de pekar på Azure AD kontra AD FS för SSO. Här fokuserar vi på SaaS-appar som använder SAML-protokollet. Detta begrepp utökar dock även anpassade LOB-appar.

> [!NOTE]
> Konfigurations värden för Azure AD följer mönstret där ditt Azure-klient-ID ersätter {Tenant-ID} och program-ID ersätter {program-ID}. Du hittar den här informationen i [Azure Portal](https://portal.azure.com/) under Azure Active Directory > egenskaper:

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

När du flyttar din app-autentisering till Azure AD skapar du mappningar från befintliga säkerhets principer till motsvarande eller alternativa varianter som är tillgängliga i Azure AD. Att se till att dessa mappningar kan göras samtidigt som de säkerhets standarder som krävs av din app-ägare blir resten av programmigreringen betydligt enklare.

För varje regeltyp och dess exempel föreslår vi här hur regeln ser ut i AD FS, den AD FS regelns språk motsvarande kod och hur den här kartan i Azure AD används.

### <a name="map-authorization-rules"></a>Mappa auktoriseringsregler

Följande är exempel på typer av auktoriseringsregler i AD FS och hur du kan mappa dem till Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Exempel 1: Tillåt åtkomst till alla användare

Tillåt åtkomst till alla användare ser ut så här i AD FS:

![Skärm bild som visar dialog rutan konfigurera enskilda Sign-On med SAML.](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Detta mappar till Azure AD på något av följande sätt:

I [Azure Portal](https://portal.azure.com/):
* Alternativ 1: Ange användar tilldelning som krävs till Nej ![Redigera princip för åtkomst kontroll för SaaS-appar ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Observera att om du anger den obligatoriska användar tilldelningen till Ja måste användarna tilldelas till programmet för att få åtkomst. När det är inställt på Nej har alla användare åtkomst. Den här växeln styr inte vad som visas för användare i upplevelsen Mina appar.


* Alternativ 2: i fliken användare och grupper tilldelar du ditt program till den automatiska gruppen "alla användare". <p>
Du måste [Aktivera dynamiska grupper](../enterprise-users/groups-create-rule.md) i Azure AD-klienten för att standard gruppen alla användare ska vara tillgänglig.

   ![Mina SaaS-appar i Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exempel 2: Tillåt en grupp uttryckligen

Explicit grupp auktorisering i AD FS:


![Skärm bild som visar dialog rutan Redigera regel för anspråks regeln Tillåt domän administratörer.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Så här mappas regeln till Azure AD:

I [Azure Portal](https://portal.azure.com/)ska du först [skapa en användar grupp](../fundamentals/active-directory-groups-create-azure-portal.md) som motsvarar gruppen med användare från AD FS och sedan tilldela app-behörigheter till den gruppen:

![Lägg till tilldelning ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exempel 3: auktorisera en speciell användare

Explicit användarauktorisering i AD FS:

![Skärm bild som visar dialog rutan Redigera regel för Tillåt en specifik användar anspråks regel med en inkommande anspråks typ för primärt I/D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Så här mappas regeln till Azure AD:

I [Azure Portal](https://portal.azure.com/)lägger du till en användare till appen via fliken Lägg till tilldelning i appen som visas nedan:

![Mina SaaS-appar i Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Kart Multi-Factor Authentications regler

En lokal distribution av [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) och AD FS kommer fortfarande att fungera efter migreringen eftersom du är federerad med AD FS. Du kan dock överväga att migrera till Azures inbyggda MFA-funktioner som är knutna till Azure ADs arbets flöden för villkorlig åtkomst.

Följande är exempel på typer av MFA-regler i AD FS och hur du kan mappa dem till Azure AD baserat på olika villkor:

Inställningar för MFA-regel i AD FS:

![Skärm bilden visar villkor för Azure A D i Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exempel 1: framtvinga MFA baserat på användare/grupper

Väljaren för användare/grupper är en regel som gör att du kan genomdriva MFA på per grupp (grupp-SID) eller per användare (primär SID). Förutom tilldelningarna användare/grupper visas alla ytterligare kryss rutor AD FS i användar gränssnittet för MFA-konfiguration som ytterligare regler som utvärderas efter att användaren/grupp regeln tillämpas.


Ange MFA-regler för en användare eller grupp i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Välj **Tilldelningar**. Lägg till användare eller grupper som du vill använda MFA på.

3. Konfigurera **åtkomst kontroll** alternativen som visas nedan:

![Skärm bild som visar fönstret beviljande där du kan ge åtkomst.](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exempel 2: tillämpa MFA för oregistrerade enheter

Ange MFA-regler för oregistrerade enheter i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Ange **tilldelningarna** till **alla användare**.

3. Konfigurera **åtkomst kontroll** alternativen som visas nedan:

![Skärm bild som visar fönstret beviljande där du kan ge åtkomst och ange andra begränsningar.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


När du ställer in alternativet för flera kontroller för att kräva en av de valda kontrollerna, innebär det att om något av de villkor som anges i kryss rutan uppfylls av användaren, beviljas de åtkomst till din app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exempel 3: tillämpa MFA baserat på plats

Ange MFA-regler baserat på en användares plats i Azure AD:

1. Skapa en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. Ange **tilldelningarna** till **alla användare**.

1. [Konfigurera namngivna platser i Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) i övrigt Federation i företags nätverket är betrott.

1. Konfigurera **villkors reglerna** för att ange de platser som du vill använda MFA för.

![Skärm bild som visar fönstret platser för villkors regler.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Konfigurera **åtkomst kontroll** alternativen som visas nedan:


![Mappa principer för åtkomst kontroll](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Mappa genererar attribut som anspråks regel

Här är ett exempel på hur attribut mappas i AD FS:

![Skärm bild som visar dialog rutan Redigera regel för att generera attribut som anspråk.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Så här mappas regeln till Azure AD:

I [Azure Portal](https://portal.azure.com/)väljer du **företags program**, **enkel inloggning** och lägger till **SAML-token-attribut** som visas nedan:

![Skärm bild som visar sidan för enkel inloggning för ditt företags program.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mappa inbyggda principer för åtkomst kontroll

AD FS 2016 har flera inbyggda principer för åtkomst kontroll som du kan välja mellan:

![Azure AD inbyggd i åtkomst kontroll](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


Om du vill implementera inbyggda principer i Azure AD kan du använda en [ny princip för villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) och konfigurera åtkomst kontroller, eller så kan du använda den anpassade princip designern i AD FS 2016 för att konfigurera principer för åtkomst kontroll. Regel redigeraren har en fullständig lista över Tillåt-och utom-alternativ som kan hjälpa dig att göra alla typer av permutationer.

![Principer för åtkomst kontroll för Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



I den här tabellen har vi listat några användbara tillstånd och andra alternativ och hur de mappar till Azure AD.


| Alternativ | Så här konfigurerar du alternativet Tillåt i Azure AD?| Hur konfigurerar du förutom alternativet i Azure AD? |
| - | - | - |
| Från specifika nätverk| Mappar till [namngiven plats](../reports-monitoring/quickstart-configure-named-locations.md) i Azure AD| Använd alternativet **exkludera** för [Betrodda platser](../conditional-access/location-condition.md) |
| Från specifika grupper| [Ange en tilldelning av användare/grupper](./assign-user-or-group-access-portal.md)| Använd alternativet **exkludera** i användare och grupper |
| Från enheter med en angiven förtroende nivå| Ange detta från kontrollen enhets tillstånd under tilldelningar – > villkor| Använd alternativet **exkludera** under enhets tillstånds villkor och inkludera **alla enheter** |
| Med vissa anspråk i begäran| Det går inte att migrera den här inställningen| Det går inte att migrera den här inställningen |


Ett exempel på hur du konfigurerar alternativet exkludera för betrodda platser i Azure Portal:

![Skärm bild av mappning av principer för åtkomst kontroll](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Överföra användare från AD FS till Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synkronisera AD FS grupper i Azure AD

När du mappar auktoriseringsregler kan appar som autentiseras med AD FS använda Active Directory grupper för behörigheter. I sådana fall använder du [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) för att synkronisera grupperna med Azure AD innan du migrerar programmen. Kontrol lera att du verifierar grupperna och medlemskapet före migreringen så att du kan bevilja åtkomst till samma användare när programmet migreras.

Mer information finns i [krav för att använda Gruppattribut synkroniserade från Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="setup-user-self-provisioning"></a>Konfigurera användare själv etablering

Vissa SaaS-program stöder möjligheten att själv etablera användare när de loggar in på programmet första gången. I Azure Active Directory (Azure AD), avser termen app-etablering att automatiskt skapa användar identiteter och roller i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) som användarna behöver åtkomst till. Användare som migreras har redan ett konto i SaaS-programmet. Nya användare som läggs till efter migreringen måste tillhandahållas. Testa [SaaS app-etablering](../app-provisioning/user-provisioning.md) när programmet har migrerats.

### <a name="sync-external-users-in-azure-ad"></a>Synkronisera externa användare i Azure AD

Dina befintliga externa användare kan konfigureras på två huvudsakliga sätt i AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externa användare med ett lokalt konto i din organisation

Du kommer att fortsätta att kunna använda dessa konton på samma sätt som dina interna användar konton fungerar. Dessa externa användar konton har ett princip namn i din organisation, även om kontots e-post kan peka externt. När du fortskrider med migreringen kan du dra nytta av de fördelar som [Azure AD B2B](../external-identities/what-is-b2b.md) erbjuder genom att migrera dessa användare till att använda sin egen företags identitet när sådan identitet är tillgänglig. Detta effektiviserar inloggnings processen för dessa användare eftersom de ofta är inloggade med sin egen företags inloggning. Organisationens administration kommer också att minskat, eftersom inte längre behöver hantera konton för externa användare.

#### <a name="federated-external-identities"></a>Externa externa identiteter

Om du för närvarande federerar med en extern organisation har du några metoder att vidta:

* [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md). Du kan proaktivt skicka B2B-samarbets inbjudningar från Azure AD-administrationskonsolen till partner organisationen för enskilda medlemmar och fortsätta använda appar och till gångar som de används för.

* [Skapa ett självbetjänings arbets flöde för B2B-registrering](../external-identities/self-service-portal.md) som genererar en begäran för enskilda användare i din partner organisation med hjälp av API: et för inbjudan i B2B.

Oavsett hur dina befintliga externa användare konfigureras, har de förmodligen behörigheter som är associerade med sitt konto, antingen i grupp medlemskap eller i vissa behörigheter. Utvärdera om dessa behörigheter måste migreras eller rensas. Konton i din organisation som representerar en extern användare måste inaktive ras när användaren har migrerats till en extern identitet. Migreringsprocessen bör diskuteras med dina affärs partner, eftersom det kan uppstå avbrott i deras möjlighet att ansluta till dina resurser.

## <a name="migrate-and-test-your-apps"></a>Migrera och testa dina appar

Följ migreringsprocessen som beskrivs i den här artikeln.

Gå sedan till [Azure Portal](https://aad.portal.azure.com/) för att testa om migreringen lyckades. Följ anvisningarna nedan:
1. Välj **företags program**  >  **alla program** och hitta din app från listan.

1. Välj **Hantera**  >  **användare och grupper** för att tilldela minst en användare eller grupp till appen.

1. Välj **Hantera**  >  **villkorlig åtkomst**. Granska listan med principer och se till att du inte blockerar åtkomsten till programmet med en [princip för villkorlig åtkomst](../conditional-access/overview.md).

Beroende på hur du konfigurerar din app, kontrol lera att SSO fungerar korrekt.

| Autentiseringstyp| Testning |
| - | - |
| OAuth/OpenID Connect| Välj **företags program > behörigheter** och se till att du har samtyckt till programmet som ska användas i din organisation i användar inställningarna för din app.
‎ |
| SAML-baserad SSO| Använd knappen [testa SAML-inställningar](./debug-saml-sso-issues.md) under **enkel inloggning**.
‎ |
| Password-Based SSO| Ladda ned och installera [säkerhets inloggnings](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [tillägget](../user-help/my-apps-portal-end-user-access.md)för Mina appar. Med det här tillägget kan du starta valfri organisations molnappar som kräver att du använder en SSO-process.
‎ |
| Programproxy| Se till att din anslutning körs och är tilldelad till ditt program. Besök [fel söknings guiden för programproxy](./application-proxy-troubleshoot.md) för ytterligare hjälp.
‎ |

> [!NOTE]
> Cookies från den gamla AD FSs miljön kommer fortfarande att vara permanenta på användarens datorer. Dessa cookies kan orsaka problem med migreringen eftersom användarna kan dirigeras till den gamla AD FS inloggnings miljön jämfört med den nya Azure AD-inloggningen. Du kan behöva rensa användarens cookies manuellt eller använda ett skript. Du kan också använda System Center Configuration Manager eller en liknande plattform.

### <a name="troubleshoot"></a>Felsöka

Om det finns några fel från testet av de migrerade programmen kan fel sökningen vara det första steget innan du återgår till befintliga AD FS förlitande parter. Se [FELSÖKA SAML-baserad enkel inloggning till program i Azure Active Directory](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Rollback-migrering

Om migreringen Miss lyckas rekommenderar vi att du lämnar de befintliga förlitande parterna på AD FS-servrar och tar bort åtkomsten till de förlitande parterna. Detta möjliggör en snabb återgång vid behov under distributionen.

### <a name="end-user-communication"></a>Slut användar kommunikation

Även om fönstret planerat avbrott i sig kan vara minimalt, bör du fortfarande planera för att kommunicera dessa tidsram proaktivt till anställda samtidigt som du gör övertagandet från AD FS till Azure AD. Se till att din app-upplevelse har en feedback-knapp eller pekare till supportavdelningen för problem.

När distributionen är klar kan du skicka kommunikation som informerar användarna om distributionen och påminna dem om eventuella nya steg som de måste vidta.

* Instruera användarna att använda [Mina appar](https://myapps.microsoft.com) för att få åtkomst till alla migrerade program.

* Påminn användarna att de kan behöva uppdatera sina MFA-inställningar.

* Om Self-Service återställning av lösen ord har distribuerats kan användarna behöva uppdatera eller verifiera sina autentiseringsmetoder. Se [MFA](https://aka.ms/mfatemplates) -och [SSPR](https://aka.ms/ssprtemplates) för slut användar kommunikation.

Kommunikation till externa användare: den här användar gruppen är vanligt vis den mest kritiska i händelse av problem. Detta gäller särskilt om din säkerhets position dikterar en annan uppsättning regler för villkorlig åtkomst eller risk profiler för externa partner. Se till att externa partners är medvetna om schemat för moln migrering och har en tidsram under vilken de uppmuntras att delta i en pilot distribution som testar alla flöden som är unika för det externa samarbetet. Se slutligen till att de har ett sätt att få åtkomst till supportavdelningen vid eventuella problem.

## <a name="next-steps"></a>Nästa steg

Läs  [migrering av programautentisering till Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Konfigurera [villkorlig åtkomst](../conditional-access/overview.md) och [MFA](../authentication/concept-mfa-howitworks.md)

Prova ett stegvis kod exempel:[AD FS till Spelbok för Azure AD Application migration för utvecklare](https://aka.ms/adfsplaybook)
