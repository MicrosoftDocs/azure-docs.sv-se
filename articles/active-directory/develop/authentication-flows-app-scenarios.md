---
title: Microsoft Identity Platform-autentisering flödar & program scenarier | Azure
description: 'Lär dig mer om program scenarier för Microsoft Identity Platform, inklusive autentisering av identiteter, hämtning av tokens och anrop av skyddade API: er.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 4147adb004947c22ec39349f408d794bca160ef5
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582187"
---
# <a name="authentication-flows-and-application-scenarios"></a>Autentiserings flöden och program scenarier

Microsoft Identity Platform stöder autentisering för olika typer av moderna program arkitekturer. Alla arkitekturer baseras på bransch standard protokollen [OAuth 2,0 och OpenID Connect](active-directory-v2-protocols.md). Genom att använda [autentiseringsinställningarna för Microsoft Identity Platform](reference-v2-libraries.md)kan program autentisera identiteter och hämta token för att få åtkomst till skyddade API: er.

I den här artikeln beskrivs autentiserings flöden och program scenarier som de används i.

## <a name="application-categories"></a>Program kategorier

Du kan hämta token från flera olika typer av program, inklusive:

- Webbappar
- Mobilappar
- Skrivbordsappar
- Webb-API:er

Token kan också erhållas av appar som körs på enheter som inte har någon webbläsare eller som körs på Sakernas Internet (IoT).

I följande avsnitt beskrivs program kategorierna.

### <a name="protected-resources-vs-client-applications"></a>Skyddade resurser kontra klient program

Autentiserings scenarier omfattar två aktiviteter:

- **Förvärva säkerhetstoken för ett skyddat webb-API**: Vi rekommenderar att du använder [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md), som har utvecklats och stöds av Microsoft.
- **Skydda ett webb-API eller en webbapp**: en utmaning för att skydda dessa resurser är att verifiera säkerhetstoken. På vissa plattformar erbjuder Microsoft [bibliotek för mellanprogram](reference-v2-libraries.md).

### <a name="with-users-or-without-users"></a>Med användare eller utan användare

De flesta autentiseringar hämtar token för inloggade användares räkning.

![Scenarier med användare](media/scenarios/scenarios-with-users.svg)

Det finns dock även daemon-appar. I dessa scenarier hämtar appar token för sig själva utan någon användare.

