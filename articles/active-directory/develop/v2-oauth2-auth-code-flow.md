---
title: Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow | Azure
titleSuffix: Microsoft identity platform
description: Bygg webb program med hjälp av Microsoft Identity Platform-implementeringen av autentiseringsprotokollet OAuth 2,0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6cf9f7a005a80ab34e05ee293c20209e9d0b3f01
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258585"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow

OAuth 2,0 Authorization Code Grant kan användas i appar som är installerade på en enhet för att få åtkomst till skyddade resurser, till exempel webb-API: er. Med hjälp av Microsoft Identity Platform-implementeringen av OAuth 2,0 kan du lägga till inloggnings-och API-åtkomst till dina mobila och Station ära appar.

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program med valfritt språk.  När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

OAuth 2,0 Authorization Code Flow beskrivs i [avsnittet 4,1 i oauth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749). Den används för att utföra autentisering och auktorisering i de flesta typer av appar, inklusive [appar för en sida](v2-app-types.md#single-page-apps-javascript), [webbappar](v2-app-types.md#web-apps)och [internt installerade appar](v2-app-types.md#mobile-and-native-apps). Flödet gör det möjligt för appar att på ett säkert sätt förvärva access_tokens som kan användas för att få åtkomst till resurser som skyddas av Microsoft Identity Platform-slutpunkten, samt att uppdatera tokens för att få ytterligare access_tokens och ID-token för den inloggade användaren.

## <a name="protocol-diagram"></a>Protokoll diagram

På hög nivå ser hela autentiseringsläget för ett program ut ungefär så här:

![Kod flöde för OAuth-auth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Omdirigerings-URI-installation krävs för appar med en sida

Flödes kods flödet för enstaka sidor kräver ytterligare konfiguration.  Följ anvisningarna för att [skapa ett program med en sida](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) för att markera omdirigerings-URI: n som aktive rad för CORS. Om du vill uppdatera en befintlig omdirigerings-URI för att aktivera CORS öppnar du manifest redigeraren och anger `type` fältet för omdirigerings-URI: n till `spa` i `replyUrlsWithType` avsnittet. Du kan också klicka på omdirigerings-URI: n i avsnittet "webb" på fliken autentisering och välja de URI: er som du vill migrera till med hjälp av kod flödet för auktorisering.

`spa`Omdirigerings typen är bakåtkompatibel med det implicita flödet. Appar som för närvarande använder det implicita flödet för att hämta token kan flyttas till den `spa` omdirigerings-URI-typen utan problem och fortsätta använda det implicita flödet.

Om du försöker använda auktoriseringskod-flödet och se det här felet:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Sedan måste du besöka appens registrering och uppdatera omdirigerings-URI: n för att din app ska kunna skriva `spa` .

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod

Kod flödet för auktorisering börjar med klienten som dirigerar användaren till `/authorize` slut punkten. I den här begäran begär klienten `openid` `offline_access` `https://graph.microsoft.com/mail.read ` behörigheterna, och från användaren.  Vissa behörigheter är administratörs begränsade, till exempel att skriva data till en organisations katalog med hjälp av `Directory.ReadWrite.All` . Om programmet begär åtkomst till någon av de här behörigheterna från en organisations användare får användaren ett fel meddelande om att de inte har behörighet att godkänna appens behörigheter. Om du vill begära åtkomst till administrations begränsade omfattningar bör du begära dem direkt från en företags administratör.  Mer information finns i [Administratörs begränsad behörighet](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Klicka på länken nedan för att utföra den här begäran! När du har loggat in bör webbläsaren omdirigeras till `https://localhost/myapp/` med en `code` i adress fältet.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Obligatorisk/valfri | Beskrivning |
|--------------|-------------|--------------|
| `tenant`    | krävs    | `{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | krävs    | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app.  |
| `response_type` | krävs    | Måste innehålla `code` för flödet av auktoriseringskod.       |
| `redirect_uri`  | krävs | Appens redirect_uri, där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. Använd standardvärdet för interna & mobila appar `https://login.microsoftonline.com/common/oauth2/nativeclient` .   |
| `scope`  | krävs    | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md) som du vill att användaren ska godkänna.  För en del `/authorize` av begäran kan detta avse flera resurser, så att din app får tillåtelse för flera webb-API: er som du vill anropa. |
| `response_mode`   | rekommenderas | Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Kan vara något av följande:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` innehåller koden som en frågesträngparametern i omdirigerings-URI: n. Om du begär en ID-token med det implicita flödet kan du inte använda `query` enligt vad som anges i [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du bara begär koden kan du använda `query` , `fragment` , eller `form_post` . `form_post` kör ett inlägg som innehåller koden för omdirigerings-URI: n. Mer information finns i [OpenID Connect-protokollet](../azuread-dev/v1-protocols-openid-connect-code.md).  |
| `state`                 | rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Värdet kan också koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `prompt`  | valfri    | Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är `login` , `none` och `consent` .<br/><br/>- `prompt=login` tvingar användaren att ange sina autentiseringsuppgifter för den begäran och negera enkel inloggning.<br/>- `prompt=none` är motsatt – det ser till att användaren inte visas med interaktiva prompter. Om begäran inte kan slutföras i bakgrunden via enkel inloggning, returnerar slut punkten för Microsoft Identity Platform ett `interaction_required` fel.<br/>- `prompt=consent` utlöser dialog rutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörighet till appen.<br/>- `prompt=select_account` avbryter enkel inloggning med konto val som visar alla konton antingen i en session eller ett Sparad konto eller ett alternativ för att välja att använda ett annat konto helt och hållet.<br/> |
| `login_hint`  | valfri    | Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar kommer ofta att använda den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med hjälp av `preferred_username` anspråket.   |
| `domain_hint`  | valfri    | Om den är inkluderad hoppar den e-postbaserad identifierings processen som användaren går igenom på inloggnings sidan, vilket leder till en något mer effektiviserad användar upplevelse, till exempel att skicka dem till sin federerade identitets leverantör. Appar använder ofta den här parametern vid omautentisering genom att extrahera `tid` från en tidigare inloggning. Om `tid` anspråk svärdet är `9188040d-6c67-4c5b-b112-36a304b66dad` ska du använda `domain_hint=consumers` . Annars använder du `domain_hint=organizations` .  |
| `code_challenge`  | rekommenderas/krävs | Används för att skydda auktoriseringskod-bidrag via bevis nyckel för Code Exchange (PKCE). Krävs om ingår `code_challenge_method` . Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). Det rekommenderas nu för alla program typer – inbyggda appar, SPAs och konfidentiella klienter som Web Apps. |
| `code_challenge_method` | rekommenderas/krävs | Den metod som används för att koda `code_verifier` för `code_challenge` parametern. Detta *bör* vara `S256` , men i specifikationen kan du använda `plain` IF av någon anledning till att klienten inte stöder SHA256. <br/><br/>Om det utesluts, `code_challenge` antas vara oformaterad text om `code_challenge` ingår. Microsoft Identity Platform stöder både `plain` och `S256` . Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). Detta krävs för [appar med en sida med hjälp av flödes kods flödet](reference-third-party-cookies-spas.md).|


Användaren uppmanas att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slut punkten för Microsoft Identity Platform ser också till att användaren har samtyckt till de behörigheter som anges i `scope` Frågeparametern. Om användaren inte har samtyckt till någon av dessa behörigheter uppmanas användaren att godkänna de behörigheter som krävs. Information om [behörigheter, medgivande och appar för flera klient organisationer finns här](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner godkännandet, returnerar Microsoft Identity Platform-slutpunkten ett svar till din app på den angivna `redirect_uri` metoden med hjälp av metoden som anges i `response_mode` parametern.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=query` ser ut så här:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beskrivning  |
|-----------|--------------|
| `code` | Den authorization_code som appen begärde. Appen kan använda auktoriseringskod för att begära en åtkomsttoken för mål resursen. Authorization_codes är korta livs längd, vanligt vis upphör de efter cirka 10 minuter. |
| `state` | Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

Du kan också få en åtkomsttoken och ID-token om du begär ett och har den implicita tilldelningen aktive rad i din program registrering.  Detta kallas ibland för "hybrid Flow" och används av ramverk som ASP.NET.

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till så att `redirect_uri` appen kan hantera dem på rätt sätt:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning  |
|----------|------------------|
| `error`  | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slut punkts fel i tillstånd

I följande tabell beskrivs de olika fel koderna som kan returneras i `error` parametern för fel svaret.

| Felkod  | Beskrivning    | Klient åtgärd   |
|-------------|----------------|-----------------|
| `invalid_request` | Protokoll fel, till exempel en obligatorisk parameter som saknas. | Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| `unauthorized_client` | Klient programmet får inte begära en auktoriseringskod. | Det här felet uppstår vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| `access_denied`  | Resurs ägare nekade medgivande  | Klient programmet kan meddela användaren att den inte kan fortsätta om inte användaren samtycks. |
| `unsupported_response_type` | Auktoriseringsservern stöder inte svars typen i begäran. | Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen.  |
| `server_error`  | Ett oväntat fel uppstod i servern.| Gör om begäran. Dessa fel kan orsakas av tillfälliga förhållanden. Klient programmet kan förklara för användaren att svaret är försenat med ett tillfälligt fel. |
| `temporarily_unavailable`   | Servern är tillfälligt upptagen och kan inte hantera begäran. | Gör om begäran. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt tillstånd. |
| `invalid_resource`  | Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så är den inte korrekt konfigurerad. | Det här felet anger att resursen, om den finns, inte har kon figurer ATS i klient organisationen. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| `login_required` | För många eller inga användare hittades | Klienten begärde tyst autentisering ( `prompt=none` ), men en enskild användare kunde inte hittas. Detta kan betyda att flera användare är aktiva i sessionen eller inga användare. Detta tar hänsyn till den klient som valts (till exempel om det finns två Azure AD-konton aktiva och en Microsoft-konto, och `consumers` väljs, så fungerar tyst autentisering). |
| `interaction_required` | Begäran kräver användar interaktion. | Ytterligare ett steg eller ett tillstånd för autentisering krävs. Gör om begäran utan `prompt=none` . |

## <a name="request-an-access-token"></a>Begär en åtkomsttoken

Nu när du har skaffat en authorization_code och har beviljats behörighet av användaren kan du lösa in `code` för en `access_token` till önskad resurs. Gör detta genom att skicka en `POST` begäran till `/token` slut punkten:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Försök att köra denna begäran i Postman! (Glöm inte att ersätta `code` ) [ ![ Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Obligatorisk/valfri | Beskrivning     |
|------------|-------------------|----------------|
| `tenant`   | krävs   | `{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | krävs  | Det program-ID (klient) som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan som har tilldelats till din app. |
| `grant_type` | krävs   | Måste vara `authorization_code` för flöde för auktoriseringskod.   |
| `scope`      | valfri   | En blankstegsavgränsad lista över omfång. Omfattningarna måste allt från en enda resurs, tillsammans med OIDC-scope ( `profile` , `openid` , `email` ). En mer detaljerad förklaring av omfattningar finns i [behörigheter, medgivande och omfattningar](v2-permissions-and-consent.md). Detta är ett Microsoft-tillägg till Authorization Code Flow, som är avsett att tillåta appar att deklarera den resurs som de vill ha token för under token inlösen.|
| `code`          | krävs  | Authorization_code som du har köpt i den första delen av flödet. |
| `redirect_uri`  | krävs  | Samma redirect_uri värde som användes för att hämta authorization_code. |
| `client_secret` | krävs för konfidentiella webbappar | Den program hemlighet som du skapade i appens registrerings Portal för din app. Du bör inte använda program hemligheten i en inbyggd app eller en app på en enskild sida eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter eller på webb sidor. Det krävs för webbappar och webb-API: er, som kan lagra client_secret säkert på Server sidan.  Klient hemligheten måste vara URL-kodad innan den kan skickas. Mer information om URI-kodning finns i [specifikation för URI-generisk syntax](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | rekommenderas  | Samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE användes i begäran om beviljande av auktoriseringskod. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar på token kommer att se ut så här:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beskrivning   |
|---------------|------------------------------|
| `access_token`  | Den begärda åtkomsttoken. Appen kan använda denna token för att autentisera till den skyddade resursen, till exempel ett webb-API.  |
| `token_type`    | Anger värdet för token-typ. Den enda typ som Azure AD stöder är Bearer |
| `expires_in`    | Hur länge åtkomsttoken är giltig (i sekunder). |
| `scope`         | De omfattningar som access_token är giltiga för. Valfritt – detta är inte standard och om det utelämnas används token för de omfattningar som begärs i den inledande benet i flödet. |
| `refresh_token` | En OAuth 2,0-uppdateringstoken. Appen kan använda denna token för att hämta ytterligare åtkomsttoken när den aktuella åtkomsttoken upphör att gälla. Refresh_tokens är långvarig och kan användas för att behålla åtkomst till resurser under långa tids perioder. Mer information om hur du uppdaterar en åtkomsttoken finns i [avsnittet nedan](#refresh-the-access-token). <br> **Obs:** Anges endast om `offline_access` omfattning begärdes. |
| `id_token`      | En JSON Web Token (JWT). Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem och konfidentiella klienter kan använda detta för auktorisering. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md) . <br> **Obs:** Anges endast om `openid` omfattning begärdes. |

### <a name="error-response"></a>Fel svar

Fel svaren kommer att se ut så här:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beskrivning    |
|-------------------|----------------|
| `error`       | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |
| `error_codes` | En lista med STS-specificerade felkoder som kan hjälpa dig i diagnostik.  |
| `timestamp`   | Tiden då felet inträffade. |
| `trace_id`    | En unik identifierare för begäran som kan hjälpa i diagnostik. |
| `correlation_id` | En unik identifierare för begäran som kan hjälpa dig i diagnostiken mellan komponenter. |

### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för token slut punkts fel

| Felkod         | Beskrivning        | Klient åtgärd    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokoll fel, till exempel en obligatorisk parameter som saknas. | Åtgärda begäran eller registrera appen och skicka begäran på nytt   |
| `invalid_grant`    | Auktoriseringskod eller PKCE Code Verifier är ogiltig eller har upphört att gälla. | Testa en ny begäran till `/authorize` slut punkten och kontrol lera att parametern code_verifier är korrekt.  |
| `unauthorized_client` | Den autentiserade klienten har inte behörighet att använda den här typen av auktoriserings beviljande. | Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| `invalid_client` | Klientautentisering misslyckades.  | Klientens autentiseringsuppgifter är inte giltiga. För att åtgärda detta uppdaterar program administratören autentiseringsuppgifterna.   |
| `unsupported_grant_type` | Auktoriseringsservern stöder inte typen för auktoriserings beviljande. | Ändra beviljad typ i begäran. Den här typen av fel bör bara inträffa under utvecklingen och identifieras under den första testningen. |
| `invalid_resource` | Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så är den inte korrekt konfigurerad. | Detta anger att resursen, om den finns, inte har kon figurer ATS i klient organisationen. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD.  |
| `interaction_required` | Icke-standard, eftersom OIDC-specifikationen bara anropar för detta på `/authorize` slut punkten. Begäran kräver användar interaktion. Till exempel krävs ytterligare ett autentiserings steg. | Gör om `/authorize` begäran med samma omfång. |
| `temporarily_unavailable` | Servern är tillfälligt upptagen och kan inte hantera begäran. | Gör om begäran efter en liten fördröjning. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt tillstånd. |
|`consent_required` | Begäran kräver användar medgivande. Det här felet är inte standard, eftersom det vanligt vis bara returneras i `/authorize` slut punkten per OIDC-specifikationer. Returneras när en `scope` parameter användes på det kod inlösen flöde som klient programmet inte har behörighet att begära.  | Klienten bör skicka tillbaka användaren till `/authorize` slut punkten med rätt omfång för att utlösa medgivande. |

> [!NOTE]
> Appar med en sida kan få ett `invalid_request` fel meddelande som anger att inlösningen för flera ursprungs-token endast tillåts för klient typen Single-Side-program.  Detta anger att omdirigerings-URI: n som används för att begära token inte har marker ATS som en `spa` omdirigerings-URI.  Granska [program registrerings stegen](#redirect-uri-setup-required-for-single-page-apps) för att aktivera det här flödet.

## <a name="use-the-access-token"></a>Använda åtkomsttoken

Nu när du har skaffat en kan `access_token` du använda token i begär anden till webb-API: er genom att inkludera den i `Authorization` rubriken:

> [!TIP]
> Kör den här begäran i Postman! (Ersätt `Authorization` rubriken först) [ ![ försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Uppdatera åtkomsttoken

Access_tokens är korta livs längd och du måste uppdatera dem när de har gått ut för att fortsätta att komma åt resurser. Det kan du göra genom att skicka en annan `POST` begäran till `/token` slut punkten, den här gången ger i `refresh_token` stället för `code` .  Uppdaterings-token är giltiga för alla behörigheter som klienten redan har fått tillåtelse för, vilket innebär att en uppdateringstoken som utfärdats på en begäran för `scope=mail.read` kan användas för att begära en ny åtkomsttoken för `scope=api://contoso.com/api/UseResource` .

Uppdatering av tokens för webbappar och inbyggda appar har inte angivna livs längder. Normalt är livs längden för uppdateringstoken relativt lång. Men i vissa fall går det inte att uppdatera token, återkallas eller saknar tillräcklig behörighet för önskad åtgärd. Ditt program måste vänta och hantera [fel som returneras av slut punkten för utfärdande av token](#error-codes-for-token-endpoint-errors) korrekt. Med en enda sida får du dock en token med en livs längd på 24 timmar, vilket kräver en ny autentisering varje dag.  Detta kan göras tyst i en iframe när cookies från tredje part är aktiverade, men måste göras i en ram på översta nivån (antingen hel sid navigering eller en popup) i webbläsare utan cookies från tredje part, till exempel Safari.

Även om uppdateringstoken inte återkallas när de används för att hämta nya åtkomsttoken, förväntas du ta bort den gamla uppdateringstoken. [OAuth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749#section-6) säger: "auktoriseringsservern kan utfärda en ny uppdateringstoken, i vilket fall klienten måste ta bort den gamla uppdateringstoken och ersätta den med den nya uppdateringstoken. Auktoriseringsservern kan återkalla den gamla uppdateringstoken när en ny uppdateringstoken har utfärdats till klienten. "

>[!IMPORTANT]
> För uppdateringstoken som skickas till en omdirigerings-URI som registrerats som `spa` , går uppdateringstoken ut efter 24 timmar. Ytterligare uppdateringstoken som förvärvas med den första uppdateringstoken kommer att överföras över den förfallo tiden, så att apparna måste förberedas för att köra om auktoriseringskod med en interaktiv autentisering för att få en ny uppdateringstoken var 24: e timme. Användarna behöver inte ange sina autentiseringsuppgifter och kommer vanligt vis inte ens att se något UX, bara en omladdning av ditt program, men webbläsaren måste gå till inloggnings sidan i en ram på översta nivån för att kunna se inloggnings sessionen.  Detta beror på [Sekretess funktioner i webbläsare som blockerar cookies från tredje part](reference-third-party-cookies-spas.md).

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Försök att köra denna begäran i Postman! (Glöm inte att ersätta `refresh_token` ) [ ![ Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parameter     | Typ           | Beskrivning        |
|---------------|----------------|--------------------|
| `tenant`        | krävs     | `{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | krävs    | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `grant_type`    | krävs    | Måste vara `refresh_token` för den här delen av Authorization Code Flow. |
| `scope`         | krävs    | En blankstegsavgränsad lista över omfång. De omfattningar som begärs i den här delen måste vara likvärdiga med eller en delmängd av de omfattningar som begärts i det ursprungliga authorization_code begär ande benet. Om de omfattningar som anges i denna begäran sträcker sig över flera resurs servrar, returnerar Microsoft Identity Platform-slutpunkten en token för den resurs som anges i det första omfånget. En mer detaljerad förklaring av omfattningar finns i [behörigheter, medgivande och omfattningar](v2-permissions-and-consent.md). |
| `refresh_token` | krävs    | Refresh_token som du har köpt i det andra benet i flödet. |
| `client_secret` | krävs för Web Apps | Den program hemlighet som du skapade i appens registrerings Portal för din app. Den bör inte användas i en intern app eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er, som kan lagra client_secret säkert på Server sidan. Den här hemligheten måste vara URL-kodad. Mer information finns i [specifikationen för URI-generisk syntax](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar på token kommer att se ut så här:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beskrivning         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Den begärda åtkomsttoken. Appen kan använda denna token för att autentisera till den skyddade resursen, till exempel ett webb-API. |
| `token_type`    | Anger värdet för token-typ. Den enda typ som Azure AD stöder är Bearer |
| `expires_in`    | Hur länge åtkomsttoken är giltig (i sekunder).   |
| `scope`         | De omfattningar som access_token är giltiga för.    |
| `refresh_token` | En ny OAuth 2,0-uppdateringstoken. Du bör ersätta den gamla uppdateringstoken med denna nyligen förvärvade uppdateringstoken för att se till att dina uppdateringstoken fortfarande är giltiga så länge som möjligt. <br> **Obs:** Anges endast om `offline_access` omfattning begärdes.|
| `id_token`      | En osignerad JSON Web Token (JWT). Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita sig på dem för några tillstånds-eller säkerhets gränser. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md) . <br> **Obs:** Anges endast om `openid` omfattning begärdes. |

#### <a name="error-response"></a>Fel svar

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beskrivning                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel.           |
| `error_codes` |En lista med STS-specificerade felkoder som kan hjälpa dig i diagnostik. |
| `timestamp` | Tiden då felet inträffade. |
| `trace_id` | En unik identifierare för begäran som kan hjälpa i diagnostik. |
| `correlation_id` | En unik identifierare för begäran som kan hjälpa dig i diagnostiken mellan komponenter. |

En beskrivning av felkoderna och den rekommenderade klient åtgärden finns i [felkoder för fel i token slut punkt](#error-codes-for-token-endpoint-errors).
