---
title: OAuth-flöde för implicit beviljande av OAuth-2,0 – Microsoft Identity Platform | Azure
description: Skydda appar med en enda sida med hjälp av implicit flöde för Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226496"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform och implicit beviljande flöde

Microsoft Identity Platform stöder OAuth-flödet för implicit beviljande av OAuth 2,0 enligt beskrivningen i [OAuth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.2). Den implicita tilldelningens definitions egenskaper är att tokens (ID-token eller åtkomsttoken) returneras direkt från/Authorize-slutpunkten i stället för/token-slutpunkten. Detta används ofta som en del av [koden för auktoriseringskod](v2-oauth2-auth-code-flow.md), i det här kallas "hybrid Flow" – hämtning av ID-token på/Authorize-begäran tillsammans med en auktoriseringskod.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Föredra flödet för auth Code

Med planer för [cookies från tredje part som ska tas bort från webbläsare](reference-third-party-cookies-spas.md) **är det implicita tilldelnings flödet inte längre en lämplig autentiseringsmetod**.  De [tysta SSO-funktionerna](#getting-access-tokens-silently-in-the-background) i det implicita flödet fungerar inte utan cookies från tredje part, vilket gör att programmen bryts när de försöker hämta en ny token. Vi rekommenderar starkt att alla nya program använder det [flöde för auktoriseringskod](v2-oauth2-auth-code-flow.md) som nu har stöd för appar med en sida i stället för det implicita flödet och att [befintliga appar för en sida börjar migrera till Authorization Code Flow](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2 implicita bidrag

Den implicita beviljandet är bara tillförlitligt för den första interaktiva delen av ditt inloggnings flöde, där det inte går att använda [cookies från tredje part](reference-third-party-cookies-spas.md) för att påverka ditt program. Den här begränsningen innebär att du bör använda den exklusivt som en del av hybrid flödet, där programmet begär en kod och en token från slut punkten för auktorisering. Detta säkerställer att ditt program får en kod som kan lösas in för en uppdateringstoken, vilket säkerställer att appens inloggnings session förblir giltig över tid.

## <a name="protocol-diagram"></a>Protokoll diagram

Följande diagram visar hur hela det implicita inloggnings flödet ser ut och de avsnitt som följer beskriver varje steg i detalj.

![Diagram som visar det implicita inloggnings flödet](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

För att först signera användaren till din app kan du skicka en [OpenID Connect](v2-protocols-oidc.md) -autentiseringsbegäran och hämta en `id_token` från Microsoft Identity Platform.

> [!IMPORTANT]
> För att kunna begära en ID-token och/eller en åtkomsttoken måste appens registrering på sidan [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) ha motsvarande implicit tilldelnings flöde aktiverat, genom att välja **ID-tokens** och **åtkomsttoken** i avsnittet **implicit beviljande och hybrid flöden** . Om den inte är aktive rad `unsupported_response` returneras ett fel: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Om du vill testa att logga in med det implicita flödet klickar du på <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> När du har loggat in bör webbläsaren omdirigeras till `https://localhost/myapp/` med ett `id_token` i adress fältet.
>

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| `tenant` | krävs |`{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | krävs | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `response_type` | krävs |Måste inkludera `id_token` för OpenID Connect-inloggning. Den kan även innehålla response_type `token` . Om `token` du använder detta kan din app ta emot en åtkomsttoken omedelbart från den auktoriserade slut punkten utan att behöva göra en andra begäran till behörighets slut punkten. Om du använder `token` response_type `scope` måste parametern innehålla ett omfång som anger vilken resurs som ska utfärda token för (till exempel User. read på Microsoft Graph). Det kan också finnas `code` i stället för `token` att tillhandahålla en auktoriseringskod för användning i [Authorization Code Flow](v2-oauth2-auth-code-flow.md). Det här id_token + kod svaret kallas ibland hybrid flöde.  |
| `redirect_uri` | rekommenderas |Appens redirect_uri, där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. |
| `scope` | krävs |En blankstegsavgränsad lista över [omfång](v2-permissions-and-consent.md). För OpenID Connect (id_tokens) måste den innehålla omfånget `openid` , som översätts till behörigheten "logga in dig" i medgivande gränssnittet. Om du vill kan du även inkludera- `email` och- `profile` omfattningarna för att få åtkomst till ytterligare användar data. Du kan också inkludera andra omfattningar i denna begäran om att begära medgivande till olika resurser, om en åtkomsttoken begärs. |
| `response_mode` | valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Standardvärdet söker efter en åtkomsttoken, men fragment om begäran innehåller en id_token. |
| `state` | rekommenderas |Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `nonce` | krävs |Ett värde som ingår i begäran, som genereras av appen, som kommer att ingå i den resulterande id_token som ett anspråk. Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. Krävs endast när en id_token begärs. |
| `prompt` | valfri |Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är ' inloggning "', ' none ', ' select_account ' och ' medgivande '. `prompt=login` tvingar användaren att ange sina autentiseringsuppgifter för den begäran och negera enkel inloggning. `prompt=none` är motsatt – det ser till att användaren inte visas med interaktiva prompter. Om begäran inte kan slutföras i bakgrunden via enkel inloggning, returnerar Microsoft Identity Platform ett fel. `prompt=select_account` skickar användaren till en konto väljare där alla konton som lagras i sessionen kommer att visas. `prompt=consent` utlöser dialog rutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörighet till appen. |
| `login_hint`  |valfri |Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar kommer ofta att använda den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med `preferred_username` anspråket.|
| `domain_hint` | valfri |Om den är inkluderad hoppar den e-postbaserad identifierings processen som användaren går igenom på inloggnings sidan, vilket leder till en något mer strömlinjeformad användar upplevelse. Den här parametern används ofta för branschspecifika appar som fungerar i en enda klient, där de kommer att tillhandahålla ett domän namn inom en viss klient, och vidarebefordra användaren till Federations leverantören för den klienten.  Observera att det här tipset förhindrar att gäster loggar in i det här programmet och begränsar användningen av autentiseringsuppgifter för moln som FIDO.  |

Användaren uppmanas att ange sina autentiseringsuppgifter och slutföra autentiseringen. Microsoft Identity Platform ser också till att användaren har samtyckt till de behörigheter som anges i `scope` Frågeparametern. Om användaren har samtyckt till **ingen** av dessa behörigheter uppmanas användaren att godkänna de behörigheter som krävs. Mer information finns i [behörigheter, medgivande och appar för flera klient organisationer](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner godkännandet, returnerar Microsoft Identity Platform ett svar till din app vid angiven `redirect_uri` användning av metoden som anges i `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` och `response_type=id_token+code` ser ut ungefär så här (med rad brytningar för läsbarhet):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `code` | Ingår om `response_type` innehåller `code` . Det här är en auktoriseringskod som är lämplig för användning i [Authorization Code Flow](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Ingår om `response_type` innehåller `token` . Den åtkomsttoken som appen begärde. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` |Ingår om `response_type` innehåller `token` . Är alltid `Bearer` . |
| `expires_in`|Ingår om `response_type` innehåller `token` . Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` |Ingår om `response_type` innehåller `token` . Anger omfånget som access_token ska gälla för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md) . <br> **Obs:** Anges endast om `openid` omfång begärdes och `response_type` inkluderades `id_tokens` . |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till så att `redirect_uri` appen kan hantera dem på rätt sätt:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Hämta åtkomsttoken tyst i bakgrunden

> [!Important]
> Den här delen av det implicita flödet är sannolikt inte att fungera för ditt program eftersom det används i olika webbläsare på grund av [borttagning av cookies från tredje part som standard](reference-third-party-cookies-spas.md).  Även om det fortfarande arbetar i Krombaserade webbläsare som inte finns i Incognito bör utvecklare överväga att använda den här delen av flödet. I webbläsare som inte stöder cookies från tredje part får du ett fel meddelande som anger att inga användare är inloggade, eftersom inloggnings sidans sessionscookies togs bort av webbläsaren. 

Nu när du har loggat in användaren i en app med en enda sida kan du hämta åtkomsttoken för att anropa webb-API: er som skyddas av Microsoft Identity Platform, t. ex. [Microsoft Graph](https://developer.microsoft.com/graph). Även om du redan har tagit emot en token med hjälp av `token` response_type kan du använda den här metoden för att hämta tokens till ytterligare resurser utan att behöva omdirigera användaren för att logga in igen.

I det normala OpenID Connect/OAuth-flödet gör du detta genom att göra en begäran till Microsoft Identity Platform- `/token` slutpunkten. Du kan göra en begäran i en dold iframe för att hämta nya token för andra webb-API: er:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Information om frågeparametrar i URL: en finns i [Skicka inloggnings förfrågan](#send-the-sign-in-request).

> [!TIP]
> Försök kopiera & att klistra in nedanstående begäran i en webbläsare-flik! (Glöm inte att ersätta `login_hint` värdena med rätt värde för din användare)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Observera att detta fungerar även i webbläsare utan stöd för cookies från tredje part, eftersom du anger det direkt i ett webb läsar fält i stället för att öppna det i en iframe. 

Tack vare `prompt=none` parametern kommer den här begäran antingen att lyckas eller Miss lyckas omedelbart och återgå till ditt program. Svaret skickas till din app `redirect_uri` med den angivna metoden i `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` ser ut så här:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Ingår om `response_type` innehåller `token` . Den åtkomsttoken som appen begärde, i det här fallet för Microsoft Graph. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` | Är alltid `Bearer` . |
| `expires_in` | Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` | Anger omfånget som access_token ska gälla för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Ingår om `response_type` innehåller `id_token` . Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [ `id_token` referensen](id-tokens.md). <br> **Obs:** Anges endast om `openid` omfattning begärdes. |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till så att `redirect_uri` appen kan hantera dem på rätt sätt. Om `prompt=none` ett förväntat fel är:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren interaktivt logga in igen för att hämta en ny token. Du kan välja att hantera det här fallet på det sätt som passar ditt program.

## <a name="refreshing-tokens"></a>Uppdatera token

Den implicita beviljandet tillhandahåller inte uppdaterade tokens. Både `id_token` och s `access_token` upphör att gälla efter en kort tids period, så din app måste vara för beredd för att uppdatera dessa token regelbundet. Om du vill uppdatera någon av typerna av token kan du utföra samma dolda iframe-begäran från ovan med `prompt=none` parametern för att styra identitets plattformens beteende. Om du vill ta emot en ny `id_token` måste du använda `id_token` i `response_type` och `scope=openid` , samt en `nonce` parameter.

I webbläsare som inte stöder cookies från tredje part leder detta till ett fel som anger att ingen användare är inloggad. 

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning

OpenID Connect `end_session_endpoint` gör att din app kan skicka en begäran till Microsoft Identity Platform för att avsluta en användares session och Rensa cookies som anges av Microsoft Identity Platform. För att fullständigt signera en användare från ett webb program bör din app avsluta sin egen session med användaren (vanligt vis genom att rensa en token-cache eller släppa cookies) och sedan omdirigera webbläsaren till:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| `tenant` |krävs |`{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | rekommenderas | URL: en som användaren ska returneras till när utloggning har slutförts. Värdet måste matcha en av de omdirigerings-URI: er som registrerats för programmet. Om den inte ingår visas ett allmänt meddelande från Microsoft Identity Platform. |

## <a name="next-steps"></a>Nästa steg

* Gå över [MSAL JS-exemplen](sample-v2-code.md) för att komma igång med kodning.
* Granska [koden för auktoriseringskod](v2-oauth2-auth-code-flow.md) som ett nyare, bättre alternativ till den implicita tilldelningen. 
