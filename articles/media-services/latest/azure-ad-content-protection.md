---
title: Innehålls skydd från slut punkt till slut punkt med hjälp av Azure AD
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
ms.openlocfilehash: 9415d66c49992bc31f773dec908a861f1126e714
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92427202"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Självstudie: innehålls skydd från slut punkt till slut punkt med hjälp av Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med den här självstudien och det tillhandahållna Player-exemplet kan du konfigurera ett Azure Active Directory AMS-undersystem Azure Media Services från slut punkt till slut punkt till slut punkt till slut punkt för att strömma medie innehåll med alla AMS som stöder DRM/AES-128, strömmande protokoll, codec och behållar format. Exemplet är allmänt nog för säker åtkomst till alla REST API som skyddas av OAuth 2 via auktoriseringskod-flöde med bevis nyckel för Code Exchange (PKCE). (Azure Media Services licens leverans tjänst är bara en av dem.) Det fungerar också med Microsoft Graph-API eller anpassade utvecklade REST API som skyddas med OAuth 2 Authorization Code Flow. Detta är det medföljande dokumentet till [exempel koden](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

I de här självstudierna får du:

> [!div class="checklist"]
>
> * Överväg autentiseringskrav
> * Förstå hur appen fungerar
> * Registrera en resurs för Server del
> * Registrera en klient app
> * Konfigurera medie tjänst kontots innehålls nyckel princip och strömmande principer
> * Konfigurera Player-appen

