---
title: Hämta &-cache-tokens med Microsoft Authentication Library (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att förvärva och cachelagra token med MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755071"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Hämta och cachelagra token med Microsoft Authentication Library (MSAL)

[Åtkomsttoken gör det](access-tokens.md) möjligt för klienter att på ett säkert sätt anropa webb-API: er som skyddas av Azure. Det finns flera sätt att hämta en token med hjälp av Microsoft Authentication Library (MSAL). Vissa kräver användar interaktion via en webbläsare, medan andra inte kräver användar interaktion. I allmänhet är metoden som används för att hämta en token beroende av om programmet är ett offentligt klient program som stationär eller mobilapp, eller ett konfidentiellt klient program som Web App, webb-API eller daemon-program.

MSAL cachelagrar en token efter att den har köpts. Program koden bör först försöka hämta en token från cachen innan du försöker hämta en token på annat sätt.

Du kan också rensa token-cachen, som uppnås genom att ta bort kontona från cachen. Detta tar inte bort sessionens cookie i webbläsaren.

## <a name="scopes-when-acquiring-tokens"></a>Omfattningar vid hämtning av token

[Omfattningar](v2-permissions-and-consent.md) är de behörigheter som ett webb-API visar att klient programmen kan begära åtkomst till. Klient program begär användarens medgivande för dessa omfattningar när de gör autentiseringsbegäranden för att hämta token för att få åtkomst till webb-API: er. Med MSAL kan du hämta token för att få åtkomst till Azure AD för utvecklare (v 1.0) och API: er för Microsoft Identity Platform. v 2.0-protokollet använder omfång i stället för resurs i begär Anden. Mer information finns i [jämförelse mellan v 1.0 och v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Baserat på webb-API: ns konfiguration av den token-version som den accepterar, returnerar v 2.0-slutpunkten åtkomsttoken till MSAL.

Flera av MSAL för token-hämtning kräver en `scopes` parameter. `scopes`Parametern är en lista med strängar som deklarerar önskade behörigheter och de begärda resurserna. Välkända omfattningar är [Microsoft Graph behörigheter](/graph/permissions-reference).

Det går också att komma åt v 1.0-resurser med MSAL. Mer information finns i [omfattningar för ett v 1.0-program](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Begär omfång för ett webb-API

När programmet måste begära en åtkomsttoken med specifika behörigheter för ett resurs-API skickar du de omfattningar som innehåller app-ID-URI: n för API: et i formatet `<app ID URI>/<scope>` .

Några exempel på omfattnings värden för olika resurser:

- Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- Anpassat webb-API: `api://11111111-1111-1111-1111-111111111111/api.read`

Formatet för omfattnings värdet varierar beroende på resurs (API) som tar emot åtkomsttoken och de `aud` anspråks värden som det accepterar.

För Microsoft Graph är `user.read` omfånget mappat till `https://graph.microsoft.com/User.Read` , och båda omfångs format kan användas utbytbart.

Vissa webb-API: er, till exempel Azure Resource Manager API ( https://management.core.windows.net/) förväntar sig ett avslutande snedstreck ("/") i mål grupps anspråket () i åtkomsttoken `aud` . I detta fall ska du skicka omfånget som `https://management.core.windows.net//user_impersonation` , inklusive det dubbla snedstrecket ("//").

Andra API: er kan kräva att det *inte finns något schema eller en värd* i värdet för omfånget, och förväntar sig endast app-ID (ett GUID) och omfångs namnet, till exempel:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Om den underordnade resursen inte finns under kontrollen kan du behöva testa olika omfattnings värde format (till exempel med/utan schema och värd) om du får `401` eller andra fel när du skickar åtkomsttoken till resursen.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Begär dynamiska omfattningar för stegvist godkännande

Som de funktioner som tillhandahålls av ditt program eller dess krav ändras kan du begära ytterligare behörigheter genom att använda omfattnings parametern. Sådana *dynamiska omfattningar* gör det möjligt för användarna att tillhandahålla ett stegvist tillstånd för omfattningar.

Du kan t. ex. Logga in användaren, men neka dem åtkomst till resurser. Senare kan du ge dem möjlighet att visa sin kalender genom att begära kalender omfånget i metoden Hämta token och erhålla användarens medgivande. Till exempel genom att begära- `https://graph.microsoft.com/User.Read` och- `https://graph.microsoft.com/Calendar.Read` omfattningarna.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Hämtar token tyst (från cachen)

MSAL upprätthåller ett token-cache (eller två cacheminnen för konfidentiella klient program) och cachelagrar en token efter att den har köpts. I många fall kommer ett försök att hämta en token att hämta en annan token med fler omfång baserat på en token i cacheminnet. Du kan också uppdatera en token när den snart upphör att gälla (eftersom token cache också innehåller en uppdateringstoken).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Rekommenderat anrops mönster för offentliga klient program

Program koden bör först försöka hämta en token från cachen. Om metod anropet returnerar ett "gränssnitt som krävs" fel eller undantag, försök att förvärva en token på annat sätt.

Det finns två flöden, men du **bör inte** försöka att hämta en token i bakgrunden:

- [Flöde](msal-authentication-flows.md#client-credentials)för klientautentiseringsuppgifter, som inte använder token-cachen, men som saknar cache för programtoken. Den här metoden tar hand om verifieringen av programtoken-cachen innan en begäran skickas till säkerhetstokentjänst.
- [Flödes kods flöde](msal-authentication-flows.md#authorization-code) i webbappar, eftersom det löser in en kod som programmet har erhållit genom att logga in användaren och låta dem godkänna fler omfattningar. Eftersom en kod och inte ett konto skickas som en parameter kan metoden inte söka i cachen innan den löser in koden, vilket anropar ett anrop till tjänsten.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Rekommenderat anrops mönster i webbappar med hjälp av kod flödet för auktorisering

För webb program som använder [OpenID Anslut auktoriseringskod](v2-protocols-oidc.md), är det rekommenderade mönstret i styrenheterna att:

- Instansiera ett konfidentiellt klient program med token cache med anpassad serialisering.
- Hämta token med hjälp av Authorization Code Flow

## <a name="acquiring-tokens"></a>Hämtar token

Metoden för att förvärva en token är vanligt vis beroende av om det är en offentlig klient eller ett konfidentiellt klient program.

### <a name="public-client-applications"></a>Offentliga klient program

För offentliga klient program (skriv bord eller mobilapp):

- Kan ofta hämta token interaktivt, där användaren loggar in via ett användar gränssnitt eller popup-fönster.
- Kan [Hämta en token tyst för den inloggade användaren](msal-authentication-flows.md#integrated-windows-authentication) med hjälp av integrerad Windows-autentisering (IWA/Kerberos) om Skriv bords programmet körs på en Windows-dator som är ansluten till en domän eller till Azure.
- Kan [Hämta en token med ett användar namn och lösen ord](msal-authentication-flows.md#usernamepassword) i .NET Framework Desktop Client-program (rekommenderas inte). Använd inte användar namn/lösen ord i konfidentiella klient program.
- Kan hämta en token via [enhets kod flödet](msal-authentication-flows.md#device-code) i program som körs på enheter som saknar webbläsare. Användaren får en URL och en kod som sedan går till en webbläsare på en annan enhet och anger koden och loggar in. Azure AD skickar sedan en token tillbaka till webbläsaren-mindre enhet.

### <a name="confidential-client-applications"></a>Konfidentiella klient program

För konfidentiella klient program (webbapp, webb-API eller ett daemon-program som en Windows-tjänst):

- Hämta token **för själva programmet** och inte för en användare med hjälp av [flödet för klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials). Den här tekniken kan användas för att synkronisera verktyg, eller verktyg som behandlar användare i allmänhet och inte en speciell användare.
- Använd [flödet på uppdrags nivå](msal-authentication-flows.md#on-behalf-of) för ett webb-API för att anropa ett API för användarens räkning. Programmet identifieras med klientautentiseringsuppgifterna för att hämta en token baserat på en användar kontroll (SAML, till exempel eller en JWT-token). Det här flödet används av program som behöver åtkomst till resurser för en viss användare i tjänst-till-tjänst-anrop.
- Hämta token med hjälp av [auktoriseringskod-flödet](msal-authentication-flows.md#authorization-code) i webbappar när användaren loggar in via URL: en för begäran om auktorisering. OpenID Connect Application använder vanligt vis den här mekanismen, vilket gör att användaren kan logga in med öppen ID Connect och sedan få åtkomst till webb-API: er för användarens räkning.

## <a name="authentication-results"></a>Verifierings resultat

När klienten begär en åtkomsttoken, returnerar Azure AD även ett verifierings resultat som innehåller metadata om åtkomsttoken. Den här informationen omfattar förfallo tiden för åtkomsttoken och de omfattningar som den är giltig för. Med den här informationen kan din app utföra intelligent cachelagring av åtkomsttoken utan att behöva parsa åtkomsttoken. Resultatet av autentiseringen visar:

- Åtkomsttoken [för webb](access-tokens.md) -API: et för att få åtkomst till resurser. Den här strängen är vanligt vis en Base64-kodad JWT, men klienten bör aldrig titta inuti åtkomsttoken. Formatet är inte garanterat vara stabilt och kan krypteras för resursen. Användare som skriver kod, beroende på åtkomsttoken på klienten, är en av de vanligaste källorna till fel och brott mot klient logiken.
- [ID-token](id-tokens.md) för användaren (en JWT).
- Förfallo tiden för token, som anger det datum/tid när token upphör att gälla.
- Klient-ID: t innehåller innehavaren där användaren hittades. För gäst användare (Azure AD B2B-scenarier) är klient-ID: t gäst klienten, inte den unika klient organisationen. När token levereras i namnet på en användare innehåller verifierings resultatet även information om den här användaren. För konfidentiella klient flöden där tokens begärs utan användare (för programmet) är denna användar information null.
- De omfattningar som token utfärdades för.
- Unikt ID för användaren.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Erfar Åtkomst till användarens cachelagrade token i appar och tjänster i bakgrunden

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Nästa steg

Flera av plattformarna som stöds av MSAL har ytterligare token cache-relaterad information i dokumentationen för plattformens bibliotek. Exempel:
- [Hämta en token från token-cachen med MSAL.NET](msal-net-acquire-token-silently.md)
- [Enkel inloggning med MSAL.js](msal-js-sso.md)
- [Anpassad token cache-serialisering i MSAL för python](msal-python-token-cache-serialization.md)
- [Anpassad token cache-serialisering i MSAL för Java](msal-java-token-cache-serialization.md)
