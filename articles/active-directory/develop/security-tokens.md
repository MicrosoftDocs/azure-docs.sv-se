---
title: Säkerhetstoken | Azure
titleSuffix: Microsoft identity platform
description: Lär dig grunderna om säkerhetstoken i Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 9668d3b0b57e36fb95421f8b502b9b743be8eb31
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756218"
---
# <a name="security-tokens"></a>Säkerhetstoken

En centraliserad identitets leverantör är särskilt användbar för appar som har användare som finns i hela världen och som inte nödvändigt vis loggar in från företagets nätverk. Microsoft Identity Platform autentiserar användare och ger säkerhetstoken, till exempel [åtkomsttoken](developer-glossary.md#access-token), [uppdateringstoken](developer-glossary.md#refresh-token)och [ID-token](developer-glossary.md#id-token), som gör att ett [klient program](developer-glossary.md#client-application) kan komma åt skyddade resurser på en [resurs Server](developer-glossary.md#resource-server).

En **åtkomsttoken är en säkerhetstoken som** utfärdas av en [Authorization-Server](developer-glossary.md#authorization-server) som en del av ett [OAuth 2,0](active-directory-v2-protocols.md) -flöde. Den innehåller information om användaren och appen som token är avsedd för. som kan användas för att få åtkomst till webb-API: er och andra skyddade resurser. Mer information om hur Microsoft Identity Platform utfärdar åtkomst-token finns i [åtkomsttoken](access-tokens.md).

Åtkomst-token är bara giltiga under en kort tids period, så att auktoriseringsdata ibland utfärdar en **uppdateringstoken** på samma gång som åtkomsttoken utfärdas. Klient programmet kan sedan byta ut denna uppdateringstoken för en ny åtkomsttoken vid behov. Mer information om hur Microsoft Identity Platform använder Refresh tokens för att återkalla behörigheter finns i återkalla [token](access-tokens.md#token-revocation).

**ID-token** skickas till klient programmet som en del av ett [OpenID Connect](v2-protocols-oidc.md) -flöde. De kan skickas på sidan eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren. Mer information om hur Microsoft Identity Platform utfärdar ID-token finns i [ID-token](id-tokens.md).

> [!NOTE]
> Den här artikeln beskriver säkerhetstoken som används av OAuth2-och OpenID Connect-protokollen. Många företags program använder SAML för att autentisera användare. Se [referens för Azure AD SAML-token](reference-saml-tokens.md) för information om SAML-kontroller.

## <a name="validating-security-tokens"></a>Verifierar säkerhetstoken

Det är upp till appen för vilken token har skapats, webbappen som signerade användaren eller webb-API: et som anropades för att validera token. Token signeras av säkerhetstokentjänst **(STS)** med en privat nyckel. STS publicerar motsvarande offentliga nyckel. För att validera en token verifierar appen signaturen med hjälp av den offentliga STS-nyckeln för att verifiera att signaturen skapades med hjälp av den privata nyckeln.

Tokens är bara giltiga under en begränsad tid. STS tillhandahåller vanligt vis ett par med token:

* En åtkomsttoken för att komma åt programmet eller den skyddade resursen, och
* En uppdateringstoken som används för att uppdatera åtkomst-token när åtkomsttoken ligger nära att gå ut.

Åtkomsttoken skickas till ett webb-API som Bearer-token i `Authorization` huvudet. En app kan tillhandahålla en uppdateringstoken till STS, och om användarens åtkomst till appen inte har återkallats kommer den att få tillbaka en ny åtkomsttoken och en ny uppdateringstoken. Detta är hur scenariot för någon som lämnar företaget hanteras. När STS tar emot uppdateringstoken, utfärdar den ingen annan giltig åtkomsttoken om användaren inte längre är auktoriserad.

## <a name="json-web-tokens-jwts-and-claims"></a>JSON-webbtoken (JWTs) och anspråk

Microsoft Identity Platform implementerar säkerhetstoken som **JSON-Webbtoken (JWTs)** som innehåller **anspråk**. Eftersom JWTs används som säkerhetstoken kallas den här typen av autentisering även **JWT-autentisering**.

Ett [anspråk](developer-glossary.md#claim) ger intyg om en entitet, till exempel ett klient program eller en [resurs ägare](developer-glossary.md#resource-owner), till en annan entitet, till exempel en resurs Server. Ett anspråk kan också kallas för ett JWT-anspråk eller JSON Web Token-anspråk.

Anspråk är namn/värde-par som vidarebefordrar fakta om ämnets token. Ett anspråk kan till exempel innehålla fakta om säkerhets objekt som autentiserats av auktoriseringsservern. De anspråk som förekommer i en specifik token är beroende av många saker, inklusive typen av token, vilken typ av autentiseringsuppgift som används för att autentisera ämnet, program konfigurationen och så vidare.

Program kan använda anspråk för olika uppgifter, till exempel:

* Token verifieras
* Identifierar [innehavarens](developer-glossary.md#tenant) token-innehavare
* Visar användar information
* Avgöra ämnets auktorisering

Ett anspråk består av nyckel/värde-par som innehåller information som:

* Säkerhetstoken som genererade token
* Datum när token genererades
* Ämne (till exempel användare--utom för daemon)
* Mål grupp, som är appen för vilken token genererades
* App (klienten) som bad om token. Om det gäller Web Apps kan detta vara samma som mål gruppen

Mer information om hur Microsoft Identity Platform implementerar token och anspråks information [finns i åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiserings flöden som stöds av Microsoft Identity Platform. Dessa flöden kan producera en mängd olika tokens (ID-token, Refresh tokens, åtkomsttoken) samt auktoriseringsregler och kräver olika token för att de ska fungera. Det här diagrammet innehåller en översikt:

|Flöden | Innebär | ID-token | åtkomsttoken | uppdatera token | auktoriseringskod |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicit flöde](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v2-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) | | | x (endast app-only)| | |

Token som utfärdas via det implicita läget har en längd begränsning på grund av att de skickas tillbaka till webbläsaren via URL: en (där `response_mode` är `query` eller `fragment` ).  Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång.  Detta innebär att dessa tokens inte har `groups` eller är `wids` anspråk.

## <a name="next-steps"></a>Nästa steg

För andra ämnen som behandlar grundläggande autentisering och auktorisering:

* Se [autentisering kontra auktorisering](authentication-vs-authorization.md) för att lära dig mer om grundläggande begrepp för autentisering och auktorisering i Microsoft Identity Platform.
* Se [program modell](application-model.md) för att lära dig mer om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform.
* Se [appens inloggnings flöde](app-sign-in-flow.md) för att lära dig om inloggnings flödet för webben, skriv bordet och mobila appar i Microsoft Identity Platform.