Om du inte har en Azure Media Services-prenumeration skapar du ett [kostnads fritt Azure-testkonto](https://azure.microsoft.com/free/) och skapar ett Media Services-konto.

### <a name="duration"></a>Varaktighet
Kursen bör ta ungefär två timmar att slutföra efter att du har den nödvändiga tekniken redo att gå vidare.

## <a name="prerequisites"></a>Förutsättningar

Följande nya teknik versioner och koncept används. Vi rekommenderar att du bekanta dig med dem innan du påbörjar den här självstudien.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper

Det är valfritt men vi rekommenderar att du är bekant med följande begrepp innan du påbörjar den här självstudien:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* AMS- [principer för innehålls nycklar](content-key-policy-concept.md) med hjälp av AMS API v3, Azure Portal eller [Azure Media Services Explorer-verktyget (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Azure AD v2-slutpunkter på [Microsoft Identity Platform](../../active-directory/develop/index.yml)
* Modern molnbaserad autentisering som [OAuth 2,0 och OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Flödes kods flöde i OAuth 2,0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) och varför PKCE krävs
  * [Delegerad behörighet vs program behörighet](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-token](../../active-directory/develop/access-tokens.md), dess anspråk och förnyelse av signerings nyckel (ingår i exemplet.)

### <a name="prerequisite-code-and-installations"></a>Nödvändig kod och installationer

* Exempel koden. Hämta [exempel koden](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* En installation av Visual Studio Code. Hämta Visual Studio Code här [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* En installation av Node.js. Hämta Node.js här [https://nodejs.org](https://nodejs.org) . NPM levereras med installationen.
* En [Azure-prenumeration](https://azure.microsoft.com/free/).
* Ett Azure Media Services-konto (AMS).
* @azure/msal-browser v 2.0, en av medlemmarna i [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK-serien för olika klient plattformar
* Den senaste versionen av [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(ingår i exemplet.)
* Autentiseringsuppgifter för FPS från Apple om du vill inkludera FairPlay DRM och det program certifikat som är värd för CORS som är tillgängligt via Java Script på klient sidan.

> [!IMPORTANT]
> I den här självstudien används .NET för att skapa begränsningen för innehålls nyckel principen.  Om du inte är .NET-utvecklare, men vill försöka Node.js att ansluta till Azure Media Services, kan du läsa [Anslut till Media Services v3 API-Node.js](configure-connect-nodejs-howto.md). Det finns också en Node.js modul som är tillgänglig för att hantera förnyelse av nycklar automatiskt i Node.js [Passport-AD-modulen](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Beakta kraven för autentisering och auktorisering

Några utmaningar presenteras i utformningen av del systemet. Den har flera rörliga delar, det finns begränsningar för klient program och Azure AD Key förnyelse som sker var sjätte vecka.

Den Single-Page app (SPA) som används i den här självstudien tar hänsyn till krav på autentisering och de begränsningar som följer. Den använder:

* Azure AD v2-slutpunkter som Azure AD Developer Platform (v1-slutpunkter) ändras till Microsoft Identity Platform (v2-slutpunkter).
* Flöde för auktoriseringskod eftersom OAuth-flödet för implicit beviljande av OAuth 2 är föråldrat.
* En app som omfattas av följande begränsningar:
    * En offentlig klient kan inte dölja klient hemligheten.  Enbart auktoriseringskod kräver att klient hemligheten döljs, vilket innebär att kod flödet för auktorisering med PKCE används.
    * En webbläsarbaserad app som omfattas av en webbläsares säkerhets Sand låda (CORS/preflight-begränsning).
    * En webbläsarbaserad app som använder moderna JavaScript-skript som omfattas av JavaScript-säkerhetsbegränsningar (anpassad rubrik tillgänglighet, korrelations-ID).

## <a name="understand-the-subsystem-design"></a>Förstå del systemets design

Under systemets design visas i följande diagram.  Den har tre lager:

* Back-Office-lager (i svart) för att konfigurera innehålls nyckel principen och publicera innehåll för strömning
* Offentliga slut punkter (i blått) som är spelare/kund riktade slut punkter som tillhandahåller autentisering, auktorisering, DRM-Licens och krypterat innehåll
* Player-app (i ljus blått) som integrerar alla komponenter och
    * hanterar användarautentisering via Azure AD.
    * hanterar access_token förvärv från Azure AD.
    * tar emot manifest och krypterat innehåll från AMS/CDN.
    * hämtar DRM-licens från Azure Media Services.
    * hanterar dekryptering av innehåll, avkodning och visning.

![skärm för parsning av JWT-token](media/aad-ams-content-protection/subsystem.svg)

Läs [design av ett innehålls skydds system med flera DRM med åtkomst kontroll](./design-multi-drm-system-with-access-control.md) för mer information om under systemet.

## <a name="understand-the-single-page-app"></a>Förstå appen med en sida

Player-appen är ett program med en enda sida (SPA) som utvecklats i Visual Studio Code med:

* Node.js med ES 6 java script
* @azure/msal-browser 2,0 beta version
* Azure Media Player SDK
* OAuth 2-flöde mot Azure AD v2-slutpunkter (Microsoft Identity Platform)

SPA Player-appen Slutför följande åtgärder:

* Användarautentisering för användare som är interna för klienten och gäst användare från andra AAD-klienter eller MSA-konton. Användarna kan välja att logga in via webbläsarfönster eller omdirigera (för webbläsare som inte tillåter popup-fönster som Safari).
* Förvärv av `access_token` genom flöde för auktoriseringskod med PKCE.
* Förnyelse av `access_token` (token utfärdade av AAD är giltiga i 1 timme), som `refresh_token` också förvärvas.
* Parsar JWT-token (både `access_token` och `id_token` ) för test/inspektion.
* Köp av DRM-licenser för alla tre DRM: er-eller AES-128-innehålls nyckeln.
* Strömning av innehåll under olika kombinationer av DRM vs Streaming Protocol vs container format. Rätt format sträng genereras för varje kombination.
* Dekryptering, avkoda och Visa.
* Microsoft Graph API-anrop i fel söknings syfte. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Skärmen för inloggning, token-förvärv, förnyelse av token och visning av token:

 ![Skärm för inloggning, token-hämtning, token-förnyelse och token-visning](media/aad-ams-content-protection/token-acquisition.png)

Skärmen för att parsa JWT-token (access_token eller id_token):

![Skärm bild som visar parsning av J W T-token.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Skärmen för att testa skyddat innehåll med olika kombinationer av DRM/AES vs streaming-protokoll vs container format:

![Skärm bild som visar hur du testar skyddat innehåll med olika kombinationer av D R M eller E S jämfört med strömnings protokoll jämfört med behållar format](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Välj en Azure AD-klient

> [!NOTE]
> Härifrån, förutsätts det att du har loggat in på Azure Portal och har minst en Azure AD-klient.

Välj en Azure AD-klient som ska användas för vårt exempel från slut punkt till slut punkt. Du kan välja mellan två alternativ:

* En befintlig Azure AD-klient. Alla Azure-prenumerationer måste ha en Azure AD-klient, men en Azure AD-klient kan användas av flera Azure-prenumerationer.
* En ny Azure AD-klient som *inte* används av någon Azure-prenumeration. Om du väljer alternativet ny klient måste Media tjänst kontot och Sample Player-appen finnas i en Azure-prenumeration som använder en separat Azure AD-klient. Detta ger en viss flexibilitet. Du kan till exempel använda din egen Azure AD-klient, men även kundens Media tjänst konto i kundens Azure-prenumeration.

## <a name="register-the-backend-resource-app"></a>Registrera Server dels resurs appen

1. Gå till den Azure AD-klient som du valde eller skapat.
1. Välj **Azure Active Directory** på menyn.
1. Välj **Appregistreringar** på menyn.
1. Klicka på **+ ny registrering**.
1. Namnge appen *LicenseDeliveryResource2* (där 2 indikerar AAD v2-slutpunkter).
1. Välj **konton i den här organisations katalogen endast ([*ditt klient namn*] endast-en klient)**. Om du vill aktivera åtkomst till flera klienter väljer du något av de andra alternativen för flera innehavare.
1. **Omdirigerings-URI: n** är valfri och kan ändras senare.
1. Klicka på **Registrera**. Vyn Appregistreringar visas.
1. Välj **manifest** på menyn. Manifest visningen visas.
1. Ändra värdet för `accessTokenAcceptedVersion` till *2* (inga citat tecken).
1. Ändra värdet för `groupMembershipClaims` till *"SecurityGroup"* (med citat tecken).
1. Klicka på **Spara**.
1. Välj **exponera ett API** från menyn. Vyn Lägg till en omfattning visas. (Azure tillhandahåller en program-ID-URI, men om du vill ändra det kan du redigera i fältet program-ID URI.)
1. Klicka på **Spara och fortsätt**. Vyn kommer att ändras. För var och en av inställningarna i kolumnen inställning i tabellen nedan anger du värdet i kolumnen värde och klickar sedan på **Lägg till omfång**.

| Inställning | Värde | Beskrivning |
| ------- | ----- | ----------- |
| Namn på sökomfång | *Rights. Licens. Delivery* | Hur omfånget visas när åtkomst till detta API begärs och i åtkomsttoken när omfattningen har beviljats till ett klient program. Detta måste vara unikt i det här programmet. Vi rekommenderar att du använder "Resource. operation. constraint" som ett mönster för att generera namnet. |
| Vem kan godkänna? | *Administratörer och användare* | Anger om användare kan godkänna det här omfånget i kataloger där användar medgivande har Aktiver ATS. |
| Visningsnamn för administratörsmedgivande | *DRM-licensleverans* | Vad omfånget kommer att anropas på skärmen för medgivande när administratörer godkänner detta omfång. |
| Beskrivning av administratörs medgivande * * | *Resurs omfång för leverans Server del för DRM-Licens* | En detaljerad beskrivning av det omfång som visas när klient administratörer utökar ett omfång på medgivande skärmen. |
| Visningsnamn för användarmedgivande | *Rights. Licens. Delivery* | Vad scopet kommer att anropas på skärmen för medgivande när användare godkänner det här omfånget. |
| Beskrivning av användarmedgivande | *Resurs omfång för leverans Server del för DRM-Licens* | Det här är en detaljerad beskrivning av det omfång som visas när användarna utökar ett omfång på medgivande skärmen. |
| Tillstånd | *Aktiverad* | Anger om det här omfånget är tillgängligt för klienter att begära. Ange den som "inaktive rad" för omfattningar som du inte vill ska visas för klienterna. Endast inaktiverade omfattningar kan tas bort och vi rekommenderar att du väntar minst en vecka efter att ett omfång har inaktiverats innan du tar bort det för att se till att inga klienter fortfarande använder det. |

## <a name="register-the-client-app"></a>Registrera klient programmet

1. Gå till den Azure AD-klient som du valde eller skapat.
1. Välj **Azure Active Directory** på menyn.
1. Välj **Appregistreringar** på menyn.
1. Klicka på **+ ny registrering**.
1. Ge klient programmet ett namn, till exempel *AMS AAD Content Protection*.
1. Välj **konton i den här organisations katalogen endast ([*ditt klient namn*] endast-en klient)**. Om du vill aktivera åtkomst till flera klienter väljer du något av de andra alternativen för flera innehavare.
1. **Omdirigerings-URI: n** är valfri och kan ändras senare.
1. Klicka på **Registrera**.
1. Välj **API-behörigheter** på menyn.
1. Klicka på **+ Lägg till en behörighet**. Vyn begär API-behörigheter öppnas.
1. Klicka på fliken **min API** och välj den *LicenseDeliveryResource2* -app som du skapade i det sista avsnittet.
1. Klicka på DRM-pilen och kontrol lera *DRM. Licens. Delivery* -behörighet.
1. Klicka på **Lägg till behörigheter**. Vyn Lägg till behörigheter stängs.
1. Välj **manifest** på menyn. Manifest visningen visas.
1. Sök efter och Lägg till följande värdepar till `replyUrlsWithType` attributet:

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
    > Nu har du inte webb adressen för din Player-app än.  Om du kör appen från din localhost-webbserver kan du bara använda värde paret localhost. När du har distribuerat din Player-app kan du lägga till posten här med den distribuerade URL: en.  Om du glömmer att göra det visas ett fel meddelande i Azure AD-inloggningen.

1. Klicka på **Spara**.
1. Klicka slutligen på **autentisering** om du vill kontrol lera att konfigurationen är korrekt.  Vyn autentisering visas. Ditt klient program visas som en enda sida (SPA), omdirigerings-URI visas och tilldelnings typen kommer att vara auktoriseringskod med PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Konfigurera princip för Media Services-kontots innehålls nyckel och strömmande principer

**Det här avsnittet förutsätter att du är en .NET-utvecklare och är bekant med att använda AMS v3-API: et.**

> [!NOTE]
> När detta skrivs kan du inte använda Azure Portal för princip inställningen för Media Services-kontots användar nyckel eftersom den inte stöder användning av en asymmetrisk token signerings nyckel med OpenID-config.  Installationen måste ha stöd för Azure AD Key förnyelse eftersom Azure AD utfärdade token har signerats av en asymmetrisk nyckel och att nyckeln rullar under var sjätte vecka. Därför använder den här självstudien .NET och AMS v3-API: et.

Konfiguration av [innehålls nyckel principen](content-key-policy-concept.md) och [strömmande principer](streaming-policy-concept.md) för DRM och AES-128 gäller.  Ändra `ContentKeyPolicyRestriction` i innehålls nyckel principen.

Nedan visas .NET-koden för att skapa en begränsning för innehålls nyckel principen.

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

Ändra `ida_AADOpenIdDiscoveryDocument` värdena, `ida_audience` och `ida_issuer` i ovanstående kod. För att hitta värdena för dessa objekt i Azure Portal:

1. Välj den AAD-klient som du använde tidigare, klicka på **Appregistreringar** på menyn och klicka sedan på länken **slut punkter** .
1. Markera och kopiera värdet för fältet **OpenIdConnect metadata Document** och klistra in det i koden som `ida_AADOpenIdDiscoveryDocument` värde.
1. `ida_audience`Värdet är program-ID: t (Client) för den registrerade appens *LicenseDeliveryResource2*.
1. `ida_issuer`Värdet är URL: en `https://login.microsoftonline.com/[tenant_id]/v2.0` . Ersätt [*tenant_id*] med klient-ID: t.

## <a name="set-up-the-sample-player-app"></a>Konfigurera Sample Player-appen

Om du inte redan har gjort det, klona eller ladda ned appen från källan lagrings platsen: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Du har två alternativ för att ställa in Player-appen:

* Minimal anpassning (ersätter bara vissa konstanta sträng värden som client_id, tenant_id och strömmande URL), men du måste använda Visual Studio Code och Node.js.
* Om du föredrar att använda en annan IDE-och webb plattform, till exempel ASP.NET Core, kan du lagra webb sidans filer, JavaScript-filer och CSS-filen i projektet eftersom själva Player-appen inte använder någon kod på Server sidan.

### <a name="option-1"></a>Alternativ 1

1. Starta Visual Studio Code.
1. Öppna projektet genom att klicka på Arkiv > Öppna mapp-> Bläddra till och välj den överordnade mappen för *package.jsi* filen.
1. Öppna JavaScript-filen *Public/Java Script/constants.js*.
1. Ersätt `OAUTH2_CONST.CLIENT_ID` av den `client_id` registrerade klient appen i AAD-klienten.  `client_id`Avsnittet Översikt i den registrerade appen finns i Azure Portal. Obs! det är klient-ID: t, inte objekt-ID.
1. Ersätt `OAUTH2_CONST.TENANT_ID` av `tenant_id` Azure AD-klienten. Du kan hitta `tenant_id` genom att klicka på Azure Active Directory-menyn. Tenant_id kommer att visas i översikts avsnittet.
1. Ersätt `OAUTH2_CONST.SCOPE` med det omfång som du lade till i den registrerade klient appen. Du hittar omfattningen genom att gå till den registrerade klient appen från **Appregistreringar** -menyn och sedan välja din klient app:
    1. Välj din klient app, klicka på menyn **API-behörigheter** och välj sedan omfattningen *DRM. Licens. Delivery* enligt API-behörighetens *LicenseDeliveryResource2*. Behörigheten ska ha formatet som *API://df4ed433-dbf0-4da6-B328-e1fe05786db5/DRM. Licens. Delivery*. **Viktigt**: Behåll utrymmet framför `offline_access` i `OAUTH2_CONST.SCOPE` .
1. Ersätt de två konstanta strängarna `AMS_CONST` enligt vad som visas nedan. En är den skyddade direkt uppspelnings-URL: en för din test till gång och den andra är URL: en för program certifikat för FPS om du vill inkludera FairPlay-Testfallet. Annars kan du lämna den som den är för `AMS_CONST.APP_CERT_URL` . Klicka sedan på **Spara**.

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

1. I Visual Studio Code (VSC) väljer du **Visa** från huvud menyn och sedan **Terminal**.
1. Om du inte redan har installerat NPM går du till kommando tolken och anger `npm install` .
1. Ange `npm start` i kommando tolken. (Om NPM inte startar kan du försöka ändra katalogen till `npmweb` genom att ange `cd npmweb` i kommando tolken.)
1. Använd en webbläsare för att bläddra till `http://localhost:3000` .

Beroende på vilken webbläsare du använder väljer du rätt kombination av DRM/AES vs strömnings protokoll vs-behållar format för att testa efter inloggning ( `access_token` Anskaffning). Om du testar i Safari på macOS kontrollerar du alternativet omdirigera API eftersom Safari inte tillåter popup-fönster. De flesta andra webbläsare tillåter både popup-och omdirigerings alternativ.

### <a name="option-2"></a>Alternativ 2

Om du planerar att använda en annan IDE/webb plattform och/eller en webb server, till exempel IIS som körs på din utvecklings dator, kopierar du följande filer till en ny katalog i din lokala webb server. Sök vägarna nedan är där du hittar dem i den kod som du laddade ned.

* *views/index. EJS* (ändra suffix till. html)
* *views/JWT. EJS* (ändra suffix till. html)
* *vyer/info. EJS* (ändra suffix till HTML)
* *offentlig/** (JavaScript-filer, CSS, bilder som visas nedan)

1. Kopiera filerna som finns i mappen *Visa* till roten i den nya katalogen.
1. Kopiera *mapparna* som finns i den *offentliga* mappen till roten i den nya katalogen.
1. Ändra fil namns tilläggen `.ejs` till `.html` . (Ingen variabel på Server sidan används så att du kan ändra den på ett säkert sätt.)
1. Öppna *index.html* i VSC (eller annan kod redigerare) och ändra `<script>` `<link>` Sök vägarna så att de återspeglar var filerna finns.  Om du har följt de föregående stegen behöver du bara ta bort `\` i sökvägen.  Till exempel `<script type="text/javascript" src="/javascript/constants.js"></script>` blir `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Anpassa konstanterna i *Java Script/constants.js-* filen som i alternativ 1.

## <a name="common-customer-scenarios"></a>Vanliga kund scenarier

Nu när du har slutfört självstudien och har ett fungerande under system kan du prova att ändra det till följande kund scenarier:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Rollbaserad åtkomst kontroll i Azure (Azure RBAC) för licens leverans via Azure AD Group-medlemskap

I så fall tillåter systemet alla användare som kan logga in för att få en giltig licens och spela det skyddade innehållet.

Det är ett vanligt kund krav att en delmängd autentiserade användare kan se innehåll medan andra inte är, till exempel en kund som erbjuder Basic-och Premium-prenumerationer för sitt video innehåll. Deras kunder, som har betalat för en grundläggande prenumeration, bör inte kunna se innehåll som kräver en Premium-prenumeration. Nedan visas de ytterligare steg som krävs för att uppfylla detta krav:

#### <a name="set-up-the-azure-ad-tenant"></a>Konfigurera Azure AD-klienten

1. Konfigurera två konton i din klient organisation. De kan namnges *premium_user* och *basic_user*;
1. Skapa en användar grupp och anropa IT- *PremiumGroup*.
1. Lägg till *premium_user* i *PremiumGroup* som en medlem, men Lägg inte till *basic_user* i gruppen.
1. Anteckna **objekt-ID: t** för *PremiumGroup*.

#### <a name="set-up-the-media-services-account"></a>Konfigurera Media Services kontot

Ändra `ContentKeyPolicyRestriction` (se avsnittet ovan i installations programmet för Media tjänst) genom att lägga till ett anspråk med namnet *grupper*, där `ida_EntitledGroupObjectId` har objekt-ID: t för *PremiumGroup* som värde:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*Grupp* anspråk är medlem i ett [begränsat anspråk som angetts](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) i Azure AD.

#### <a name="test"></a>Test

1. Logga in med det *premium_user* kontot. Du bör kunna spela det skyddade innehållet.
1. Logga in med det *basic_user* kontot. Du får ett fel meddelande som anger att videon är krypterad, men det finns ingen nyckel för att dekryptera den. Om du visar händelserna, felen och nedladdningarna med list rutan längst ned i spelarens diagnostiska överlägg, bör fel meddelandet indikera licens hämtnings fel på grund av det saknade anspråks värdet för grupp anspråk i det JWT som utfärdats av Azure AD-token slut punkt.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Stöd för flera media service-konton (över flera prenumerationer)

En kund kan ha flera media service-konton i en eller flera Azure-prenumerationer. En kund kan till exempel ha ett medie tjänst konto som primär produktion, en annan som sekundär/säkerhets kopiering och en annan för utveckling/testning.

Allt du behöver göra är att se till att du använder samma uppsättning parametrar som du använde i avsnittet (Konfigurera i medie tjänst konto) i skapa `ContentKeyPolicyRestriction` i alla media service-konton.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Stöd för en kund, dess leverantörer och/eller dotter bolag över flera AAD-klienter

Som användare av lösningen, en kunds dotter bolag, kan leverantörer/partner finnas i olika AAD-klienter, till exempel, `mycustomer.com` `mysubsidiary.com` och `myparther.com` . Även om den här lösningen bygger på en enda viss AAD-klient, till exempel `mycustomer.com` , kan du göra så att den fungerar för användare från andra klienter.

Använd `mycustomer.com` för den här lösningen och Lägg till en användare från `mypartner.com` som gäst användare till `mycustomer.com` klienten. Se till att `mypartner.com` användaren aktiverar gäst kontot. Gäst kontot kan antingen vara från en annan AAD-klient eller ett `outlook.com` konto.

Observera att gäst användare från `mypartner.com` , efter att ha Aktiver ATS i `mycustomer.com` , fortfarande autentiseras via sin egen/ursprungliga AAD-klient, `mypartner.com` men `access_token` utfärdas av `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Stöd för en kund klient/prenumeration med en konfiguration i din prenumeration/klient

Du kan använda din installation för att testa skyddat innehåll i kundens medie tjänst konto/prenumeration. Du skulle konfigurera det med en Azure AD-klient och ett media service-konto i samma prenumeration. Kundens Media tjänst konto finns i Azure-prenumerationen med sin egen Azure AD-klient.

1. Lägg till kundens konto i din klient organisation som ett gäst konto.
1. Arbeta med kunden för att förbereda skyddat innehåll i kundens medie tjänst konto genom att ange de tre parametrarna som anges i avsnittet Installera i Media Service-kontot.

Kunden kan sedan bläddra till din installation, logga in med gäst kontot och testa sitt eget skyddade innehåll. Du kan också logga in med ditt eget konto och testa kundens innehåll.

Exempel lösningen kan konfigureras i en Microsoft-klient med Microsoft-prenumeration eller anpassad klient med Microsoft-prenumeration. Azure Media service-instansen kan vara från en annan prenumeration med klienten.

## <a name="clean-up-resources"></a>Rensa resurser

> [!WARNING]
> Om du inte kommer att fortsätta att använda det här programmet tar du bort de resurser du skapade när du följer den här kursen. Annars kommer du att debiteras för dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: Kryptera innehåll](encrypt-content-quickstart.md)
