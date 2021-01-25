---
title: OAuth 2,0-klientens autentiseringsuppgifter flöde på Microsoft Identity Platform | Azure
description: Bygg webb program med hjälp av Microsoft Identity Platform-implementeringen av autentiseringsprotokollet OAuth 2,0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/2/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 96f7d7c94ce908d953a6941bfa237fe8da1dc482
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752658"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity Platform och OAuth 2,0-klientens autentiseringsuppgifter flödet

Du kan använda [tilldelningen oauth 2,0-klientautentiseringsuppgifter](https://tools.ietf.org/html/rfc6749#section-4.4) som anges i RFC 6749, ibland kallat *två-ledade OAuth*, för att få åtkomst till webb värd resurser med hjälp av identiteten för ett program. Den här beviljandetypen används ofta för server-till-server-interaktioner som måste köras i bakgrunden, utan direkt interaktion med en användare. Dessa typer av program kallas ofta för *daemon* eller *tjänst konton*.

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program. När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

OAuth 2,0-klientens autentiseringsuppgifter för beviljande av autentiseringsuppgifter tillåter en webb tjänst (konfidentiell klient) att använda sina egna autentiseringsuppgifter, i stället för att personifiera en användare, för att autentisera vid anrop till en annan webb tjänst. I det här scenariot är klienten vanligt vis en webb tjänst på mellan nivå, en daemon-tjänst eller en webbplats. För en högre säkerhets nivå tillåter Microsofts identitets plattform också att anrops tjänsten använder ett certifikat (i stället för en delad hemlighet) som autentiseringsuppgift.

I flödet för klientautentiseringsuppgifter beviljas behörigheter direkt till själva programmet av en administratör. När appen presenterar en token för en resurs, tillämpar resursen att själva appen har behörighet att utföra en åtgärd eftersom det inte finns någon användare som är involverad i autentiseringen.  Den här artikeln beskriver båda de steg som krävs för att [auktorisera ett program för att anropa ett API](#application-permissions), samt [hur du hämtar de token som krävs för att anropa detta API](#get-a-token).

## <a name="protocol-diagram"></a>Protokoll diagram

Hela flödet för klientens autentiseringsuppgifter ser ut ungefär som i följande diagram. Vi beskriver vart och ett av stegen längre fram i den här artikeln.

![Diagram som visar flödet för klientautentiseringsuppgifter](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Hämta direkt auktorisering

En app får normalt direkt behörighet att få åtkomst till en resurs på något av två sätt:

* [Via en åtkomst kontrol lista (ACL) på resursen](#access-control-lists)
* [Genom tilldelning av program behörighet i Azure AD](#application-permissions)

Dessa två metoder är de vanligaste i Azure AD och vi rekommenderar dem för klienter och resurser som utför flödet för klientautentiseringsuppgifter. En resurs kan också välja att auktorisera klienter på andra sätt. Varje resurs Server kan välja den metod som är bäst för dess program.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll

En resurs leverantör kan genomdriva en verifierings kontroll baserat på en lista över program-ID: n (klienter) som den känner till och beviljar en specifik åtkomst nivå till. När resursen tar emot en token från Microsoft Identity Platform kan den avkoda token och extrahera klientens program-ID från- `appid` och- `iss` anspråk. Sedan jämförs programmet med en åtkomst kontrol lista (ACL) som den underhåller. ACL: s granularitet och metod kan variera avsevärt mellan resurserna.

Ett vanligt användnings fall är att använda en ACL för att köra tester för ett webb program eller för ett webb-API. Webb-API: et kan bara ge en del av fullständiga behörigheter till en viss klient. Om du vill köra slut punkt till slut punkt på API: et skapar du en test klient som hämtar token från Microsoft Identity Platform och skickar dem sedan till API: et. API: et kontrollerar sedan ACL: en för test klientens program-ID för fullständig åtkomst till API: ns hela funktion. Om du använder den här typen av ACL måste du verifiera att det inte bara `appid` är anroparens värde, utan även kontrol lera att `iss` värdet för token är betrott.

Den här typen av auktorisering är vanlig för daemon-och tjänst konton som behöver åtkomst till data som ägs av konsument användare som har personliga Microsoft-konton. För data som ägs av organisationer rekommenderar vi att du får nödvändig behörighet genom program behörigheter.

#### <a name="controlling-tokens-without-the-roles-claim"></a>Kontrol lera token utan `roles` anspråket

För att aktivera detta ACL-baserade auktoriseringsarkiv kräver Azure AD inte att program har behörighet att hämta token för ett annat program. Därför kan endast app-token utfärdas utan `roles` anspråk. Program som exponerar API: er måste implementera behörighets kontroller för att kunna godkänna tokens.

Om du vill förhindra att program hämtar roller som inte har några roller, [så se till att krav för användar tilldelning har Aktiver ATS för din app](../manage-apps/assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Detta blockerar användare och program utan tilldelade roller från att kunna hämta en token för det här programmet. 

### <a name="application-permissions"></a>Program behörigheter

I stället för att använda ACL: er kan du använda API: er för att visa en uppsättning **program behörigheter**. En program behörighet beviljas till ett program av en organisations administratör och kan bara användas för att komma åt data som ägs av organisationen och dess anställda. Microsoft Graph visar till exempel flera program behörigheter för att göra följande:

* Läs e-post i alla post lådor
* Läsa och skriva e-post i alla post lådor
* Skicka e-post som valfri användare
* Läs katalogdata

Om du vill använda program behörigheter med ditt eget API (till skillnad från Microsoft Graph), måste du först [exponera API: et](quickstart-configure-app-expose-web-apis.md) genom att definiera omfång i API: ns app Registration i Azure Portal. Konfigurera sedan [åtkomst till API: et](quickstart-configure-app-access-web-apis.md) genom att välja dessa behörigheter i klient programmets app-registrering. Om du inte har exponerat några omfång i ditt API: s app-registrering kan du inte ange program behörigheter till detta API i klient programmets app-registrering i Azure Portal.

När du autentiserar som ett program (till skillnad från med en användare) kan du inte använda *delegerade behörigheter* – omfattningar som har beviljats av en användare. Du måste använda program behörigheter, även kallade roller, som har beviljats av en administratör för programmet eller via för auktorisering i webb-API: et.

Mer information om program behörigheter finns i [behörigheter och medgivande](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderat: signera användaren i din app

När du skapar ett program som använder program behörigheter kräver appen vanligt vis en sida eller vy där administratören godkänner appens behörigheter. Den här sidan kan vara en del av appens inloggnings flöde, en del av appens inställningar, eller så kan det vara ett dedikerat "Connect"-flöde. I många fall är det meningsfullt att appen visar vyn "Anslut" bara när en användare har loggat in med ett arbets-eller skol Microsoft-konto.

Om du signerar användaren till din app kan du identifiera den organisation som användaren tillhör innan du ber användaren att godkänna program behörigheter. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina användare. Följ [självstudierna för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md)för att logga in användaren i.

#### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalog administratör

När du är redo att begära behörigheter från organisationens administratör kan du omdirigera användaren till Microsoft Identity Platform *admin medgivande-slutpunkten*.

> [!TIP]
> Försök att köra denna begäran i Postman! (Använd ditt eget app-ID för bästa resultat – själv studie kursen begär inte användbara behörigheter.) [ ![ Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Pro-tips: försök att klistra in följande begäran i en webbläsare.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Den katalog klient som du vill begära behörighet från. Detta kan vara i ett GUID eller eget namn format. Om du inte vet vilken klient som användaren tillhör och du vill låta dem logga in med valfri klient använder du `common` . |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `redirect_uri` | Obligatorisk | Den omdirigerings-URI där du vill att svaret på din app ska hanteras. Det måste exakt matcha en av de omdirigerings-URI: er som du har registrerat i portalen, förutom att den måste vara URL-kodad och den kan ha ytterligare Sök vägs segment. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |

I det här läget tvingar Azure AD att endast en klient organisations administratör kan logga in för att slutföra begäran. Administratören kommer att uppmanas att godkänna alla direkta program behörigheter som du har begärt för din app i registrerings portalen för appen.

##### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för ditt program ser det lyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Den katalog klient som beviljade programmet de behörigheter som begärdes i GUID-format. |
| `state` | Ett värde som ingår i begäran som också returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `admin_consent` | Ange som **Sant**. |

##### <a name="error-response"></a>Fel svar

Om administratören inte godkänner behörigheterna för ditt program ser det misslyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel och som du kan använda för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett fel. |

När du har fått ett lyckat svar från appens etablerings slut punkt har appen fått de direkta program behörigheter som krävs. Nu kan du begära en token för den resurs som du vill ha.

## <a name="get-a-token"></a>Hämta en token

När du har skaffat nödvändig behörighet för ditt program kan du fortsätta med att förvärva åtkomsttoken för API: er. Om du vill hämta en token med hjälp av tilldelningen av klientautentiseringsuppgifter skickar du en POST-begäran till `/token` Microsoft Identity Platform:

> [!TIP]
> Försök att köra denna begäran i Postman! (Använd ditt eget app-ID för bästa resultat – själv studie kursen begär inte användbara behörigheter.) [ ![ Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: begäran om åtkomsttoken med en delad hemlighet

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Katalogen som klienten ska använda för att hantera i GUID-eller domän namns format. |
| `client_id` | Obligatorisk | Det program-ID som har tilldelats din app. Du hittar den här informationen i portalen där du registrerade appen. |
| `scope` | Obligatorisk | Värdet som skickas för `scope` parametern i den här begäran ska vara resurs-ID: t (program-ID: t) för den resurs som du vill ha, fäst med `.default` suffixet. För Microsoft Graph exemplet är värdet `https://graph.microsoft.com/.default` . <br/>Det här värdet anger Microsoft Identity-plattformen för alla direkta program behörigheter som du har konfigurerat för din app. slut punkten bör utfärda en token för de som är associerade med den resurs som du vill använda. Mer information om `/.default` omfattningen finns i [medgivande dokumentationen](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obligatorisk | Den klient hemlighet som du genererade för din app i registrerings portalen för appen. Klient hemligheten måste vara URL-kodad innan den kan skickas. |
| `grant_type` | Obligatorisk | Måste anges till `client_credentials` . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fall: åtkomsttoken för begäran med ett certifikat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Katalogen som klienten ska använda för att hantera i GUID-eller domän namns format. |
| `client_id` | Obligatorisk |Det program-ID (klient) som har tilldelats din app. |
| `scope` | Obligatorisk | Värdet som skickas för `scope` parametern i den här begäran ska vara resurs-ID: t (program-ID: t) för den resurs som du vill ha, fäst med `.default` suffixet. För Microsoft Graph exemplet är värdet `https://graph.microsoft.com/.default` . <br/>Det här värdet informerar Microsoft Identity Platform om alla direkta program behörigheter som du har konfigurerat för din app. den bör utfärda en token för de som associeras med den resurs som du vill använda. Mer information om `/.default` omfattningen finns i [medgivande dokumentationen](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obligatorisk | Värdet måste anges till `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Obligatorisk | En kontroll (en JSON-webbtoken) som du måste skapa och signera med det certifikat som du har registrerat som autentiseringsuppgifter för ditt program. Läs om [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md) för att lära dig hur du registrerar ditt certifikat och formatet på intyget.|
| `grant_type` | Obligatorisk | Måste anges till `client_credentials` . |

Observera att parametrarna är nästan desamma som i fallet med delad hemlighet, förutom att parametern client_secret ersätts av två parametrar: en client_assertion_type och client_assertion.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar ut så här:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` | Den begärda åtkomsttoken. Appen kan använda denna token för att autentisera till den skyddade resursen, till exempel ett webb-API. |
| `token_type` | Anger värdet för token-typ. Den enda typ som stöds av Microsoft Identity Platform `bearer` . |
| `expires_in` | Den tid som en åtkomsttoken är giltig (i sekunder). |

### <a name="error-response"></a>Fel svar

Ett fel svar ser ut så här:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar och för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |
| `error_codes` | En lista med STS-specificerade felkoder som kan vara till hjälp vid diagnostik. |
| `timestamp` | Den tidpunkt då felet inträffade. |
| `trace_id` | En unik identifierare för begäran om att hjälpa till med diagnostik. |
| `correlation_id` | En unik identifierare för begäran om att hjälpa till med diagnostik i komponenter. |

## <a name="use-a-token"></a>Använd en token

Nu när du har skaffat en token kan du använda token för att göra förfrågningar till resursen. När token går ut upprepar du begäran till `/token` slut punkten för att hämta en ny åtkomsttoken.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Kod exempel och annan dokumentation

Läs [dokumentations översikten över klientens autentiseringsuppgifter](https://aka.ms/msal-net-client-credentials) från Microsoft Authentication Library

| Exempel | Plattform |Beskrivning |
|--------|----------|------------|
|[Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2,1-konsol | Ett enkelt .NET Core-program som visar användare av en klient som frågar Microsoft Graph som använder appens identitet, i stället för för en användares räkning. Exemplet illustrerar också variationen med hjälp av certifikat för autentisering. |
|[Active Directory-Dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Ett webb program som synkroniserar data från Microsoft Graph med appens identitet, i stället för för en användares räkning. |
