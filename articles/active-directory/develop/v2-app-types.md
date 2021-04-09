---
title: Program typer för Microsoft Identity Platform | Azure
description: Typer av appar och scenarier som stöds av Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752785"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Program typer för Microsoft Identity Platform

Microsoft Identity Platform stöder autentisering för olika moderna app-arkitekturer, som alla baseras på bransch standard protokollen [OAuth 2,0 eller OpenID Connect](active-directory-v2-protocols.md). Den här artikeln beskriver de typer av appar som du kan bygga med hjälp av Microsoft Identity Platform, oavsett vilket språk eller vilken plattform du föredrar. Informationen är utformad för att hjälpa dig att förstå scenarier på hög nivå innan du börjar arbeta med koden i [program scenarier](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>Grunderna

Du måste registrera varje app som använder Microsoft Identity Platform i Azure Portal [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908). Registrerings processen för appen samlar in och tilldelar dessa värden för din app:

* Ett **program (klient) ID** som unikt identifierar din app
* En **omdirigerings-URI** som du kan använda för att dirigera svar tillbaka till appen
* Några andra scenario-speciella värden, till exempel typer av konto typer som stöds

Mer information finns i så här [registrerar du en app](quickstart-register-app.md).

När appen har registrerats kommunicerar appen med Microsoft Identity Platform genom att skicka begär anden till slut punkten. Vi tillhandahåller ramverk för öppen källkod och bibliotek som hanterar information om dessa förfrågningar. Du kan också välja att implementera autentiseringsmetoden själv genom att skapa begär anden till dessa slut punkter:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Appar med en sida (Java Script)

Många moderna appar har en fristående app från en sida som främst är skriven i Java Script, ofta med ett ramverk som vinkel, reagera eller Vue. Microsoft Identity Platform stöder de här apparna med hjälp av [OpenID Connect](v2-protocols-oidc.md) -protokollet för autentisering och antingen [OAuth 2,0-implicit beviljande av flöde](v2-oauth2-implicit-grant-flow.md) eller den senaste [OAuth 2,0-auktoriseringskod + PKCE Flow](v2-oauth2-auth-code-flow.md) för auktorisering (se nedan).

Flödes diagrammet nedan visar OAuth 2,0-auktoriseringskod (med information kring PKCE utelämnad), där appen tar emot en kod från Microsoft Identity Platform `authorize` -slutpunkten och löser in den för token och uppdaterar token med webb förfrågningar över olika webbplatser. Uppdateringstoken upphör att gälla var 24: e timme och appen måste begära en annan kod. Förutom åtkomst-token `id_token` begär en som representerar den inloggade användaren till klient programmet vanligt vis även genom samma flöde och/eller en separat OpenID Connect-begäran (visas inte här).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram som visar OAuth 2-auktoriseringskod mellan en app med en enda sida och slut punkten för säkerhetstoken." border="false":::

Om du vill se hur det här scenariot fungerar går du till [självstudien: Logga in användare och anropa Microsoft Graph-API: t från ett Java Script spa med auth Code Flow](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Authorization Code Flow jämfört med implicit flöde

För de flesta historiken av OAuth 2,0 var det [implicita flödet](v2-oauth2-implicit-grant-flow.md) det rekommenderade sättet att bygga appar på en sida. Med borttagning av [cookies från tredje part](reference-third-party-cookies-spas.md) och [mer uppmärksamhet](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) på säkerhets problem kring det implicita flödet har vi flyttat till Authorization Code Flow för appar på en sida.

För att säkerställa kompatibiliteten för din app i Safari och andra sekretess medveten webbläsare rekommenderar vi inte längre att använda det implicit flödet och rekommenderar i stället godkännande flödet.

## <a name="web-apps"></a>Webbappar

För webb program (.NET, PHP, Java, ruby, python, Node) som användaren har åtkomst till via en webbläsare kan du använda [OpenID Connect](active-directory-v2-protocols.md) för användar inloggning. I OpenID Connect tar webbappen emot en ID-token. En ID-token är en säkerhetstoken som verifierar användarens identitet och ger information om användaren i form av anspråk:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Mer information om olika typer av token som används i Microsoft Identity Platform finns [i referens för åtkomsttoken och](access-tokens.md) [id_token referens](id-tokens.md)

I Web Server-appar använder inloggnings flödet följande steg på hög nivå:

![Visar verifierings flödet för webbappar](./media/v2-app-types/convergence-scenarios-webapp.svg)

Du kan se till att användarens identitet genom att verifiera ID-token med en offentlig signerings nyckel som tas emot från Microsoft Identity Platform. En sessions-cookie har angetts, som kan användas för att identifiera användaren på efterföljande sid begär Anden.

Om du vill se hur det här scenariot fungerar kan du testa kod exemplen i [webbappen som loggar in i användar scenariot](scenario-web-app-sign-user-overview.md).

Förutom enkel inloggning kan en webbapp ha åtkomst till en annan webb tjänst, till exempel en REST API. I det här fallet deltar webb Server appen i en kombinerad OpenID Connect-och OAuth 2,0-flöde genom att använda [kod flödet för oauth 2,0-auktorisering](v2-oauth2-auth-code-flow.md). Mer information om det här scenariot finns [i komma igång med webbappar och webb-API: er](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>Webb-API:er

Du kan använda Microsoft Identity Platform för att skydda webb tjänster, t. ex. appens RESTful-webb-API. Webb-API: er kan implementeras på flera olika plattformar och på olika språk. De kan också implementeras med HTTP-utlösare i Azure Functions. I stället för ID-tokens och sessionscookies använder ett webb-API en OAuth 2,0-åtkomsttoken för att skydda dess data och för att autentisera inkommande begär Anden. Anroparen för ett webb-API lägger till en åtkomsttoken i Authorization-huvudet för en HTTP-begäran, så här:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API: et använder åtkomsttoken för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som kodas i åtkomsttoken. Mer information om olika typer av token som används i Microsoft Identity Platform finns [i referens för åtkomsttoken och](access-tokens.md) [id_token](id-tokens.md) referens.

Ett webb-API kan ge användare möjlighet att välja eller välja ut vissa funktioner eller data genom att exponera behörigheter, även kallade [omfång](v2-permissions-and-consent.md). För att en anropande app ska kunna erhålla behörighet till ett omfång måste användaren godkänna omfånget under ett flöde. Microsoft Identity Platform ber användaren om behörighet och registrerar sedan behörigheter i alla åtkomsttoken som webb-API: et tar emot. Webb-API: et verifierar de åtkomsttoken den tar emot vid varje anrop och utför verifierings kontroller.

Ett webb-API kan ta emot åtkomsttoken från alla typer av appar, inklusive Web Server-appar, Desktop-och mobilappar, appar med en sida, daemon på Server sidan och även andra webb-API: er. Hög nivå flödet för ett webb-API ser ut så här:

![Visar autentiserings flödet för webb-API](./media/v2-app-types/convergence-scenarios-webapi.svg)

Om du vill lära dig att skydda ett webb-API med hjälp av OAuth2-åtkomsttoken kan du titta närmare på webb-API-kod exemplen i det [skyddade webb-API-scenariot](scenario-protected-web-api-overview.md).

I många fall behöver webb-API: er också göra utgående förfrågningar till andra underordnade webb-API: er som skyddas av Microsoft Identity Platform. För att göra det kan webb-API: er dra nytta av flödet **på olika** sätt, vilket gör det möjligt för webb-API: et att utväxla en inkommande åtkomsttoken för en annan åtkomsttoken som ska användas i utgående begär Anden. Mer information finns i [Microsoft Identity Platform och OAuth 2,0 på uppdrag av Flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobila och interna appar

Enhets installerade appar, till exempel mobilappar och skrivbordsappar, behöver ofta åtkomst till backend-tjänster eller webb-API: er som lagrar data och utför funktioner för en användares räkning. De här apparna kan lägga till inloggning och auktorisering till backend-tjänster med hjälp av [OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

I det här flödet tar appen emot en auktoriseringskod från Microsoft Identity Platform när användaren loggar in. Auktoriseringskod representerar appens behörighet att anropa backend-tjänster åt den användare som är inloggad. Appen kan utbyta auktoriseringskod i bakgrunden för en OAuth 2,0-åtkomsttoken och en uppdateringstoken. Appen kan använda åtkomsttoken för att autentisera till webb-API: er i HTTP-begäranden och använda uppdateringstoken för att hämta nya åtkomsttoken när äldre åtkomsttoken upphör att gälla.

![Visar det inbyggda flödet för app-autentisering](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Om programmet använder standard-webbvisningen, kontrol lera informationen om "bekräfta mina inloggnings funktioner" och felkoden AADSTS50199 i [Azure AD-autentisering och auktorisering fel koder](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Daemon och appar på Server Sidan

Appar som har långvariga processer eller som fungerar utan interaktion med en användare behöver också ett sätt att komma åt skyddade resurser, till exempel webb-API: er. Dessa appar kan autentisera och hämta token genom att använda appens identitet, i stället för en användares delegerade identitet, med flödet OAuth 2,0-klientautentiseringsuppgifter. Du kan bevisa appens identitet med hjälp av en klient hemlighet eller ett certifikat. Mer information finns i [.net Core daemon-konsolens program med hjälp av Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

I det här flödet samverkar appen direkt med `/token` slut punkten för att få åtkomst:

![Visar daemon-flödet för app-autentisering](./media/v2-app-types/convergence-scenarios-daemon.svg)

Om du vill bygga en daemon-app läser du [dokumentationen för klient information](v2-oauth2-client-creds-grant-flow.md)eller försöker med en [.net-exempel App](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med de typer av program som stöds av Microsoft Identity Platform kan du läsa mer om [OAuth 2,0 och OpenID Connect](active-directory-v2-protocols.md) för att få en förståelse för de protokoll komponenter som används i de olika scenarierna.
