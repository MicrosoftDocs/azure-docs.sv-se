---
title: Innehållsskydd från start till slut med Azure AD
description: Den här artikeln lär dig hur du skyddar ditt innehåll med Azure Media Services och Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599493"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Självstudie: Innehållsskydd från start till slut med Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med den här självstudien och det tillhandahållna spelarexempel kan du konfigurera ett undersystem för medieinnehållsskydd från Azure Media Services (AMS) och Azure Active Directory (AAD) för att strömma medieinnehåll med alla AMS-stödda DRM/AES-128-format, strömningsprotokoll, codec och containerformat. Exemplet är tillräckligt allmänt för säker åtkomst till alla REST API som skyddas av OAuth 2 via auktoriseringskodflöde med proof key for Code Exchange (PKCE). (Azure Media Services är bara en av dem.) Det fungerar även för Microsoft Graph API eller anpassade utvecklade REST API med OAuth 2-auktoriseringskodflöde. Det här är det tillhörande dokumentet till [exempelkoden](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

I de här självstudierna får du:

> [!div class="checklist"]
>
> * Överväg autentiseringskraven
> * Förstå hur appen fungerar
> * Registrera en resursapp för backend
> * Registrera en klientapp
> * Konfigurera media services-kontots innehållsnyckelprincip och strömningsprinciper
> * Konfigurera spelarappen