![Scenarier med daemon-appar](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Enkels Ides, offentlig klient och konfidentiella klient program

Du kan hämta säkerhetstoken med flera olika typer av program. Dessa program tenderar att delas upp i följande tre kategorier. Varje används med olika bibliotek och objekt.

- **Program med en enda sida**: även kallat SPAs, dessa är webbappar där tokens förvärvas av en JavaScript-eller typescript-app som körs i webbläsaren. Många moderna appar har ett enda webb program på klient sidan som främst skrivs i Java Script. Programmet använder ofta ett ramverk som vinkel, reagera eller Vue. MSAL.js är det enda Microsoft Authentication Library som stöder program med en enda sida.

- **Offentliga klient program**: appar i den här kategorin, som följande typer, alltid loggar in användare:
  - Skrivbordsappar som anropar webb-API: er åt inloggade användare
  - Mobilappar
  - Appar som körs på enheter som inte har någon webbläsare, som de som körs på IoT

- **Konfidentiella klient program**: appar i den här kategorin omfattar:
  - Webb program som anropar ett webb-API
  - Webb-API: er som anropar ett webb-API
  - Daemon-appar, även när de implementeras som en konsol tjänst som en Linux-daemon eller en Windows-tjänst

### <a name="sign-in-audience"></a>Logga in målgrupp

De tillgängliga autentiserings flödena varierar beroende på inloggnings mål gruppen. Vissa flöden är bara tillgängliga för arbets-eller skol konton. Andra är tillgängliga både för arbets-eller skol konton och för personliga Microsoft-konton.

Mer information finns i [konto typer som stöds](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Programscenarier

Microsoft Identity Platform stöder autentisering för dessa app-arkitekturer:

- Enkelsidiga appar
- Webbappar
- Webb-API:er
- Mobilappar
- Inbyggda appar
- Daemon-appar
- Appar på Server Sidan

Programmen använder olika autentiserings flöden för att logga in användare och hämta token för att anropa skyddade API: er.

### <a name="single-page-application"></a>Enkelsidig app

Många moderna webbappar skapas som program på en enda sida på klient sidan. Dessa program använder Java Script eller ett ramverk som till exempel vinkel, Vue och reagera. Dessa program körs i en webbläsare.

Program med en enda sida skiljer sig från traditionella webbappar på Server sidan med avseende på autentiserings-egenskaper. Med hjälp av Microsoft Identity Platform kan program med en enda sida logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er. Microsoft Identity Platform erbjuder två typer av tilldelning för JavaScript-program:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![En programautentisering med en enda sida](media/scenarios/spa-app-auth.svg) | ![Ett program med en enda sida, implicit](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Webbapp som loggar in en användare

![En webbapp som loggar in en användare](media/scenarios/scenario-webapp-signs-in-users.svg)

Hjälpa till att skydda en webbapp som loggar in en användare:

- Om du utvecklar i .NET använder du ASP.NET eller ASP.NET Core med ASP.NET OpenID Connect mellan. Att skydda en resurs innebär att verifiera säkerhetstoken, som görs av IdentityModel- [tilläggen för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) och inte MSAL-bibliotek.

- Om du utvecklar i Node.js använder du [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Mer information finns i [webbapp som loggar in användare](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Webbapp som loggar in en användare och anropar ett webb-API för användarens räkning

![En webbapp som anropar webb-API: er](media/scenarios/web-app.svg)

För att anropa ett webb-API från en webbapp för en användares räkning använder du auktoriseringskod och lagrar de hämtade token i token cache. Vid behov uppdaterar MSAL token och styrenheten hämtar token tyst från cachen.

Mer information finns i [webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Skriv bords app som anropar ett webb-API för en inloggad användare

Om du vill att en app ska anropa ett webb-API som loggar in användare använder du de interaktiva metoderna för att hämta MSAL. Med dessa interaktiva metoder kan du styra inloggnings GRÄNSSNITTs upplevelsen. MSAL använder en webbläsare för den här interaktionen.

![En stationär app som anropar ett webb-API](media/scenarios/desktop-app.svg)

Det finns en annan möjlighet för Windows-värdbaserade program på datorer som är anslutna till en Windows-domän eller av Azure Active Directory (Azure AD). Dessa program kan hämta en token tyst med hjälp av [integrerad Windows-autentisering](https://aka.ms/msal-net-iwa).

Program som körs på en enhet utan webbläsare kan fortfarande anropa ett API för en användares räkning. Användaren måste logga in på en annan enhet som har en webbläsare för att kunna autentisera. Det här scenariot kräver att du använder [enhets kod flödet](https://aka.ms/msal-net-device-code-flow).

![Enhets kod flöde](media/scenarios/device-code-flow-app.svg)

Även om vi inte rekommenderar att du använder den, är [användar namnet/lösen ordet Flow](scenario-desktop-acquire-token.md#username-and-password) tillgängligt i offentliga klient program. Det här flödet behövs fortfarande i vissa scenarier som DevOps.

Användning av användar namn/lösen ord-flödet begränsar dina program. Till exempel kan inte program logga in en användare som behöver använda multifaktorautentisering eller verktyget för villkorlig åtkomst i Azure AD. Dina program har inte heller nytta av enkel inloggning. Autentisering med användar namn/lösen ord-flödet går mot principerna för modern autentisering och tillhandahålls endast av äldre skäl.

Om du vill att token-cachen ska sparas i skrivbordsappar, kan du anpassa [cachelagringen av token](scenario-desktop-acquire-token.md#file-based-token-cache). Genom att implementera [serialisering av dubbla token cache](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)kan du använda bakåtkompatibla token-och Forward-kompatibla token-cacheminnen. Dessa tokens stöder tidigare generationer av autentiseringspaket. Särskilda bibliotek innehåller Azure AD Authentication Library för .NET (ADAL.NET) version 3 och version 4.

Mer information finns i [Skriv bords program som anropar webb-API: er](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Mobilapp som anropar ett webb-API för en interaktiv användares räkning

På samma sätt som en stationär app anropar en mobilapp de interaktiva metoderna för att hämta en MSAL för att hämta en token för att anropa ett webb-API.

![En mobilapp som anropar ett webb-API](media/scenarios/mobile-app.svg)

MSAL iOS och MSAL Android använder system webbläsare som standard. Du kan dock dirigera dem att använda den inbäddade webbvy i stället. Det finns information som är beroende av den mobila plattformen: Universell Windows-plattform (UWP), iOS eller Android.

Vissa scenarier, t. ex. sådana som inbegriper villkorlig åtkomst som rör ett enhets-ID eller en enhets registrering, kräver att en Service Broker installeras på enheten. Exempel på utjämnare är Microsoft Företagsportal på Android och Microsoft Authenticator på Android och iOS. MSAL kan nu interagera med mäklare. Mer information om utjämnare finns i använda [utjämnare på Android och iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Mer information finns i [mobilapp som anropar webb-API: er](scenario-mobile-overview.md).

> [!NOTE]
> En mobilapp som använder MSAL. iOS, MSAL. Android eller MSAL.NET på Xamarin kan ha skydds principer för appar som tillämpas på den. Till exempel kan principerna hindra en användare från att kopiera skyddad text. Mobilappen hanteras av Intune och identifieras av Intune som en hanterad app. Mer information finns i [Översikt över Microsoft Intune App SDK](/intune/app-sdk).
>
> [Intune App SDK](/intune/app-sdk-get-started) är separat från MSAL-bibliotek och interagerar med Azure AD på egen hand.

### <a name="protected-web-api"></a>Skyddat webb-API

Du kan använda Microsoft Identity Platform-slutpunkten för att skydda webb tjänster som appens RESTful-webb-API. Ett skyddat webb-API anropas via en åtkomsttoken. Token skyddar API: ns data och autentiserar inkommande begär Anden. Anroparen för ett webb-API lägger till en åtkomsttoken i Authorization-huvudet för en HTTP-begäran.

Om du vill skydda din ASP.NET eller ASP.NET Core webb-API, verifiera åtkomsttoken. För den här verifieringen använder du ASP.NET JWT mellanprogram. Verifieringen görs av IdentityModel- [tilläggen för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -biblioteket och inte av MSAL.net.

Mer information finns i [Protected Web API](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Webb-API som anropar ett annat webb-API för en användares räkning

För att ditt skyddade webb-API ska anropa ett annat webb-API för en användares räkning måste appen Hämta en token för det underordnade webb-API: et. Sådana samtal kallas ibland *tjänst-till-tjänst-* anrop. Webb-API: er som anropar andra webb-API: er behöver tillhandahålla anpassad cache-serialisering.

![Ett webb-API som anropar ett annat webb-API](media/scenarios/web-api.svg)

Mer information finns i [webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Daemon-app som anropar ett webb-API i daemon-namnet

Appar som har långvariga processer eller som fungerar utan användar interaktion behöver också ett sätt att komma åt säkra webb-API: er. En sådan app kan autentisera och hämta token genom att använda appens identitet. Appen visar sin identitet med hjälp av en klient hemlighet eller ett certifikat.

Du kan skriva sådana daemon-appar som hämtar en token för den anropande appen genom att använda förvärvs metoder för [klientens autentiseringsuppgifter](scenario-daemon-acquire-token.md#acquiretokenforclient-api) i MSAL. Dessa metoder kräver en klient hemlighet som du lägger till i appens registrering i Azure AD. Appen delar sedan hemligheten med den heta daemonen. Exempel på sådana hemligheter är program lösen ord, certifikat kontroll och klient kontroll.

![En daemon-app som anropas av andra appar och API: er](media/scenarios/daemon-app.svg)

Mer information finns i [daemon-program som anropar webb-API: er](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenarier och stödda autentiserings flöden

Du använder autentiserings flöden för att implementera program scenarier som begär tokens. Det finns inte en en-till-en-mappning mellan program scenarier och autentiserings flöden.

Scenarier som innefattar att förvärva token mappas också till OAuth 2,0-autentiserings flöden. Mer information finns i [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Detaljerad genom gång av scenario</th> <th>OAuth 2,0-flöde och-beviljande</th> <th>Målgrupp</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Ensidesapp</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a> med PKCE</td>
   <td>Arbets-eller skol konton, personliga konton och Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Ensidesapp</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicit</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Webbapp som loggar in användare</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Authorization code (Auktoriseringskod)</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webbapp som anropar webb-API:er</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Authorization code (Auktoriseringskod)</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Datorprogram som anropar webb-API:er</a></td>
   <td>Interaktivt med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrerad Windows-autentisering</td>
   <td>Arbets- eller skolkonton</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Enhets kod</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobilapp att anropar webb-API:er</a></td>
   <td>Interaktivt med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Daemon-app som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Klientautentiseringsuppgifter</a></td>
   <td>Endast app-behörigheter som inte har någon användare och som endast används i Azure AD-organisationer</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Webb-API som anropar webb-API:er</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">På uppdrag av</a></td>
   <td>Arbets-eller skol konton och personliga konton</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenarier och plattformar och språk som stöds

Microsoft Authentication libraries stöder flera plattformar:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Inbyggd iOS
- macOS
- Ursprunglig Android
- Java
- Python

Du kan också använda olika språk för att bygga dina program.

> [!NOTE]
> Vissa program typer är inte tillgängliga på alla plattformar.

I Windows-kolumnen i följande tabell beskrivs varje tid av .NET Core .NET Framework också. Den sistnämnda filen utelämnas för att undvika att tabellen blir rörig.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Ensidesapp](scenario-spa-overview.md) <br/>[![App-autentisering med en sida](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Ensidesapp](scenario-spa-overview.md) <br/>[![En app med en enda sida implicit](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md) <br/>[![Webbapp som loggar in användare](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webbapp som anropar webb-API:er](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python
| [Datorprogram som anropar webb-API:er](scenario-desktop-overview.md) <br/> <br/>[ ![ Stationär app som anropar webb-API: er](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ Enhets kod flöde](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python <br/> ![iOS/mål C eller Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Mobilapp att anropar webb-API:er](scenario-mobile-overview.md) <br/> [![Mobilapp att anropar webb-API:er](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/mål C eller Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-app](scenario-daemon-overview.md) <br/> [![Daemon-app](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python
| [Webb-API som anropar webb-API:er](scenario-web-api-call-api-overview.md) <br/><br/> [![Webb-API som anropar webb-API:er](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python

Mer information finns i [Microsoft Identity Platform Authentication libraries](reference-v2-libraries.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [grundläggande autentisering](./authentication-vs-authorization.md) och [åtkomsttoken i Microsoft Identity Platform](access-tokens.md).
* Lär dig mer om [att skydda åtkomsten till IoT-appar](/azure/architecture/example-scenario/iot-aad/iot-aad).
