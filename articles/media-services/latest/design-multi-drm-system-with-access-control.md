---
title: Ett innehålls skydds system med flera DRM
description: Den här artikeln innehåller en detaljerad beskrivning av hur du utformar ett innehålls skydds system med flera DRM med Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 057a284f99dd7c3713249b19e8fc39212d9e6003
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640269"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Att utforma och skapa ett DRM-undersystem (Digital Rights Management) för en över-och OTT-eller direkt uppspelnings lösning är en komplex uppgift. Operatörer/video leverantörer tar vanligt vis ut den här uppgiften till specialiserade DRM-tjänstleverantörer. Målet med det här dokumentet är att presentera en referens design och en referens implementering av ett DRM-undersystem från slut punkt till slut punkt i en OTT-eller direkt uppspelnings lösning.

Mål läsarna för det här dokumentet är ingenjörer som arbetar i DRM-undersystem av OTT eller online streaming/Multiscreen-lösningar eller läsare som är intresserade av DRM-undersystem. Antagandet är att läsarna är bekant med minst en av DRM-teknikerna på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe Access.

I den här diskussionen inkluderar multi-DRM de tre DRM: er som stöds av Azure Media Services: Common Encryption (CENC) för PlayReady och Widevine, FairPlay samt AES-128-kryptering med tydlig nyckel. En större tendens i online-direktuppspelning och OTT-branschen är att använda inbyggd DRM: er på olika klient plattformar. Den här trenden är en Skift från den tidigare en som använde ett enda DRM och dess klient-SDK för olika klient plattformar. När du använder CENC med multi-Native DRM krypteras både PlayReady och Widevine enligt specifikationen [common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Fördelarna med att använda inbyggt multi-DRM för innehålls skydd är att:

* Minskar krypterings kostnaden eftersom en enda krypterings process används för att rikta in sig på olika plattformar med dess inbyggda DRM: er.
* Minskar kostnaden för att hantera till gångar eftersom endast en enda kopia av till gången behövs i lagringen.
* Eliminerar DRM-klientens licens kostnad eftersom den inbyggda DRM-klienten vanligt vis är kostnads fri på den ursprungliga plattformen.

### <a name="goals-of-the-article"></a>Målen för artikeln

Syftet med den här artikeln är att:

* Tillhandahålla en referens design för ett DRM-undersystem som använder alla tre DRM: er (CENC for bindestreck, FairPlay för HLS och PlayReady för smidig strömning).
* Tillhandahålla en referens implementering på Azure och Azure Media Services plattform.
* Diskutera några design-och implementerings ämnen.

I följande tabell sammanfattas inbyggda DRM-stöd på olika plattformar och EME-stöd i olika webbläsare.

| **Klient plattform** | **Inbyggt DRM** | **EME** |
| --- | --- | --- |
| **Smarta TV-apparater, STBs** | PlayReady, Widevine och/eller andra | Embedded Browser/EME för PlayReady och/eller Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 för PlayReady|
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome för Widevine |
| **iOS** | FairPlay | Safari för FairPlay (sedan iOS 11,2) |
| **macOS** | FairPlay | Safari för FairPlay (sedan Safari 9 + på macOS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Med hänsyn till det aktuella distributions läget för varje DRM vill en tjänst normalt implementera två eller tre DRM: er för att se till att du hanterar alla typer av slut punkter på det bästa sättet.

Det finns en kompromiss mellan tjänst logikens komplexitet och komplexiteten på klient sidan för att uppnå en viss nivå av användar upplevelse på olika klienter.

Tänk på följande när du väljer:

* PlayReady implementeras internt i varje Windows-enhet på vissa Android-enheter och är tillgängliga via Software SDK: er på nästan vilken plattform som helst.
* Widevine implementeras internt i varje Android-enhet, i Chrome och i vissa andra enheter. Widevine stöds också i Firefox och Opera browsers med streck.
* FairPlay finns på iOS, macOS och tvOS.

## <a name="a-reference-design"></a>En referens design

I det här avsnittet presenteras en referens design som är oberoende till tekniken som används för att implementera den.

Ett DRM-undersystem kan innehålla följande komponenter:

* Nyckelhantering
* Paket för DRM-kryptering
* DRM-licensleverans
* Rättighets kontroll/åtkomst kontroll
* Användarautentisering/auktorisering
* Player-app
* Ursprung/Content Delivery Network (CDN)

Följande diagram illustrerar en övergripande interaktion mellan komponenterna i ett DRM-undersystem:

![DRM-undersystem med CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Secure token service (STS)** |Azure AD |
| **Arbets flöde för DRM-skydd** |Azure Media Services dynamiskt skydd |
| **DRM-licensleverans** |* Media Services licens leverans (PlayReady, Widevine, FairPlay) <br/>* Axinom licens Server <br/>* Anpassad PlayReady-licensserver |
| **Kommer** |Slut punkt för Azure Media Services strömning |
| **Nyckel hantering** |Krävs inte för referens implementering |
| **Innehållshantering** |Ett C#-konsol program |

Med andra ord tillhandahålls både IDP och STS av Azure AD. [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) : t används för spelaren. Både Azure Media Services och Azure Media Player stöd för CENC över streck, FairPlay över HLS, PlayReady över smidig strömning och AES-128-kryptering för streck, HLS och smidighet.

Följande diagram visar den övergripande strukturen och flödet med föregående teknik mappning:

![CENC på Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Innehålls hanterings verktyget använder följande indata för att konfigurera DRM-innehålls skydd:

* Öppna innehåll
* Innehålls nyckel från nyckel hantering
* URL för licens hämtning
* En lista med information från Azure AD, till exempel Audience, utfärdare och token-anspråk

Här är resultatet av verktyget för innehålls hantering:

* ContentKeyPolicy beskriver DRM-Licens mal len för varje typ av DRM som används.
* ContentKeyPolicyRestriction beskriver åtkomst kontrollen innan en DRM-licens utfärdas
* Streamingpolicy beskriver de olika kombinationerna av DRM-krypterings läge – strömmande protokoll-container format, för strömning
* StreamingLocator beskriver innehålls nyckel/IV som används för kryptering och strömmande URL: er 

Här är flödet under körning:

* Vid användarautentisering genereras en JWT.
* En av de anspråk som finns i JWT är ett grupp anspråk som innehåller gruppobjekt-ID EntitledUserGroup. Detta påstående används för att skicka rättighets kontrollen.
* Spelaren laddar ned klient manifestet för CENC innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är DRM-skyddat.
   * URL: er för licens hämtning.
* Spelaren gör en begäran om licens hämtning baserat på webbläsare/DRM som stöds. I begäran om licens hämtning skickas även nyckel-ID: t och JWT. Licens leverans tjänsten verifierar JWT och de anspråk som finns innan den utfärdar den licens som krävs.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Implementerings procedurer
Implementeringen omfattar följande steg:

1. Förbered test till gångar. Koda/paketera en test video till en fragmenterad MP4 med flera bit hastigheter i Media Services. Den här till gången är *inte* DRM-skyddad. DRM-skydd görs med dynamiskt skydd senare.

2. Skapa ett nyckel-ID och en innehålls nyckel (valfritt från ett nyckel-Seed). I den här instansen behövs inte nyckel hanterings systemet eftersom endast ett nyckel-ID och en innehålls nyckel krävs för ett par test till gångar.

3. Använd Media Services API för att konfigurera tjänster för multi-DRM-licens för test till gången. Om du använder anpassade licens servrar av företaget eller företagets leverantörer i stället för licens tjänster i Media Services kan du hoppa över det här steget. Du kan ange URL: er för licens hämtning i steget när du konfigurerar licens leverans. Media Services API krävs för att ange vissa detaljerade konfigurationer, till exempel principer för begränsning av Auktoriseringsprinciper och mallar för licens svar för olika DRM-licenstjänster. För tillfället tillhandahåller Azure Portal inte det gränssnitt som krävs för den här konfigurationen. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](protect-with-drm.md).

4. Använd Media Services API för att konfigurera till gångs leverans principen för test till gången. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](protect-with-drm.md).

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

* Du kan hitta information om utvecklare i [Azure Active Directory Developer ' s guide](../../active-directory/develop/v2-overview.md).
* Du kan hitta administratörs information i [administrera din Azure AD-klient katalog](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Några problem i implementeringen

Använd följande felsöknings information för att få hjälp med implementerings problem.

* Utfärdar-URL: en måste sluta med "/". Mål gruppen måste vara klient-ID för Player-programmet. Lägg också till "/" i slutet av utfärdar-URL: en.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    I [JWT-avkodaren](http://jwt.calebb.net/)ser du **AUD** och **ISS**, som du ser i JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter för programmet i Azure AD på fliken **Konfigurera** i programmet. Behörigheter krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

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

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Eftersom du lägger till stöd för JWT (Azure AD) utöver SWT (ACS) är standard-TokenType TokenType. JWT. Om du använder SWT/ACS måste du ange token till TokenType. SWT.

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

![Anpassat Azure AD-klientens domän konto ett](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domännamn med smartkort**: inloggnings sidan som anpassats av Microsoft Corporate IT med tvåfaktorautentisering.

![Anpassat Azure AD-klientens domän konto två](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: inloggnings sidan för Microsoft-konto för konsumenter.

![Anpassat Azure AD-klientens domän konto tre](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använd krypterade medie tillägg för PlayReady

I en modern webbläsare med EME (Encrypted Media Extensions) för PlayReady-support, till exempel Internet Explorer 11 på Windows 8,1 eller senare och Microsoft Edge-webbläsare på Windows 10, är PlayReady den underliggande DRM för EME.

![Använd EME för PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Det mörka mediet är eftersom PlayReady-skyddet förhindrar att du skapar en skärm bild av en skyddad video.

Följande skärm bild visar plugin-programmen för Player och Microsoft Security Essentials (MSE)/EME-stöd:

![Windows Media Player-plugin-program för PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 i Windows 10 gör att [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kan anropas på Windows 10-enheter som har stöd för det. PlayReady SL3000 låser upp flödet av förbättrat Premium-innehåll (4K, HDR) och nya innehålls leverans modeller (för förbättrat innehåll).

För att fokusera på Windows-enheter är PlayReady den enda DRM i maskin varan som är tillgänglig på Windows-enheter (PlayReady SL3000). En strömmande tjänst kan använda PlayReady via EME eller via ett Universell Windows-plattform-program och erbjuda en högre video kvalitet med hjälp av PlayReady SL3000 än ett annat DRM. Vanligt vis är innehållet upp till 2 000 flöden via krom eller Firefox och innehåll upp till 4K-flöden via Microsoft Edge/Internet Explorer 11 eller ett Universell Windows-plattform program på samma enhet. Mängden är beroende av tjänst inställningar och implementering.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine

I en modern webbläsare med stöd för EME/Widevine, till exempel krom 41 + på Windows 10, Windows 8,1, Mac OSX Yosemite och Chrome på Android 4.4.4, Google Widevine är DRM bakom EME.

![Använd EME för Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine hindrar dig inte från att göra en skärm bild av skyddad video.

![Spelare-plugin-program för Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Använd EME för FairPlay

På samma sätt kan du testa FairPlay-skyddat innehåll i den här test spelaren i Safari på macOS eller iOS 11,2 och senare.

Kontrol lera att du har ange "FairPlay" som protectionInfo. type och Lägg till rätt URL för ditt program certifikat i FPS AC-sökväg (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Obehöriga användare

Om en användare inte är medlem i gruppen "berättigade användare", godkänns inte rättighets kontrollen. Tjänsten multi-DRM vägrar att utfärda den begärda licensen som visas. Den detaljerade beskrivningen är "licens hämtningen misslyckades", som är utformad.

![Obehöriga användare](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Köra en anpassad säkerhetstokentjänst

Om du kör en anpassad STS utfärdas JWT av den anpassade STS genom att antingen använda en symmetrisk eller asymmetrisk nyckel.

Följande skärm bild visar ett scenario som använder en symmetrisk nyckel (med krom):

![Anpassad STS med en symmetrisk nyckel](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Följande skärm bild visar ett scenario som använder en asymmetrisk nyckel via ett X509-certifikat (med hjälp av en Microsoft modern webbläsare):

![Anpassad STS med en asymmetrisk nyckel](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

I båda de föregående fallen är användarautentisering oförändrad. Det äger rum via Azure AD. Den enda skillnaden är att JWTs utfärdas av den anpassade STS i stället för Azure AD. När du konfigurerar dynamiskt CENC-skydd anger licens leverans tjänstens begränsning typen av JWT, antingen en symmetrisk eller asymmetrisk nyckel.