Om du inte har någon prenumeration Azure Media Services du ett kostnadsfritt utvärderingskonto [för](https://azure.microsoft.com/free/) Azure och skapar sedan ett Media Services konto.

### <a name="duration"></a>Varaktighet
Självstudien bör ta ungefär två timmar att slutföra när du har den nödvändiga tekniken klar.

## <a name="prerequisites"></a>Förutsättningar

Följande senaste teknikversioner och begrepp används. Vi rekommenderar att du bekantar dig med dem innan du påbörjar den här självstudien.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper

Det är valfritt, men vi rekommenderar att du är bekant med följande begrepp innan du påbörjar den här självstudien:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* Principer för [AMS-innehållsnyckel](drm-content-key-policy-concept.md) med hjälp av AMS API v3, Azure Portal eller [amse-verktyget (Azure Media Services Explorer)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Azure AD v2-slutpunkter på [Microsoft Identity Platform](../../active-directory/develop/index.yml)
* Modern molnautentisering som [OAuth 2.0 och OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Auktoriseringskodflöde i OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) och varför PKCE behövs
  * [Delegerad behörighet jämfört med programbehörighet](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-token,](../../active-directory/develop/access-tokens.md)dess anspråk och signeringsnyckelsrover (ingår i exemplet.)

### <a name="prerequisite-code-and-installations"></a>Kravkod och installationer

* Exempelkoden. Ladda ned [exempelkoden](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* En installation av Visual Studio Code. Ladda Visual Studio kod [https://code.visualstudio.com/download](https://code.visualstudio.com/download) här.
* En installation av Node.js. Ladda Node.js här [https://nodejs.org](https://nodejs.org) . NPM levereras med installationen.
* En [Azure-prenumeration](https://azure.microsoft.com/free/).
* Ett Azure Media Services -konto (AMS).
* @azure/msal-browser v2.0, en av medlemmarna i [MICROSOFT Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK-familjen för olika klientplattformar
* Den senaste versionen av [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(ingår i exemplet.)
* POR-autentiseringsuppgifter från Apple om du vill inkludera FairPlay DRM och programcertifikatet med CORS som kan nås via JavaScript på klientsidan.

> [!IMPORTANT]
> I den här självstudien används .NET för att skapa innehållsnyckelprincipbegränsningen.  Om du inte är .NET-utvecklare men vill prova Node.js för att ansluta till Azure Media Services läser du Ansluta till [Media Services v3 API – Node.js](configure-connect-nodejs-howto.md). Det finns även en Node.js-modul som kan hantera nyckelrover automatiskt, se Node.js [passport-ad-modulen](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Överväg kraven för autentisering och auktorisering

Det finns några utmaningar när undersystemet utformas. Den har flera rörliga delar, det finns begränsningar i klientappen och den Azure AD-nyckel som sker var sjätte vecka.

Den Single-Page app (SPA) som används i den här självstudien tar hänsyn till utmaningar gällande autentiseringskrav och de begränsningar som följer. Den använder:

* Azure AD v2-slutpunkter som Azure AD-utvecklarplattform (v1-slutpunkter) ändras till Microsoft Identity Platform (v2-slutpunkter).
* Auktoriseringskodflöde eftersom det implicita beviljandeflödet i OAuth 2 har gjorts inaktuellt.
* En app som omfattas av följande begränsningar:
    * En offentlig klient kan inte dölja klienthemligheten.  Enbart auktoriseringskodflöde kräver att klienthemligheten döljs, så auktoriseringskodflödet med PKCE används.
    * En webbläsarbaserad app som omfattas av en sandbox-miljö för webbläsarsäkerhet (CORS/preflight constraint).
    * En webbläsarbaserad app med modern JavaScript som omfattas av Säkerhetsbegränsningar i JavaScript (anpassad rubriktillgänglighet, korrelations-ID).

## <a name="understand-the-subsystem-design"></a>Förstå undersystemsdesignen

Undersystemets design visas i följande diagram.  Den har tre lager:

* Back office-lager (i svart) för att konfigurera innehållsnyckelprincipen och publicera innehåll för strömning
* Offentliga slutpunkter (i blått) som är spelare/kundriktade slutpunkter som tillhandahåller autentisering, auktorisering, DRM-licens och krypterat innehåll
* Spelarapp (i ljusblått) som integrerar alla komponenter och
    * hanterar användarautentisering via Azure AD.
    * hanterar access_token från Azure AD.
    * tar emot manifest och krypterat innehåll från AMS/CDN.
    * hämtar DRM-licens från Azure Media Services.
    * hanterar innehållsdekryptering, avkodning och visning.

![skärm för parsning av JWT-token](media/aad-ams-content-protection/subsystem.svg)

Läs [Design av ett multi-DRM-innehållsskyddssystem med åtkomstkontroll](./architecture-design-multi-drm-system.md) för mer information om undersystemet.

## <a name="understand-the-single-page-app"></a>Förstå ensidesappen

Spelarappen är en ensidesapplikation (SPA) som utvecklats i Visual Studio Code med:

* Node.js med ES 6 JavaScript
* @azure/msal-browser 2.0 betaversion
* Azure Media Player SDK
* OAuth 2-flöde mot Azure AD v2-slutpunkter (Microsoft Identity Platform)

SPA-spelarappen utför följande åtgärder:

* Användarautentisering för användare som är inbyggda i klientorganisationen och gästanvändare från andra AAD-klienter eller MSA-konton. Användare kan välja att logga in via ett popup-fönster eller en omdirigering i webbläsaren (för webbläsare som inte tillåter popup-fönster som Safari).
* Förvärv av `access_token` via auktoriseringskodflöde med PKCE.
* Förnyelse av `access_token` (token som utfärdats av AAD är giltiga i 1 timme), för vilka `refresh_token` också förvärvas.
* Parsa JWT-token (både `access_token` och `id_token` ) för testning/inspektion.
* Förvärv av DRM-licenser för alla tre DRM:er eller AES-128-innehållsnyckel.
* Strömning av innehåll under olika kombinationer av DRM jämfört med Streaming Protocol jämfört med Container Format. Rätt formatsträng genereras för varje kombination.
* Dekryptering, avkodning och visning.
* Microsoft Graph API-anrop i felsökningssyfte. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Skärmen för inloggning, tokenförvärv, tokenförnyelse och tokenvisning:

 ![Skärm för inloggning, tokenförvärv, tokenförnyelse och tokenvisning](media/aad-ams-content-protection/token-acquisition.png)

Skärmen för parsning av JWT-token (access_token eller id_token):

![Skärmbild som visar parsning av J W T-token.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Skärmen för att testa skyddat innehåll med olika kombinationer av DRM/AES jämfört med direktuppspelningsprotokoll jämfört med containerformat:

![Skärmbild som visar testning av skyddat innehåll med olika kombinationer av D R M eller A E S jämfört med direktuppspelningsprotokoll jämfört med containerformat](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Välj en Azure AD-klientorganisation

> [!NOTE]
> Härifrån och framåt förutsätts det att du har loggat in på Azure Portal och har minst en Azure AD-klientorganisation.

Välj en Azure AD-klient som ska användas för vårt exempel från slutet till slut. Du kan välja mellan två alternativ:

* En befintlig Azure AD-klientorganisation. Alla Azure-prenumerationer måste ha en Azure AD-klientorganisation, men en Azure AD-klientorganisation kan användas av flera Azure-prenumerationer.
* En ny Azure AD-klientorganisation som *inte* används av någon Azure-prenumeration. Om du väljer det nya klientalternativet måste mediatjänstkontot och exempelspelarappen finnas i en Azure-prenumeration som använder en separat Azure AD-klientorganisation. Detta ger viss flexibilitet. Du kan till exempel använda din egen Azure AD-klientorganisation men även kundens mediatjänstkonto i kundens Azure-prenumeration.

## <a name="register-the-backend-resource-app"></a>Registrera backend-resursappen

1. Gå till den Azure AD-klient som du valde eller skapade.
1. Välj **Azure Active Directory** på menyn.
1. Välj **Appregistreringar** på menyn.
1. Klicka **på + Ny registrering.**
1. Ge appen namnet *LicenseDeliveryResource2* (där 2 anger AAD v2-slutpunkter).
1. Välj **Endast konton i den här organisationskatalogen ([ ditt *klientnamn*] endast – enskild klient)**. Om du vill aktivera åtkomst till flera klienter väljer du något av de andra alternativen för flera klienter.
1. **Omdirigerings-URI** är valfritt och kan ändras senare.
1. Klicka på **Registrera**. Den Appregistreringar vyn visas.
1. Välj **Manifest** på menyn. Manifestvyn visas.
1. Ändra värdet för till `accessTokenAcceptedVersion` *2* (inga citattecken).
1. Ändra värdet för till `groupMembershipClaims` *"SecurityGroup"* (med citattecken).
1. Klicka på **Spara**.
1. Välj **Exponera ett API** på menyn. Vyn Lägg till ett omfång visas. (Azure tillhandahåller en program-ID-URI, men om du vill ändra den kan du redigera i fältet Program-ID-URI.)
1. Klicka **på Spara och fortsätt**. Vyn ändras. För var och en av inställningarna i kolumnen Inställning i tabellen nedan anger du värdet i kolumnen Värde och klickar sedan på Lägg **till omfång.**

| Inställning | Värde | Beskrivning |
| ------- | ----- | ----------- |
| Namn på sökomfång | *Drm. License.Delivery* | Hur omfånget visas när åtkomst till det här API:et begärs och i åtkomsttoken när omfånget har beviljats till ett klientprogram. Detta måste vara unikt för det här programmet. En bra metod är att använda "resource.operation.constraint" som ett mönster för att generera namnet. |
| Vem kan samtycka? | *Administratörer och användare* | Avgör om användarna kan godkänna det här omfånget i kataloger där användarmedgivande har aktiverats. |
| Visningsnamn för administratörsmedgivande | *DRM-licensleverans* | Vad omfånget ska anropas på skärmen för medgivande när administratörer godkänner det här omfånget. |
| Beskrivning av administratörsmedgivande** | *Resursomfång för DRM-licensleverans i backend* | En detaljerad beskrivning av det omfång som visas när klientorganisationsadministratörer utökar ett omfång på skärmen för medgivande. |
| Visningsnamn för användarmedgivande | *Drm. License.Delivery* | Vad omfånget ska anropas på skärmen för medgivande när användare godkänner det här omfånget. |
| Beskrivning av användarmedgivande | *Resursomfång för DRM-licensleverans i backend* | Det här är en detaljerad beskrivning av det omfång som visas när användare utökar ett omfång på skärmen för medgivande. |
| Tillstånd | *Aktiverad* | Avgör om det här omfånget är tillgängligt för klienter att begära. Ange "Inaktiverad" för omfång som du inte vill ska vara synliga för klienter. Endast inaktiverade omfång kan tas bort, och vi rekommenderar att du väntar minst en vecka efter att ett omfång har inaktiverats innan du tar bort det för att se till att inga klienter fortfarande använder det. |

## <a name="register-the-client-app"></a>Registrera klientappen

1. Gå till den Azure AD-klient som du valde eller skapade.
1. Välj **Azure Active Directory** på menyn.
1. Välj **Appregistreringar** på menyn.
1. Klicka **på + Ny registrering.**
1. Ge klientappen ett namn, till exempel *AMS AAD Content Protection*.
1. Välj **Endast konton i den här organisationskatalogen ([ ditt *klientnamn*] endast – enskild klient)**. Om du vill aktivera åtkomst till flera klienter väljer du något av de andra alternativen för flera klienter.
1. **Omdirigerings-URI** är valfritt och kan ändras senare.
1. Klicka på **Registrera**.
1. Välj **API-behörigheter** på menyn.
1. Klicka på **+ Lägg till en behörighet**. Vyn Begär API-behörigheter öppnas.
1. Klicka på fliken **Mitt API** och välj appen *LicenseDeliveryResource2* som du skapade i det förra avsnittet.
1. Klicka på DRM-pilen och kontrollera *DRM. License.Delivery-behörighet.*
1. Klicka på **Lägg till behörigheter**. Vyn Lägg till behörigheter stängs.
1. Välj **Manifest** på menyn. Manifestvyn visas.
1. Leta upp och lägg till följande värdepar i `replyUrlsWithType` attributet :

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > I det här läget har du ännu inte URL:en för din spelarapp.  Om du kör appen från din localhost-webbserver kan du bara använda värdeparet localhost. När du har distribuerat spelarappen kan du lägga till posten här med den distribuerade URL:en.  Om du glömmer att göra det visas ett felmeddelande vid Azure AD-inloggningen.

1. Klicka på **Spara**.
1. Slutligen kontrollerar du att konfigurationen är korrekt genom att välja **Autentisering**.  Vyn Autentisering visas. Klientprogrammet visas som en ensidesapp (SPA), omdirigerings-URI:n visas och beviljandetypen blir Authorization Code Flow with PKCE (Auktoriseringskodflöde med PKCE).

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Konfigurera principen för Media Services-kontoinnehållsnyckel och strömningsprinciper

**Det här avsnittet förutsätter att du är .NET-utvecklare och är bekant med AMS v3 API.**

> [!NOTE]
> När detta skrivs kan du inte använda Azure Portal för principkonfigurationen för Media Services-kontonyckeln eftersom den inte stöder användning av en signeringsnyckel för asymmetrisk token med OpenID-Config.  Konfigurationen måste ha stöd för azure AD-nyckelrover eftersom den azure AD-utfärdade token signeras av en asymmetrisk nyckel och nyckeln återställs var sjätte vecka. Därför använder den här självstudien .NET och AMS v3 API.

Konfiguration av [innehållsnyckelprincipen och](drm-content-key-policy-concept.md) [strömningsprinciper för](stream-streaming-policy-concept.md) DRM och AES-128 gäller.  Ändra i `ContentKeyPolicyRestriction` innehållsnyckelprincipen.

Nedan visas .NET-koden för att skapa innehållsnyckelprincipbegränsningen.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Ändra `ida_AADOpenIdDiscoveryDocument` värdena `ida_audience` , och i `ida_issuer` koden ovan. Så här hittar du värdena för dessa objekt i Azure Portal:

1. Välj den AAD-klient som du använde **tidigare, klicka Appregistreringar** på menyn och klicka sedan på **länken Slutpunkter.**
1. Markera och kopiera värdet för **dokumentfältet OpenIdConnect-metadata** och klistra in det i koden som `ida_AADOpenIdDiscoveryDocument` värde.
1. Värdet `ida_audience` är program-ID:t (klienten) för den registrerade *appen LicenseDeliveryResource2.*
1. Värdet `ida_issuer` är URL:en `https://login.microsoftonline.com/[tenant_id]/v2.0` . Ersätt [*tenant_id*] med ditt klientorganisations-ID.

## <a name="set-up-the-sample-player-app"></a>Konfigurera exempelspelarappen

Om du inte redan har gjort det klonar eller laddar du ned appen från källdatabasen: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Du har två alternativ för att konfigurera spelarappen:

* Minimal anpassning (ersätt endast vissa konstanta strängvärden som client_id, tenant_id och strömnings-URL), men du måste använda Visual Studio Code och Node.js.
* Om du föredrar att använda en annan IDE och webbplattform som ASP.NET Core kan du placera webbsidefilerna, JavaScript-filerna och CSS-filen i projektet eftersom själva spelarappen inte använder någon kod på serversidan.

### <a name="option-1"></a>Alternativ 1

1. Starta Visual Studio Code.
1. Öppna projektet genom att klicka på File -> Open Folder -> browse to and select the parent folder of thepackage.json file (Öppna mapp -> och välj den överordnade mappen för denpackage.js *filen.*
1. Öppna JavaScript-filen *public/javascript/constants.js*.
1. Ersätt `OAUTH2_CONST.CLIENT_ID` med för din registrerade `client_id` klientapp i AAD-klientorganisationen.  Du hittar i `client_id` avsnittet Översikt för den registrerade appen i Azure Portal. Obs! Det är klient-ID, inte objekt-ID.
1. Ersätt `OAUTH2_CONST.TENANT_ID` med för din Azure `tenant_id` AD-klientorganisation. Du hittar din genom `tenant_id` att klicka på Azure Active Directory meny. Den tenant_id visas i avsnittet Översikt.
1. Ersätt `OAUTH2_CONST.SCOPE` med det omfång som du lade till i din registrerade klientapp. Du hittar omfånget genom att gå till den registrerade klientappen **på Appregistreringar** och sedan välja din klientapp:
    1. Välj din klientapp, klicka på menyn **API-behörigheter** och välj sedan omfånget *DRM. License.Delivery* under API-behörigheten *LicenseDeliveryResource2.* Behörigheten ska vara i det format som *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM. License.Delivery*. **Viktigt!** Håll utrymmet framför `offline_access` i `OAUTH2_CONST.SCOPE` .
1. Ersätt de två konstanta strängarna för `AMS_CONST` enligt nedan. Den ena är den skyddade strömnings-URL:en för din testtillgång och den andra är URL:en för PROGRAMcertifikatet om du vill inkludera FairPlay-testfallet. Annars kan du lämna det som det är för `AMS_CONST.APP_CERT_URL` . Klicka sedan på **Spara**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Så här testar du lokalt:

1. I Visual Studio Code (VSC) väljer **du Visa** på huvudmenyn och sedan **Terminal**.
1. Om du inte redan har installerat npm anger du i `npm install` kommandotolken.
1. Ange `npm start` i kommandotolken. (Om npm inte startar kan du prova att ändra katalogen till `npmweb` genom att ange i `cd npmweb` kommandotolken.)
1. Använd en webbläsare för att bläddra till `http://localhost:3000` .

Beroende på vilken webbläsare du använder väljer du rätt kombination av DRM/AES vs Streaming Protocol jämfört med Container Format för att testa efter inloggning `access_token` (förvärv). Om du testar i Safari på macOS markerar du alternativet Omdirigerings-API eftersom Safari inte tillåter popup-fönster. De flesta andra webbläsare tillåter både popup-fönster och omdirigeringsalternativ.

### <a name="option-2"></a>Alternativ 2

Om du planerar att använda en annan IDE/webbplattform och/eller en webbserver, till exempel IIS som körs på utvecklingsdatorn, kopierar du följande filer till en ny katalog i din lokala webbserver. Sökvägarna nedan är där du hittar dem i koden som du laddade ned.

* *views/index.ejs* (ändra suffixet till .html)
* *views/jwt.ejs* (ändra suffixet till .html)
* *views/info.ejs* (ändra suffix till html)
* *public/** (JavaScript-filer, CSS, bilder enligt nedan)

1. Kopiera filerna som finns i *mappen view* till roten för den nya katalogen.
1. Kopiera *mapparna* som finns i *den offentliga* mappen till roten för den nya katalogen.
1. Ändra `.ejs` filtilläggen till `.html` . (Ingen variabel på serversidan används så att du kan ändra den på ett säkert sätt.)
1. Öppna *index.htmi* VSC (eller någon annan kodredigerare) och ändra sökvägarna och så att de återspeglar var filerna `<script>` `<link>` finns.  Om du har följt de föregående stegen behöver du bara ta bort `\` i sökvägen.  Blir till exempel `<script type="text/javascript" src="/javascript/constants.js"></script>` `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Anpassa konstanterna i *javascript-/constants.jsfilen* som i alternativ 1.

## <a name="common-customer-scenarios"></a>Vanliga kundscenarier

Nu när du har slutfört självstudien och har ett fungerande undersystem kan du prova att ändra det för följande kundscenarier:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Rollbaserad åtkomstkontroll i Azure (Azure RBAC) för licensleverans via Azure AD-gruppmedlemskap

Hittills tillåter systemet alla användare som kan logga in att få en giltig licens och spela upp det skyddade innehållet.

Det är ett vanligt kundkrav att en delmängd autentiserade användare tillåts titta på innehåll medan andra inte är, till exempel en kund som erbjuder grundläggande prenumerationer och premiumprenumerationer för videoinnehåll. Deras kunder som har betalat för en grundläggande prenumeration ska inte kunna titta på innehåll som kräver en premiumprenumeration. Nedan visas de ytterligare steg som krävs för att uppfylla det här kravet:

#### <a name="set-up-the-azure-ad-tenant"></a>Konfigurera Azure AD-klientorganisationen

1. Konfigurera två konton i din klientorganisation. De kan ha *namnet premium_user* och *basic_user*;
1. Skapa en användargrupp och kalla den *PremiumGroup*.
1. Lägg till *premium_user* till *PremiumGroup* som medlem, men lägg inte *till basic_user* i gruppen.
1. Anteckna **Objekt-ID för** *PremiumGroup.*

#### <a name="set-up-the-media-services-account"></a>Konfigurera Media Services konto

Ändra (som visas i avsnittet ovan i Installation i Media Service-konto) genom att lägga till ett anspråk med namnet groups , där har `ContentKeyPolicyRestriction`  `ida_EntitledGroupObjectId` objekt-ID:t *för PremiumGroup* som värde:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*Gruppanspråk* är medlem i en [begränsad anspråksuppsättning](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) i Azure AD.

#### <a name="test"></a>Test

1. Logga in  med premium_user konto. Du bör kunna spela upp det skyddade innehållet.
1. Logga in  med basic_user konto. Du bör få ett felmeddelande som anger att videon är krypterad, men det finns ingen nyckel för att dekryptera den. Om du visar händelser, fel och nedladdningar med listrutan längst ned i diagnostiköverlägget för spelaren bör felmeddelandet indikera att licenshämtningen misslyckades på grund av det saknade anspråksvärdet för gruppanspråk i JWT som utfärdats av Slutpunkt för Azure AD-token.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Stöd för flera mediatjänstkonton (över flera prenumerationer)

En kund kan ha flera mediatjänstkonton i antingen en eller flera Azure-prenumerationer. En kund kan till exempel ha ett mediatjänstkonto som primär produktionskonto, ett annat som sekundärt/säkerhetskopieringskonto och ett annat för dev/test.

Allt du behöver göra är att se till att du använder samma uppsättning parametrar som du använde i avsnittet (installationsprogrammet i Media Service-kontot) när du skapade i alla `ContentKeyPolicyRestriction` mediatjänstkonton.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Stöd för en kund, dess leverantörer och/eller dotterbolag över flera AAD-klienter

Som användare av lösningen kan en kunds dotterbolag, leverantörer/partner finnas i olika AAD-klienter, till exempel `mycustomer.com` , `mysubsidiary.com` och `myparther.com` . Även om den här lösningen bygger på en enda specifik AAD-klient, till exempel , kan du få den att fungera `mycustomer.com` för användare från andra klienter.

Använd `mycustomer.com` för den här lösningen och lägg till en användare från som `mypartner.com` gästanvändare i `mycustomer.com` klientorganisationen. Kontrollera att `mypartner.com` användaren aktiverar gästkontot. Gästkontot kan antingen vara från en annan AAD-klient eller ett `outlook.com` konto.

Observera att gästanvändare från , efter att ha aktiverats i , fortfarande autentiseras via sin `mypartner.com` `mycustomer.com` egen/ursprungliga AAD-klient, `mypartner.com` , men utfärdas av `access_token` `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Stöd för en kundklient/-prenumeration med en konfiguration i din prenumeration/klientorganisation

Du kan använda din konfiguration för att testa skyddat innehåll i kundens mediatjänstkonto/prenumeration. Du skulle konfigurera det med en Azure AD-klientorganisation och ett mediatjänstkonto i samma prenumeration. Kundens mediatjänstkonto skulle finnas i sin Azure-prenumeration med sin egen Azure AD-klientorganisation.

1. Lägg till kundens konto i din klientorganisation som ett gästkonto.
1. Arbeta med kunden för att förbereda skyddat innehåll i kundens mediatjänstkonto genom att ange de tre parametrarna som anges i avsnittet Installation i Media Service-konto.

Kunden kan sedan bläddra till konfigurationen, logga in med gästkontot och testa sitt eget skyddade innehåll. Du kan också logga in med ditt eget konto och testa kundens innehåll.

Exempellösningen kan konfigureras i en Microsoft-klientorganisation med Microsoft-prenumeration eller anpassad klientorganisation med Microsoft-prenumeration. Azure Media Service-instansen kan komma från en annan prenumeration med dess klientorganisation.

## <a name="clean-up-resources"></a>Rensa resurser

> [!WARNING]
> Om du inte kommer att fortsätta att använda det här programmet tar du bort de resurser som du skapade när du följde den här självstudien. Annars debiteras du för dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Kryptera innehåll](drm-encrypt-content-how-to.md)
