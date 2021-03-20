---
title: OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: En guide till OAuth 2,0-och OpenID Connect-protokoll som stöds av Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 765c363542b07deac44d47b94731e1109fcba045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755748"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform

Microsoft Identity Platform-slutpunkten för identitet-som-en-tjänst implementerar autentisering och auktorisering med bransch standard protokollen OpenID Connect (OIDC) respektive OAuth 2,0. Tjänsten är standard-kompatibel, men det kan finnas små skillnader mellan de två implementeringarna av de här protokollen. Informationen här är användbar om du väljer att skriva koden genom att direkt skicka och hantera HTTP-förfrågningar eller använda ett bibliotek med öppen källkod från tredje part, i stället för att använda ett av våra [bibliotek med öppen källkod](reference-v2-libraries.md).

## <a name="the-basics"></a>Grunderna

I nästan alla OAuth 2,0-och OpenID Connect-flöden finns det fyra parter som ingår i Exchange:

![Diagram över OAuth 2,0-roller](./media/active-directory-v2-flows/protocols-roles.svg)

* **Auktoriseringsservern** är Microsoft Identity Platform och ansvarar för att se till att användarens identitet, beviljar och återkallar åtkomst till resurser och utfärdande av tokens. Auktoriseringsservern kallas även för identitets leverantören – den hanterar på ett säkert sätt vad som helst med användarens information, deras åtkomst och förtroende relationer mellan parter i ett flöde.
* **Resurs ägaren** är vanligt vis slutanvändaren. Det är den part som äger data och har möjlighet att tillåta klienter att komma åt dessa data eller resurser.
* **OAuth-klienten** är din app som identifieras av dess program-ID. OAuth-klienten är vanligt vis den part som slutanvändaren interagerar med och den begär token från auktoriseringsservern. Klienten måste beviljas behörighet att få åtkomst till resursen av resurs ägaren.
* **Resurs servern** är den plats där resursen eller data finns. Den litar på Auktoriseringsservern för att på ett säkert sätt autentisera och auktorisera OAuth-klienten, och använder sig av Bearer-åtkomsttoken för att säkerställa att åtkomst till en resurs kan beviljas.

## <a name="app-registration"></a>Appregistrering

Varje app som vill acceptera både personliga konton och arbets-eller skol konton måste registreras via **Appregistreringar** -upplevelsen i [Azure Portal](https://aka.ms/appregistrations) innan de kan signera dessa användare i med hjälp av OAuth 2,0 eller OpenID Connect. Registrerings processen för appen samlar in och tilldelar några värden till appen:

* Ett **program-ID** som unikt identifierar din app
* En **omdirigerings-URI** (valfritt) som kan användas för att dirigera svar tillbaka till din app
* Några andra scenario-/regionsspecifika värden.

Om du vill ha mer information lär du dig hur du [registrerar en app](quickstart-register-app.md).

## <a name="endpoints"></a>Slutpunkter

När appen har registrerats kommunicerar den med Microsoft Identity Platform genom att skicka begär anden till slut punkten:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Där `{tenant}` kan ett av fyra olika värden göras:

| Värde | Beskrivning |
| --- | --- |
| `common` | Gör att användare med både personliga Microsoft-konton och arbets-/skol konton från Azure AD kan logga in på programmet. |
| `organizations` | Tillåter endast användare med arbets-/skol konton från Azure AD att logga in på programmet. |
| `consumers` | Tillåter endast användare med personliga Microsoft-konton (MSA) att logga in i programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Tillåter endast användare med arbets-/skol konton från en viss Azure AD-klient för att logga in på programmet. Antingen det egna domän namnet för Azure AD-klienten eller klient organisationens GUID-identifierare kan användas. |

Om du vill lära dig hur du interagerar med dessa slut punkter väljer du en viss typ av app i avsnittet [protokoll](#protocols) och följer länkarna för mer information.

> [!TIP]
> Alla appar som är registrerade i Azure AD kan använda Microsofts identitets plattform, även om de inte loggar in personliga konton.  På så sätt kan du migrera befintliga program till Microsoft Identity Platform och [MSAL](reference-v2-libraries.md) utan att skapa programmet på nytt.

## <a name="tokens"></a>Token

OAuth 2,0 och OpenID Connect gör en omfattande användning av **Bearer-token**, som vanligt vis visas som [JWTs (JSON Web tokens)](https://tools.ietf.org/html/rfc7519). En Bearer-token är en förenklad säkerhetstoken som ger "Bearer"-åtkomst till en skyddad resurs. I detta mening är "innehavare" alla som hämtar en kopia av token. Även om en part först måste autentisera med Microsoft Identity Platform för att ta emot Bearer-token, om de nödvändiga stegen inte vidtas för att skydda token i överföring och lagring, kan den fångas upp och användas av en ej avsedd part. Även om vissa säkerhetstoken har en inbyggd mekanism för att förhindra att obehöriga parter använder dem, har inte Bearer-token denna mekanism och måste transporteras i en säker kanal, till exempel Transport Layer Security (HTTPS). Om en Bearer-token överförs i klartext kan en angripare använda en person-i-ett-mellan-angrepp för att hämta token och använda den för obehörig åtkomst till en skyddad resurs. Samma säkerhets principer gäller när du lagrar eller cachelagrar Bearer-token för senare användning. Se alltid till att appen skickar och lagrar Bearer-token på ett säkert sätt. Mer information om säkerhets aspekter på Bearer-token finns i [RFC 6750 avsnitt 5](https://tools.ietf.org/html/rfc6750).

Det finns huvudsakligen tre typer av tokens som används i OAuth 2,0/OIDC:

* [Åtkomsttoken – tokens](access-tokens.md) som en resurs server tar emot från en klient, som innehåller behörigheter som klienten har beviljats.  
* [ID-token](id-tokens.md) -token som en klient tar emot från auktoriseringsservern, som används för att logga in en användare och få grundläggande information om dem.
* Uppdatera tokens – används av en klient för att få nya åtkomst-och ID-token över tid.  Detta är ogenomskinliga strängar och kan bara förstås av auktoriseringsservern.

## <a name="protocols"></a>Protokoll

Kom igång med något av protokoll dokumenten nedan om du är redo att se några exempel förfrågningar. Var och en motsvarar ett visst scenario för autentisering. Om du behöver hjälp med att fastställa vilket som är rätt flöde åt dig kan du titta närmare [på de typer av appar som du kan bygga med Microsoft Identity Platform](v2-app-types.md).

* [Bygg mobilt, ursprungligt och webb program med OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Logga in användare med OpenID Connect](v2-protocols-oidc.md)
* [Bygg daemons eller Server sidans processer med OAuth 2,0-klientens autentiseringsuppgifter Flow](v2-oauth2-client-creds-grant-flow.md)
* [Hämta token i ett webb-API med OAuth 2,0 på uppdrag av Flow](v2-oauth2-on-behalf-of-flow.md)
* [Bygg appar med en sida med det implicita flödet i OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
