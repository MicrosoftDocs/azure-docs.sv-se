---
title: Flöden för MSAL-autentisering | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om de autentiserings flöden och-anslag som används av Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755022"
---
# <a name="authentication-flows"></a>Autentiserings flöden

Microsoft Authentication Library (MSAL) stöder flera autentiserings flöden för användning i olika program scenarier.

| Flöden | Beskrivning | Används i |
|--|--|--|
| [Authorization code (Auktoriseringskod)](#authorization-code) | Används i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. Gör att du kan lägga till inloggnings-och API-åtkomst till dina mobila och Station ära appar. | [Skrivbordsappar](scenario-desktop-overview.md), [mobilappar](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) |
| [Klientautentiseringsuppgifter](#client-credentials) | Gör att du kan komma åt webb värd resurser genom att använda identiteten för ett program. Används ofta för server-till-Server-interaktioner som måste köras i bakgrunden, utan omedelbar interaktion med en användare. | [Daemon-appar](scenario-daemon-overview.md) |
| [Enhets kod](#device-code) | Tillåter att användare loggar in på inmatade enheter, till exempel en smart TV, IoT-enhet eller skrivare. | [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Implicit beviljande](#implicit-grant) | Tillåter appen att hämta tokens utan att säkerhetskopiera autentiseringsuppgifter för backend-servern. Gör att appen kan logga in användaren, underhålla sessionen och hämta token till andra webb-API: er i klientens JavaScript-kod. | [Enkels Ides program (SPA)](scenario-spa-overview.md) |
| [På uppdrag av](#on-behalf-of) | Ett program anropar en tjänst eller ett webb-API, som i sin tur måste anropa en annan tjänst eller ett webb-API. Tanken är att sprida den delegerade användar identiteten och behörigheterna via begär ande kedjan. | [Webb-API:er](scenario-web-api-call-api-overview.md) |
| [Användarnamn/lösenord](#usernamepassword) | Gör att ett program kan logga in användaren genom att direkt hantera lösen ordet. Det här flödet rekommenderas inte. | [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#username-and-password) |
| [Integrerad Windows-autentisering](#integrated-windows-authentication) | Gör det möjligt för program på domän-eller Azure Active Directory (Azure AD) anslutna datorer att hämta en token tyst (utan någon användar GRÄNSSNITTs interaktion från användaren). | [Desktop/Mobile-appar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur ditt klient program har skapats kan den använda ett eller flera av de autentiseringsscheman som stöds av Microsoft Identity Platform. Dessa flöden kan producera flera typer av tokens samt auktoriseringsregler, och kräver olika token för att de ska fungera.

| Flöden                                                                               | Innebär            | id_token | åtkomsttoken | uppdatera token | auktoriseringskod |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (endast app-only) |               |                    |
| [Enhets kod flöde](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Implicit flöde](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md)                                | åtkomsttoken        | x        | x            | x             |                    |
| [Användar namn/lösen ord](v2-oauth-ropc.md) (ROPC)                                       | användar namn & lösen ord | x        | x            | x             |                    |
| [Hybrid OIDC-flöde](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Uppdatera token-inlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | uppdatera token       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Interaktiv och icke-interaktiv autentisering

Flera av dessa flöden stöder både interaktiv och icke-interaktiv token-hämtning.

  - **Interaktiva** innebär att användaren kan uppmanas att ange indata. Till exempel uppmana användaren att logga in, utföra Multi-Factor Authentication (MFA) eller bevilja ytterligare medgivande till resurser.
  - **Icke-interaktiv** eller *tyst* autentisering försöker hämta en token på ett sätt som inloggnings servern *inte kan* uppmana användaren att ange ytterligare information.

Ditt MSAL-baserade program bör först försöka hämta en token i *bakgrunden* och sedan interaktivt endast om den icke-interaktiva metoden Miss lyckas. Mer information om det här mönstret finns i [Hämta och cache-token med hjälp av Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Authorization code (Auktoriseringskod)

[Beviljande av OAuth 2-auktoriseringskod](v2-oauth2-auth-code-flow.md) kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser som webb-API: er. På så sätt kan du lägga till inloggnings-och API-åtkomst till dina mobila och Station ära appar.

När användarna loggar in på webb program (webbplatser) får webb programmet en auktoriseringskod. Auktoriseringskod löses för att hämta en token för att anropa webb-API: er.

![Diagram över flöde för auktoriseringskod](media/msal-authentication-flows/authorization-code.png)

I föregående diagram är programmet:

1. Begär en auktoriseringskod som löses in för en åtkomsttoken.
2. Använder åtkomsttoken för att anropa ett webb-API.

### <a name="considerations"></a>Överväganden

- Du kan bara använda auktoriseringskod en gång för att lösa in en token. Försök inte att hämta en token flera gånger med samma auktoriseringskod eftersom den uttryckligen förbjuds av protokoll standard specifikationen. Om du löser in koden flera gånger, antingen avsiktligt eller på grund av att ett ramverk också gör det, får du följande fel meddelande:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Klientautentiseringsuppgifter

Med [flödet för OAuth 2-klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) kan du komma åt webb värd resurser genom att använda identiteten för ett program. Den här beviljandetypen används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan direkt interaktion med en användare. Dessa typer av program kallas ofta för daemon eller tjänstkonton.

Med det beviljande flödet för klientautentiseringsuppgifter tillåts en webb tjänst (en konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare, för att autentisera vid anrop till en annan webb tjänst. I det här scenariot är klienten vanligt vis en webb tjänst på mellan nivå, en daemon-tjänst eller en webbplats. För en högre säkerhets nivå tillåter Microsofts identitets plattform också att anrops tjänsten använder ett certifikat (i stället för en delad hemlighet) som autentiseringsuppgift.

> [!NOTE]
> Det konfidentiella klient flödet är inte tillgängligt på mobila plattformar som UWP, Xamarin. iOS och Xamarin. Android eftersom de endast stöder offentliga klient program. Offentliga klient program vet inte hur man kan bevisa programmets identitet för identitets leverantören. En säker anslutning kan uppnås på webbappen eller webb-API-backend genom att distribuera ett certifikat.

### <a name="application-secrets"></a>Program hemligheter

![Diagram över konfidentiell klient med lösen ord](media/msal-authentication-flows/confidential-client-password.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för program hemlighet eller lösen ord.
2. Använder token för att göra begär Anden för resursen.

### <a name="certificates"></a>Certifikat

![Diagram över konfidentiell klient med certifikat](media/msal-authentication-flows/confidential-client-certificate.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av autentiseringsuppgifter för certifikatet.
2. Använder token för att göra begär Anden för resursen.

Dessa klientautentiseringsuppgifter måste vara:

- Registrerad med Azure AD.
- Skickades när du konstruerade det konfidentiella klient program objektet i koden.

## <a name="device-code"></a>Enhets kod

Med [enhets kod flödet OAuth 2](v2-oauth2-device-code.md) kan användarna logga in på indata-begränsade enheter som smarta TV-apparater, IoT-enheter och skrivare. Interaktiv autentisering med Azure AD kräver en webbläsare. Enhets kod flödet låter användaren använda en annan enhet som en dator eller mobil telefon för att logga in interaktivt, där enheten eller operativ systemet inte tillhandahåller någon webbläsare.

Genom att använda enhets kod flödet hämtar programmet token via en två stegs process som har utformats för dessa enheter och operativ system. Exempel på sådana program är sådana som körs på IoT-enheter och CLI-verktyg (kommando rads gränssnitt).

![Diagram över enhets kod flöde](media/msal-authentication-flows/device-code.png)

I diagrammet ovan händer följande:

1. När användarautentisering krävs ger appen en kod och ber användaren att använda en annan enhet som en Internet-ansluten smartphone för att besöka en URL (t. ex. `https://microsoft.com/devicelogin` ). Användaren uppmanas sedan att ange koden och fortsätta med en normal autentisering, inklusive medgivande-prompter och [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), om det behövs.
1. Vid lyckad autentisering tar kommando rads appen emot de begärda token via en återställnings kanal och använder dem för att utföra de webb-API-anrop som krävs.

### <a name="constraints"></a>Villkor

- Enhets kod flödet är endast tillgängligt i offentliga klient program.
- Den auktoritet som skickades när du konstruerade det offentliga klient programmet måste vara något av följande:
  - Används i formuläret `https://login.microsoftonline.com/{tenant}/,` där `{tenant}` är antingen det GUID som representerar klient-ID: t eller ett domän namn som är associerat med klienten.
  - För arbets-och skol konton i formuläret `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Implicit beviljande

Med det [implicita tilldelnings flödet för OAuth 2](v2-oauth2-implicit-grant-flow.md) kan appen Hämta tokens från Microsoft Identity Platform utan att utföra en server för autentiseringsuppgifter för backend-servern. Med det här flödet kan appen logga in användaren, underhålla en session och hämta token för andra webb-API: er i klientens JavaScript-kod.

![Diagram över implicit beviljande av flöde](media/msal-authentication-flows/implicit-grant.svg)

Många moderna webb program skapas som på klient sidan, ett enda sid program (SPA) som skrivits i Java Script eller ett SPA-ramverk, till exempel vinkel, Vue.js och React.js. Dessa program körs i en webbläsare och har olika egenskaper för autentisering än traditionella webb program på Server sidan. Microsoft Identity Platform gör det möjligt för program att logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er med hjälp av det implicita tilldelnings flödet. Det implicita flödet gör att programmet kan hämta ID-token för att representera den autentiserade användaren och även få åtkomst till tokens som krävs för att anropa skyddade API: er.

Det här autentiseringsschemat omfattar inte program scenarier som använder plattforms oberoende JavaScript-ramverk som Electron eller React-Native eftersom de kräver ytterligare funktioner för interaktion med de ursprungliga plattformarna.

Token som utfärdas via det implicita flödes läget har en **längd begränsning** eftersom de returneras till webbläsaren via URL (där `response_mode` är antingen `query` eller `fragment` ). Vissa webbläsare begränsar längden på URL: en i webbläsarens fält och fungerar inte när den är för lång. Dessa implicita flödes-token innehåller därför inte `groups` eller `wids` anspråk.

## <a name="on-behalf-of"></a>På uppdrag av

[OAuth 2-flödet för autentisering av autentiseringsuppgifter](v2-oauth2-on-behalf-of-flow.md) används när ett program anropar en tjänst eller ett webb-API som i sin tur måste anropa en annan tjänst eller ett webb-API. Tanken är att sprida den delegerade användar identiteten och behörigheterna via begär ande kedjan. För att mellanskikts tjänsten ska kunna göra autentiserade begär anden till den underordnade tjänsten måste den skydda en åtkomsttoken från Microsoft Identity Platform för användarens *räkning* .

![Diagram över flöde på samma vägnar](media/msal-authentication-flows/on-behalf-of.png)

I diagrammet ovan händer följande:

1. Programmet hämtar en åtkomsttoken för webb-API: et.
2. En klient (webb-, skriv bords-, mobil-eller Enkels Ides program) anropar ett skyddat webb-API och lägger till åtkomsttoken som en Bearer-token i HTTP-begärans Authentication-huvud. Webb-API: et autentiserar användaren.
3. När klienten anropar webb-API: et begär webb-API en annan token för användaren.
4. Det skyddade webb-API: et använder denna token för att anropa ett underordnat webb-API för användaren. Webb-API: et kan också senare begära token för andra underordnade API: er (men fortfarande på uppdrag av samma användare).

## <a name="usernamepassword"></a>Användarnamn/lösenord

Med Grant-ROPC ( [Resource Owner Password credentials](v2-oauth-ropc.md) ) kan ett program logga in användaren genom att direkt hantera lösen ordet. I ditt Skriv bords program kan du använda flödet för användar namn/lösen ord för att hämta en token i bakgrunden. Inget användar gränssnitt krävs när programmet används.

![Diagram över flödet för användar namn/lösen ord](media/msal-authentication-flows/username-password.png)

I föregående diagram är programmet:

1. Hämtar en token genom att skicka användar namnet och lösen ordet till identitets leverantören.
2. Anropar ett webb-API med hjälp av token.

> [!WARNING]
> Det här flödet rekommenderas inte. Det krävs en hög grad av förtroende och exponering av autentiseringsuppgifter. Du bör *endast* använda det här flödet när det inte går att använda fler säkra flöden. Mer information finns i [Vad är lösningen på det växande problemet med lösen ord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Det prioriterade flödet för att hämta en token tyst på domänanslutna Windows-datorer är [integrerad Windows-autentisering](#integrated-windows-authentication). I andra fall använder du [enhets kod flödet](#device-code).

Även om användar namnet och lösen ordet kan vara användbart i vissa scenarier som DevOps, undviker du det om du vill använda användar namn/lösen ord i interaktiva scenarier där du anger ditt eget användar gränssnitt.

Med användar namn/lösen ord:

- Användare som behöver utföra Multi-Factor Authentication kan inte logga in eftersom det inte finns någon interaktion.
- Användare kan inte utföra enkel inloggning.

### <a name="constraints"></a>Villkor

Utöver de [integrerade begränsningarna för Windows-autentisering](#integrated-windows-authentication)gäller även följande begränsningar:

- Användar namnet/lösen ordet är inte kompatibelt med villkorlig åtkomst och Multi-Factor Authentication. Det innebär att om din app körs i en Azure AD-klient där klient organisationen kräver Multi-Factor Authentication, kan du inte använda det här flödet. Många organisationer gör det.
- ROPC fungerar bara för arbets-och skol konton. Du kan inte använda ROPC för Microsoft-konton (MSA).
- Flödet är tillgängligt på .NET Desktop och .NET Core, men inte på Universell Windows-plattform.
- I Azure AD B2C fungerar ROPC-flödet bara för lokala konton. Information om ROPC i MSAL.NET och Azure AD B2C finns i [using ROPC with Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

MSAL stöder integrerad Windows-autentisering (IWA) för Station ära och mobila program som körs på en domänansluten eller Azure AD-ansluten Windows-dator. Med hjälp av IWA kan de här programmen hämta en token tyst utan att kräva användar GRÄNSSNITTs interaktion av användaren.

![Diagram över integrerad Windows-autentisering](media/msal-authentication-flows/integrated-windows-authentication.png)

I föregående diagram är programmet:

1. Hämtar en token med hjälp av integrerad Windows-autentisering.
2. Använder token för att göra begär Anden för resursen.

### <a name="constraints"></a>Villkor

Integrerad Windows-autentisering (IWA) stöder *endast* federerade användare – användare som skapats i Active Directory och som backas upp av Azure AD. Användare som skapats direkt i Azure AD utan Active Directory-säkerhets kopiering (hanterade användare) kan inte använda det här autentiseringsschemat. Den här begränsningen påverkar inte [flödet av användar namn/lösen ord](#usernamepassword).

IWA är för .NET Framework, .NET Core och Universell Windows-plattform program.

IWA kringgår inte Multi-Factor Authentication. Om Multi-Factor Authentication har kon figurer ATS kan IWA Miss förväntat om en Multi-Factor Authentication-utmaning krävs. Multi-Factor Authentication kräver användar interaktion.

Du styr inte när identitets leverantören begär tvåfaktorautentisering som ska utföras. Klient organisationens administratör gör. Vanligt vis krävs tvåfaktorautentisering när du loggar in från ett annat land/en annan region, när du inte är ansluten via VPN till ett företags nätverk och ibland även när du är ansluten via VPN. Azure AD använder AI för att kontinuerligt lära sig om tvåfaktorautentisering krävs. Om IWA Miss lyckas bör du gå tillbaka till en [interaktiv användar varning](#interactive-and-non-interactive-authentication).

Auktoriteten som skickades när du konstruerade det offentliga klient programmet måste vara något av följande:

- Används i formuläret `https://login.microsoftonline.com/{tenant}/,` där `{tenant}` är antingen det GUID som representerar klient-ID: t eller ett domän namn som är associerat med klienten.
- För arbets-och skol konton ( `https://login.microsoftonline.com/organizations/` ). Microsoft personal accounts (MSA) stöds inte. Du kan inte använda `/common` eller `/consumers` klienter.

Eftersom IWA är ett tyst flöde måste något av följande vara sant:

- Användaren av ditt program måste tidigare ha samtyckt till att använda programmet.
- Klient organisationens administratör måste tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.

Det innebär att något av följande stämmer:

- Du som utvecklare har valt att **ge** dig ett bidrag i Azure Portal.
- En innehavaradministratör har valt **beviljande/återkalla administratörs medgivande för {klient domän}** på fliken **API-behörigheter** i appens registrering i Azure Portal (se [lägga till behörigheter för åtkomst till ditt webb-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- Du har angett ett sätt för användarna att samtycka till programmet. Se [begära individuell användar medgivande](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Du har angett ett sätt för klient organisations administratören att godkänna ansökan. Se [administrativt medgivande](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

IWA-flödet har Aktiver ATS för .NET Desktop-, .NET Core-och Windows Universal Platform-appar.

Mer information om medgivande finns i [v 2.0-behörigheter och medgivande](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat de autentiseringsscheman som stöds av Microsoft Authentication Library (MSAL), lär du dig att förvärva och cachelagra de token som används i dessa flöden:

[Hämta och cachelagra token med Microsoft Authentication Library (MSAL)](msal-acquire-cache-tokens.md)
