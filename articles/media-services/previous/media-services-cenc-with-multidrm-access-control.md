---
title: Design av ett innehålls skydds system med åtkomst kontroll med Azure Media Services | Microsoft Docs
description: Lär dig mer om hur du licensierar Microsoft Smooth Streaming client porting kit.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a4f151b597b57b77fa6517c6ea0d586c1106986
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017145"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Design av ett innehålls skydds system med åtkomst kontroll med Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

Att utforma och skapa ett Digital Rights Management-undersystem (DRM) för en överordnad (OTT) eller online streaming-lösning är en komplex uppgift. Operatörer/video leverantörer tar vanligt vis ut den här uppgiften till specialiserade DRM-tjänstleverantörer. Målet med det här dokumentet är att presentera en referens design och implementering av ett DRM-undersystem från slut punkt till slut punkt i en OTT-eller direkt uppspelnings lösning.

Mål läsarna för det här dokumentet är ingenjörer som arbetar i DRM-undersystem av OTT eller online streaming/Multiscreen-lösningar eller läsare som är intresserade av DRM-undersystem. Antagandet är att läsarna är bekant med minst en av DRM-teknikerna på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe Access.

I den här diskussionen av DRM inkluderar vi även common Encryption (CENC) med multi-DRM. En större tendens i online-direktuppspelning och OTT-branschen är att använda CENC med multi-Native DRM på olika klient plattformar. Den här trenden är en Skift från den tidigare en som använde ett enda DRM och dess klient-SDK för olika klient plattformar. När du använder CENC med multi-Native DRM krypteras både PlayReady och Widevine enligt specifikationen [common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Fördelarna med CENC med multi-DRM är att:

* Minskar krypterings kostnaden eftersom en enda krypterings process används för att rikta in sig på olika plattformar med dess inbyggda DRM: er.
* Minskar kostnaden för att hantera krypterade till gångar eftersom endast en enda kopia av krypterade till gångar behövs.
* Eliminerar DRM-klientens licens kostnad eftersom den inbyggda DRM-klienten vanligt vis är kostnads fri på den ursprungliga plattformen.

Microsoft är en aktiv marknadsförings panel av streck och CENC tillsammans med några större bransch spelare. Azure Media Services ger stöd för streck-och CENC. För nya meddelanden, se följande Bloggar:

*  [Vi ber att få presentera Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services lägger till Google Widevine-paket för att leverera en multi-DRM-ström](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Målen för artikeln

Syftet med den här artikeln är att:

* Tillhandahålla en referens design för ett DRM-undersystem som använder CENC med multi-DRM.
* Tillhandahålla en referens implementering på en Azure/Media Services-plattform.
* Diskutera några design-och implementerings ämnen.

I artikeln omfattar termen "multi-DRM" följande produkter:

* Microsoft PlayReady
* Google-Widevine
* Apple-FairPlay 

I följande tabell sammanfattas den inbyggda plattformen/inbyggda app och webbläsare som stöds av varje DRM.

| **Klient plattform** | **Inbyggt DRM-stöd** | **Webbläsare/app** | **Strömmande format** |
| --- | --- | --- | --- |
| **Smarta TV-apparater, operatorer STBs, OTT STBs** |PlayReady främst, och/eller Widevine, och/eller andra |Linux, Opera, WebKit, övrigt |Olika format |
| **Windows 10-enheter (Windows-dator, Windows-surfplattor, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universell Windows-plattform |BINDESTRECK (för HLS stöds inte PlayReady)<br/><br/>BINDESTRECK, Smooth Streaming (för HLS stöds inte PlayReady) |
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome/EME |STRECK, HLS |
| **iOS (iPhone, iPad), OS X-klienter och Apple TV** |FairPlay |Safari 8 +/EME |HLS |

Med hänsyn till det aktuella distributions läget för varje DRM vill en tjänst normalt implementera två eller tre DRM: er för att se till att du hanterar alla typer av slut punkter på det bästa sättet.

Det finns en kompromiss mellan tjänst logikens komplexitet och komplexiteten på klient sidan för att uppnå en viss nivå av användar upplevelse på olika klienter.

Tänk på följande när du väljer:

* PlayReady implementeras internt i varje Windows-enhet på vissa Android-enheter och är tillgängliga via Software SDK: er på nästan vilken plattform som helst.
* Widevine implementeras internt i varje Android-enhet, i Chrome och i vissa andra enheter.
* FairPlay är endast tillgängligt på iOS och Mac OS klienter eller genom iTunes.

Det finns två alternativ för ett typiskt multi-DRM:

* PlayReady och Widevine
* PlayReady, Widevine och FairPlay

## <a name="a-reference-design"></a>En referens design
I det här avsnittet presenteras en referens design som är oberoende till tekniken som används för att implementera den.

Ett DRM-undersystem kan innehålla följande komponenter:

* Nyckelhantering
* DRM-paketering
* DRM-licensleverans
* Rättighets kontroll
* Autentisering/auktorisering
* Spelare
* Ursprung/Content Delivery Network (CDN)

Följande diagram illustrerar en övergripande interaktion mellan komponenterna i ett DRM-undersystem:

![DRM-undersystem med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Designen har tre grundläggande lager:

* Ett Back-Office-lager (Black) exponeras inte externt.
* Ett DMZ-lager (mörkt blått) innehåller alla slut punkter som är riktade mot allmänheten.
* Ett offentligt Internet lager (lätt blått) innehåller CDN och spelare med trafik över det offentliga Internet.

Det bör också finnas ett innehålls hanterings verktyg för att kontrol lera DRM-skydd, oavsett om det är statiskt eller dynamiskt kryptering. Indata för DRM-kryptering är:

* MBR video-innehåll
* Innehålls nyckel
* URL för licens hämtning

Här är ett hög nivå flöde under uppspelnings tiden:

* Användaren är autentiserad.
* En autentiseringstoken skapas för användaren.
* DRM-skyddat innehåll (manifest) hämtas till spelaren.
* Spelaren skickar en begäran om licens hämtning till licens servrar tillsammans med ett nyckel-ID och en autentiseringstoken.

I följande avsnitt beskrivs designen av nyckel hantering.

| **ContentKey-till-till-gång** | **Scenario** |
| --- | --- |
| 1-till-1 |Det enklaste fallet. Den ger Finest-kontrollen. Men den här överenskommelsen resulterar i allmänhet av den högsta licens leverans kostnaden. Som minimum krävs en licens förfrågan för varje skyddad till gång. |
| 1-till-många |Du kan använda samma innehålls nyckel för flera till gångar. Till exempel, för alla till gångar i en logisk grupp, till exempel en genre eller en delmängd av en genre (eller en film gen), kan du använda en enda innehålls nyckel. |
| Många-till-1 |Flera innehålls nycklar krävs för varje till gång. <br/><br/>Om du till exempel behöver tillämpa dynamiskt CENC-skydd med multi-DRM för MPEG-streck och dynamisk AES-128-kryptering för HLS, behöver du två separata innehålls nycklar. Varje innehålls nyckel behöver sin egen ContentKeyType. (Använd ContentKeyType. CommonEncryption för den innehålls nyckel som används för dynamiskt CENC skydd. Använd ContentKeyType. EnvelopeEncryption för innehålls nyckeln som används för dynamisk AES-128-kryptering.)<br/><br/>Som ett annat exempel kan du i teorin använda en innehålls nyckel för att skydda video strömmen och en annan innehålls nyckel för att skydda ljud strömmen i CENC skydd av streck innehåll. |
| Många-till-många |Kombination av föregående två scenarier. En uppsättning innehålls nycklar används för var och en av de flera till gångarna i samma till gångs grupp. |

En annan viktig faktor att överväga är att använda beständiga och inte beständiga licenser.

Varför är det viktigt att tänka på?

Om du använder ett offentligt moln för licens leverans har beständiga och ej beständiga licenser direkt påverkan på licens leverans kostnaden. Följande två olika design ärenden är att illustrera:

* Månatlig prenumeration: Använd en beständiga licens och 1-till-många mappning av innehålls nyckel till till gång. Till exempel för alla barns filmer använder vi en enda innehålls nyckel för kryptering. Om så är fallet:

    Totalt antal licenser som begärs för alla barns filmer/enhet = 1

* Månatlig prenumeration: Använd en ej beständiga licens och 1-till-1-mappning mellan innehålls nyckel och till gång. Om så är fallet:

    Totalt antal licenser som begärts för alla barns filmer/enhet = [antal filmer som bevakas] x [antal sessioner]

De två olika designerna resulterar i mycket olika mönster för licens förfrågningar. De olika mönstren resulterar i olika licens leverans kostnader om licens leverans tjänsten tillhandahålls av ett offentligt moln, till exempel Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Kart design till teknik för implementering
Sedan mappas den allmänna designen till tekniker på Azure/Media Services-plattformen genom att ange vilken teknik som ska användas för varje Bygg block.

I följande tabell visas mappningen.

| **Bygg block** | **Teknik** |
| --- | --- |
| **Spelare** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitets leverantör (IDP)** |Azure Active Directory (Azure AD) |
| **Säkerhetstokentjänst (STS)** |Azure AD |
| **Arbets flöde för DRM-skydd** |Media Services dynamiskt skydd |
| **DRM-licensleverans** |* Media Services licens leverans (PlayReady, Widevine, FairPlay) <br/>* Axinom licens Server <br/>* Anpassad PlayReady-licensserver |
| **Kommer** |Slut punkt för Media Services strömning |
| **Nyckel hantering** |Krävs inte för referens implementering |
| **Innehållshantering** |Ett C#-konsol program |

Med andra ord används både IDP och STS med Azure AD. [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) : t används för spelaren. Både Media Services och Media Player stöd för bindestreck och CENC med multi-DRM.

Följande diagram visar den övergripande strukturen och flödet med föregående teknik mappning:

![CENC på Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om du vill konfigurera dynamisk CENC kryptering använder verktyget Content Management följande indata:

* Öppna innehåll
* Innehålls nyckel från generering/hantering av nyckel
* URL för licens hämtning
* En lista med information från Azure AD

Här är resultatet av verktyget för innehålls hantering:

* ContentKeyAuthorizationPolicy innehåller en specifikation för hur licens leveransen verifierar en JSON Web Token (JWT) och DRM-licens.
* AssetDeliveryPolicy innehåller specifikationer för strömnings format, DRM-skydd och URL: er för licens hämtning.

Här är flödet under körning:

* Vid användarautentisering genereras en JWT.
* En av de anspråk som finns i JWT är ett grupp anspråk som innehåller gruppobjekt-ID EntitledUserGroup. Detta påstående används för att skicka rättighets kontrollen.
* Spelaren laddar ned klient manifestet för CENC innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är CENC-skyddat.
   * URL: er för licens hämtning.
* Spelaren gör en begäran om licens hämtning baserat på webbläsare/DRM som stöds. I begäran om licens hämtning skickas även nyckel-ID: t och JWT. Licens leverans tjänsten verifierar JWT och de anspråk som finns innan den utfärdar den licens som krävs.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Implementerings procedurer
Implementeringen omfattar följande steg:

1. Förbered test till gångar. Koda/paketera en test video till en fragmenterad MP4 med flera bit hastigheter i Media Services. Den här till gången är *inte* DRM-skyddad. DRM-skydd görs med dynamiskt skydd senare.

2. Skapa ett nyckel-ID och en innehålls nyckel (valfritt från ett nyckel-Seed). I den här instansen behövs inte nyckel hanterings systemet eftersom endast ett nyckel-ID och en innehålls nyckel krävs för ett par test till gångar.

3. Använd Media Services API för att konfigurera tjänster för multi-DRM-licens för test till gången. Om du använder anpassade licens servrar av företaget eller företagets leverantörer i stället för licens tjänster i Media Services kan du hoppa över det här steget. Du kan ange URL: er för licens hämtning i steget när du konfigurerar licens leverans. Media Services API krävs för att ange vissa detaljerade konfigurationer, till exempel principer för begränsning av Auktoriseringsprinciper och mallar för licens svar för olika DRM-licenstjänster. För tillfället tillhandahåller Azure Portal inte det gränssnitt som krävs för den här konfigurationen. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).

4. Använd Media Services API för att konfigurera till gångs leverans principen för test till gången. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).

5. Skapa och konfigurera en Azure AD-klient i Azure.

6. Skapa några användar konton och grupper i din Azure AD-klient. Skapa minst en "berättigad användare"-grupp och Lägg till en användare i den här gruppen. Användare i den här gruppen släpper rättighets kontrollen i licens förvärv. Användare som inte tillhör den här gruppen kan inte skicka verifierings kontrollen och kan inte hämta en licens. Medlemskap i den här gruppen "berättigade användare" är ett obligatoriskt grupp anspråk i JWT som utfärdats av Azure AD. Du anger detta krav för anspråk i steget när du konfigurerar leverans tjänster för multi-DRM-licenser.

7. Skapa en ASP.NET MVC-app som värd för din Videos pelare. Den här ASP.NET-appen skyddas med användarautentisering mot Azure AD-klienten. Korrekta anspråk ingår i åtkomsttoken som erhållits efter användarautentisering. Vi rekommenderar OpenID Connect API för det här steget. Installera följande NuGet-paket:

   * Install-Package Microsoft. Azure. ActiveDirectory. GraphClient
   * Install-Package Microsoft. OWIN. Security. OpenIdConnect
   * Install-Package Microsoft. OWIN. Security. cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft. IdentityModel. clients. ActiveDirectory

8. Skapa en spelare med hjälp av [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd [Azure Media Player ProtectionInfo-API](https://amp.azure.net/libs/amp/latest/docs/) för att ange vilken DRM-teknik som ska användas på olika DRM-plattformar.

9. I följande tabell visas test mat ris.

    | **Rights** | **Webbläsare** | **Resultat för behörig användare** | **Resultat för berättigade användare** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge eller Internet Explorer 11 i Windows 10 |Gå |Underkänn |
    | **Widevine** |Chrome, Firefox, Opera |Gå |Underkänn |
    | **FairPlay** |Safari på macOS      |Gå |Underkänn |
    | **AES-128** |De flesta moderna webbläsare  |Gå |Underkänn |

Information om hur du konfigurerar Azure AD för en ASP.NET MVC Player-app finns i [integrera en Azure Media Services OWIN MVC-baserad app med Azure Active Directory och begränsa leverans av innehålls nycklar baserat på JWT-anspråk](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Mer information finns i [JWT token-autentisering i Azure Media Services och dynamisk kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

För information om Azure AD:

* Du kan hitta information om utvecklare i [Azure Active Directory Developer ' s guide](../../active-directory/azuread-dev/v1-overview.md).
* Du kan hitta administratörs information i [administrera din Azure AD-klient katalog](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Några problem i implementeringen
Använd följande felsöknings information för att få hjälp med implementerings problem.

* Utfärdar-URL: en måste sluta med "/". Mål gruppen måste vara klient-ID för Player-programmet. Lägg också till "/" i slutet av utfärdar-URL: en.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    I [JWT-avkodaren](http://jwt.calebb.net/)ser du **AUD** och **ISS**, som du ser i JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter för programmet i Azure AD på fliken **Konfigurera** i programmet. Behörigheter krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Använd rätt utfärdare när du konfigurerar dynamiskt CENC-skydd.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Följande fungerar inte:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    GUID är Azure AD-klient-ID. Du hittar GUID på popup-menyn för **slut punkter** i Azure Portal.

* Bevilja grupp medlemskap anspråk behörighet. Kontrol lera att följande är i manifest filen för Azure AD-program: 

    "groupMembershipClaims": "all" (Standardvärdet är null)

* Ange rätt TokenType när du skapar begränsnings krav.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Eftersom du lägger till stöd för JWT (Azure AD) utöver SWT (ACS) är standard-TokenType TokenType. JWT. Om du använder SWT/ACS måste du ange token till TokenType. SWT.

## <a name="additional-topics-for-implementation"></a>Ytterligare avsnitt för implementering
I det här avsnittet beskrivs några ytterligare ämnen i design och implementering.

### <a name="http-or-https"></a>HTTP eller HTTPS?
ASP.NET MVC Player-programmet måste ha stöd för följande:

* Användarautentisering via Azure AD, som är under HTTPS.
* JWT Exchange mellan klienten och Azure AD, som är under HTTPS.
* Hämtning av DRM-licenser av klienten, som måste vara under HTTPS om licens leverans tillhandahålls av Media Services. PlayReady-produktsortimentet mäter inte HTTPS för licens leverans. Om din PlayReady-licensserver är utanför Media Services kan du använda antingen HTTP eller HTTPS.

ASP.NET Player-programmet använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP rekommenderas dock för strömning, så du måste tänka på problemet med blandat innehåll.

* Webbläsaren tillåter inte blandat innehåll. Men plugin-program som Silverlight och OSMF-plugin-programmet för smidig och streck tillåter det. Blandat innehåll är ett säkerhets problem på grund av hotet om möjligheten att injicera skadlig Java Script, vilket kan orsaka att kunddata är utsatta för risk. Webbläsare blockerar den här funktionen som standard. Det enda sättet att undvika det finns på servern (ursprungs sidan) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen ingen bra idé.
* Undvik blandat innehåll. Både Player-programmet och Media Player ska använda HTTP eller HTTPS. När du spelar blandat innehåll kräver Silverlight-Tech att du avmarkerar en varning med blandat innehåll. Den blixt tekniska tekniken hanterar blandat innehåll utan varnings meddelande med blandat innehåll.
* Om slut punkten för direkt uppspelning skapades före 2014 augusti stöder den inte HTTPS. I det här fallet skapar och använder du en ny slut punkt för direkt uppspelning för HTTPS.

I referens implementeringen för DRM-skyddat innehåll är både programmet och streaming under HTTPS. För öppna innehåll behöver spelaren inte autentisering eller en licens, så du kan använda antingen HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory förnyelse av signerings nyckel
Förnyelse av signerings nyckel är en viktig punkt för att ta hänsyn till i din implementering. Om du ignorerar det slutar det färdiga systemet att fungera helt, inom sex veckor.

Azure AD använder bransch standarder för att upprätta förtroende mellan sig och program som använder Azure AD. Mer specifikt använder Azure AD en signerings nyckel som består av ett offentligt och privat nyckel par. När Azure AD skapar en säkerhetstoken som innehåller information om användaren, signeras den av Azure AD med en privat nyckel innan den skickas tillbaka till programmet. För att verifiera att token är giltig och kommer från Azure AD måste programmet validera token signatur. Programmet använder den offentliga nyckel som exponeras av Azure AD och som finns i klientens dokumentmetadata för federationsmetadata. Den här offentliga nyckeln och den signerings nyckel som den härleds till är samma som används för alla klienter i Azure AD.

Mer information om förnyelse av Azure AD Key finns i [viktig information om förnyelse av signerings nyckel i Azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md).

Mellan det [offentliga privata nyckel paret](https://login.microsoftonline.com/common/discovery/keys/):

* Den privata nyckeln används av Azure AD för att generera en JWT.
* Den offentliga nyckeln används av ett program som leverans tjänster för DRM-licenser i Media Services för att verifiera JWT.

Av säkerhets synpunkt roterar Azure AD certifikaten regelbundet (var sjätte vecka). När det gäller säkerhets överträdelser kan nyckel förnyelsen inträffa när som helst. Därför måste licens leverans tjänsterna i Media Services uppdatera den offentliga nyckeln som används när Azure AD roterar nyckel paret. Annars Miss lyckas token-autentisering i Media Services och ingen licens utfärdas.

Om du vill konfigurera den här tjänsten ställer du in TokenRestrictionTemplate. OpenIdConnectDiscoveryDocument när du konfigurerar leverans tjänster för DRM-licenser.

Här är JWT-flödet:

* Azure AD utfärdar JWT med den aktuella privata nyckeln för en autentiserad användare.
* När en spelare ser en CENC med innehåll som skyddas med multi-DRM, hittar den först den JWT som utfärdats av Azure AD.
* Spelaren skickar en begäran om licens hämtning med JWT till licens leverans tjänster i Media Services.
* Licens leverans tjänsterna i Media Services använda den aktuella/giltiga offentliga nyckeln från Azure AD för att verifiera JWT innan licenser utfärdas.

Leverans tjänster för DRM-Licens söker alltid efter den aktuella/giltiga offentliga nyckeln från Azure AD. Den offentliga nyckeln som presenteras av Azure AD är den nyckel som används för att verifiera en JWT som utfärdats av Azure AD.

Vad händer om nyckel förnyelsen sker när Azure AD genererar en JWT men innan JWT skickas av spelarna till leverans tjänster för DRM-licens i Media Services för verifiering?

Eftersom en nyckel kan återställas när som helst är mer än en giltig offentlig nyckel alltid tillgänglig i dokumentet för federationsmetadata. Media Services licens leverans kan använda alla nycklar som anges i dokumentet. Eftersom en nyckel kan återställas snart, kan en annan vara en ny ersättning, och så vidare.

### <a name="where-is-the-access-token"></a>Var finns åtkomsttoken?
Om du tittar på hur en webbapp anropar en API-app under [program identitet med OAuth 2,0-klientens autentiseringsuppgifter](../../active-directory/azuread-dev/web-api.md), är autentiseringsläget följande:

* En användare loggar in på Azure AD i webb programmet. Mer information finns i [webbläsare till webb program](../../active-directory/azuread-dev/web-app.md).
* Azure AD Authorization-slutpunkten omdirigerar användar agenten tillbaka till klient programmet med en auktoriseringskod. Användar agenten returnerar auktoriseringskod till klient programmets omdirigerings-URI.
* Webb programmet måste skaffa en åtkomsttoken så att den kan autentisera till webb-API: et och hämta önskad resurs. Den gör en begäran till Azure AD-token-slutpunkten och tillhandahåller autentiseringsuppgifter, klient-ID och webb-API: n för program-ID. Den visar auktoriseringskod för att bevisa att användaren har skickat.
* Azure AD autentiserar programmet och returnerar en JWT-åtkomsttoken som används för att anropa webb-API: et.
* Via HTTPS använder webb programmet den returnerade JWT-åtkomsttoken för att lägga till JWT-strängen med beteckningen "Bearer" i rubriken "Authorization" i begäran till webb-API: et. Webb-API: et validerar sedan JWT. Om verifieringen lyckas, returneras önskad resurs.

I det här programmets identitets flöde är webb-API: er som autentiserats för webb programmet. Därför kallas det här mönstret ett betrott under system. [Diagrammet Flow-flöde](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) beskriver hur ett flöde för auktoriserings kod tilldelning fungerar.

Licens förvärv med token-begränsning följer samma betrott under Systems mönster. Licens leverans tjänsten i Media Services är webb-API-resursen eller "backend-resursen" som ett webb program behöver åtkomst till. Så var är åtkomsttoken?

Åtkomsttoken hämtas från Azure AD. När en lyckad användarautentisering har autentiserats returneras en auktoriseringskod. Auktoriseringskod används sedan tillsammans med klient-ID: t och appens nyckel för att byta åtkomst-token. Åtkomsttoken används för att få åtkomst till ett "Point"-program som pekar på eller representerar leverans tjänsten för Media Services licenser.

Gör så här för att registrera och konfigurera pekverktyget i Azure AD:

1. I Azure AD-klienten:

   * Lägg till ett program (resurs) med inloggnings-URL https://[resource_name]. azurewebsites. net/. 
   * Lägg till ett app-ID med URL: en https://[aad_tenant_name]. onmicrosoft. com/[resource_name].

2. Lägg till en ny nyckel för resurs programmet.

3. Uppdatera appens manifest fil så att groupMembershipClaims-egenskapen har värdet "groupMembershipClaims": "alla".

4. I Azure AD-appen som pekar på Player-webbappen, i avsnittet **behörigheter till andra program**, lägger du till resurs programmet som lades till i steg 1. Under **delegerad behörighet** väljer du **åtkomst [resource_name]**. Det här alternativet ger webbappen behörighet att skapa åtkomsttoken som ansluter till resurs appen. Gör detta för både den lokala och distribuerade versionen av webbapp om du utvecklar med Visual Studio och Azure-webbappen.

Den JWT som utfärdats av Azure AD är den åtkomsttoken som används för att få åtkomst till pekarens resurs.

### <a name="what-about-live-streaming"></a>Vad händer om Direktsänd strömning?
Föregående diskussion fokuserade på till gångar på begäran. Vad händer om Direktsänd strömning?

Du kan använda exakt samma design och implementering för att skydda Direktsänd strömning i Media Services genom att behandla till gången som är kopplad till ett program som en VOD till gång.

För att göra Direktsänd strömning i Media Services måste du skapa en kanal och sedan skapa ett program under kanalen. För att skapa programmet måste du skapa en till gång som innehåller Live-arkivet för programmet. Om du vill tillhandahålla CENC med multi-DRM-skydd för Live-innehållet använder du samma installation/bearbetning till till gången som om det vore en VOD-till gång innan du startar programmet.

### <a name="what-about-license-servers-outside-media-services"></a>Vad gäller för licens servrar utanför Media Services?
Kunder investeras ofta i en licens Server grupp antingen i ett eget Data Center eller en värd av DRM-tjänst leverantörer. Med Media Services Content Protection kan du arbeta i hybrid läge. Innehållet kan nås och skyddas dynamiskt i Media Services, medan DRM-licenser levereras av servrar utanför Media Services. I det här fallet bör du tänka på följande ändringar:

* STS måste utfärda token som är acceptabla och kan verifieras av licens Server gruppen. Till exempel kräver Widevine-licensservern som tillhandahålls av Axinom ett speciellt JWT som innehåller ett rättighets meddelande. Därför måste du ha en STS för att utfärda ett sådant JWT. Information om den här typen av implementering finns i [Azures dokumentations Center](https://azure.microsoft.com/documentation/) och se [använda Axinom för att leverera Widevine-licenser till Azure Media Services](media-services-axinom-integration.md).
* Du behöver inte längre konfigurera licens leverans tjänsten (ContentKeyAuthorizationPolicy) i Media Services. Du måste ange URL: er för licens hämtning (för PlayReady, Widevine och FairPlay) när du konfigurerar AssetDeliveryPolicy för att konfigurera CENC med multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Vad händer om jag vill använda en anpassad STS?
En kund kan välja att använda en anpassad STS för att tillhandahålla JWTs. Orsaker är:

* IDP som används av kunden har inte stöd för STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöva mer flexibel eller tätare kontroll för att integrera STS med kundens fakturerings system. En MVPD-operatör kan till exempel erbjuda flera OTT-prenumerationer, till exempel Premium, Basic och idrotts. Operatören kanske vill matcha anspråk i en token med ett prenumerations paket så att endast innehållet i ett särskilt paket blir tillgängligt. I det här fallet ger en anpassad STS nödvändig flexibilitet och kontroll.

När du använder en anpassad STS måste du göra två ändringar:

* När du konfigurerar licens leverans tjänsten för en till gång måste du ange den säkerhets nyckel som ska användas för verifiering av den anpassade STS i stället för den aktuella nyckeln från Azure AD. (Mer information följer.) 
* När en JTW-token skapas, anges en säkerhets nyckel i stället för den privata nyckeln för det aktuella X509-certifikatet i Azure AD.

Det finns två typer av säkerhets nycklar:

* Symmetrisk nyckel: samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: ett offentligt privat privat nyckel par i ett X509-certifikat används med en privat nyckel för att kryptera/generera en JWT och med den offentliga nyckeln för att verifiera token.

> [!NOTE]
> Om du använder .NET Framework/C# som utvecklings plattform måste X509-certifikatet som används för en asymmetrisk säkerhets nyckel ha en nyckel längd på minst 2048. Detta är ett krav för klassen system. IdentityModel. tokens. X509AsymmetricSecurityKey i .NET Framework. Annars genereras följande undantag:
> 
> IDX10630: system. IdentityModel. tokens. X509AsymmetricSecurityKey för signering får inte vara mindre än 2048 bitar.

## <a name="the-completed-system-and-test"></a>Slutfört system och test
Det här avsnittet vägleder dig genom följande scenarier i det slutförda slutpunkt-till-slutpunkt-systemet så att du kan få en grundläggande bild av beteendet innan du får ett inloggnings konto:

* Om du behöver ett icke-integrerat scenario:

    * För video till gångar som finns i Media Services som antingen är oskyddade eller DRM-skyddade, men utan token-autentisering (som utfärdar en licens till vem som begär det), kan du testa den utan att logga in. Växla till HTTP om din video strömning är över HTTP.

* Om du behöver ett integrerat scenario från slut punkt till slut punkt:

    * För video till gångar under dynamiskt DRM-skydd i Media Services, med token-autentisering och JWT som genereras av Azure AD, måste du logga in.

För Player-webbprogrammet och dess inloggning, se [den här webbplatsen](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Användarinloggning
Om du vill testa det integrerade DRM-systemet från slut punkt till slut punkt måste du ha ett konto som skapats eller lagts till.

Vilket konto?

Även om Azure ursprungligen tillät åtkomst endast av Microsoft-konto användare tillåts åtkomst nu av användare från båda systemen. Alla Azure-egenskaper litar nu på Azure AD för autentisering och Azure AD autentiserar organisations användare. En Federations relation skapades där Azure AD litar på Microsoft-konto konsument identitets systemet för att autentisera konsument användare. Det innebär att Azure AD kan autentisera både gästa Microsoft-konton och interna Azure AD-konton.

Eftersom Azure AD litar på Microsoft-konto domän kan du lägga till alla konton från någon av följande domäner till den anpassade Azure AD-klienten och använda kontot för att logga in:

| **Domän namn** | **Domän** |
| --- | --- |
| **Anpassad Azure AD-klient domän** |somename.onmicrosoft.com |
| **Företags domän** |microsoft.com |
| **Microsoft-konto domän** |outlook.com, live.com, hotmail.com |

Du kan kontakta någon av författarna för att skapa ett konto eller lägga till ett konto.

Följande skärm bilder visar olika inloggnings sidor som används av olika domän konton:

**Anpassat Azure AD-klientens domän konto**: den anpassade inloggnings sidan för den anpassade Azure AD-klient domänen.

![Skärm bild som visar den anpassade inloggnings sidan för den anpassade Azure A D-klient domänen.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domännamn med smartkort**: inloggnings sidan som anpassats av Microsoft Corporate IT med tvåfaktorautentisering.

![Skärm bild som visar inloggnings sidan anpassad av Microsoft Corporate I T med tvåfaktorautentisering.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: inloggnings sidan för Microsoft-konto för konsumenter.

![Anpassat Azure AD-klientens domän konto](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använd krypterade medie tillägg för PlayReady
I en modern webbläsare med EME (Encrypted Media Extensions) för PlayReady-support, till exempel Internet Explorer 11 på Windows 8,1 eller senare och Microsoft Edge-webbläsare på Windows 10, är PlayReady den underliggande DRM för EME.

![Använd EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Det mörka mediet är eftersom PlayReady-skyddet förhindrar att du skapar en skärm bild av en skyddad video.

Följande skärm bild visar plugin-programmen för Player och Microsoft Security Essentials (MSE)/EME-stöd:

![Windows Media Player-plugin-program för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 i Windows 10 gör att [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kan anropas på Windows 10-enheter som har stöd för det. PlayReady SL3000 låser upp flödet av förbättrat Premium-innehåll (4K, HDR) och nya innehålls leverans modeller (för förbättrat innehåll).

För att fokusera på Windows-enheter är PlayReady den enda DRM i maskin varan som är tillgänglig på Windows-enheter (PlayReady SL3000). En strömmande tjänst kan använda PlayReady via EME eller via ett Universell Windows-plattform-program och erbjuda en högre video kvalitet med hjälp av PlayReady SL3000 än ett annat DRM. Vanligt vis är innehållet upp till 2 000 flöden via krom eller Firefox och innehåll upp till 4K-flöden via Microsoft Edge/Internet Explorer 11 eller ett Universell Windows-plattform program på samma enhet. Mängden är beroende av tjänst inställningar och implementering.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine
I en modern webbläsare med stöd för EME/Widevine, till exempel krom 41 + på Windows 10, Windows 8,1, Mac OSX Yosemite och Chrome på Android 4.4.4, Google Widevine är DRM bakom EME.

![Använd EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine hindrar dig inte från att göra en skärm bild av skyddad video.

![Spelare-plugin-program för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Obehöriga användare
Om en användare inte är medlem i gruppen "berättigade användare", godkänns inte rättighets kontrollen. Tjänsten multi-DRM vägrar att utfärda den begärda licensen som visas. Den detaljerade beskrivningen är "licens hämtningen misslyckades", som är utformad.

![Obehöriga användare](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Köra en anpassad säkerhetstokentjänst
Om du kör en anpassad STS utfärdas JWT av den anpassade STS genom att antingen använda en symmetrisk eller asymmetrisk nyckel.

Följande skärm bild visar ett scenario som använder en symmetrisk nyckel (med krom):

![Anpassad STS med en symmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Följande skärm bild visar ett scenario som använder en asymmetrisk nyckel via ett X509-certifikat (med hjälp av en Microsoft modern webbläsare):

![Anpassad STS med en asymmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I båda de föregående fallen är användarautentisering oförändrad. Det äger rum via Azure AD. Den enda skillnaden är att JWTs utfärdas av den anpassade STS i stället för Azure AD. När du konfigurerar dynamiskt CENC-skydd anger licens leverans tjänstens begränsning typen av JWT, antingen en symmetrisk eller asymmetrisk nyckel.

## <a name="summary"></a>Sammanfattning

Det här dokumentet diskuterade CENC med multi-Native DRM och åtkomst kontroll via autentiseringstoken, dess design och dess implementering genom att använda Azure, Media Services och Media Player.

* En referens design presenterad som innehåller alla nödvändiga komponenter i ett DRM/CENC-undersystem.
* En referens implementering presenterades på Azure, Media Services och Media Player.
* Vissa avsnitt som är direkt inblandade i utformningen och implementeringen diskuterades också.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
