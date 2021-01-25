---
title: Microsoft Identity Platform och OpenID Connect-protokollet | Azure
titleSuffix: Microsoft identity platform
description: Bygg webb program med hjälp av Microsoft Identity Platform-implementeringen av OpenID Connect Authentication Protocol.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752591"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform och OpenID Connect-protokoll

OpenID Connect (OIDC) är ett autentiseringsprotokoll som skapats på OAuth 2,0 som du kan använda för att på ett säkert sätt logga in en användare i ett program. När du använder Microsoft Identity Platforms implementering av OpenID Connect kan du lägga till inloggnings-och API-åtkomst till dina appar. Den här artikeln visar hur du gör detta oberoende av språk och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda några [Microsoft-bibliotek med öppen källkod](reference-v2-libraries.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar *auktoriserings* protokollet OAuth 2,0 för användning som ett *autentiseringsprotokoll* , så att du kan utföra enkel inloggning med OAuth. OpenID Connect introducerar konceptet med en *ID-token*, vilket är en säkerhetstoken som gör att klienten kan verifiera användarens identitet. ID-token hämtar även grundläggande profil information om användaren. Den introducerar även [UserInfo-slutpunkten](userinfo.md), ett API som returnerar information om användaren. 


## <a name="protocol-diagram-sign-in"></a>Protokoll diagram: inloggning

Det mest grundläggande inloggnings flödet har stegen som visas i nästa diagram. Varje steg beskrivs i detalj i den här artikeln.

![OpenID Connect-protokoll: Logga in](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Hämta dokumentet för OpenID Connect-metadata

OpenID Connect beskriver ett metadataobjekt [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) som innehåller merparten av den information som krävs för att en app ska kunna logga in. Detta omfattar information som webb adresserna som ska användas och platsen för tjänstens offentliga signerings nycklar. Du kan hitta det här dokumentet genom att lägga till sökvägen för identifierings dokumentet till auktoritets-URL: en:

Sökväg till identifierings dokument: `/.well-known/openid-configuration`

Tullmyndighet `https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}`Kan göra något av fyra värden:

| Värde | Beskrivning |
| --- | --- |
| `common` |Användare med både ett privat Microsoft-konto och ett arbets-eller skol konto från Azure AD kan logga in i programmet. |
| `organizations` |Endast användare med arbets-eller skol konton från Azure AD kan logga in i programmet. |
| `consumers` |Endast användare med en personlig Microsoft-konto kan logga in i programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` | Endast användare från en specifik Azure AD-klient (oavsett om de är medlemmar i katalogen med ett arbets-eller skol konto eller om de är gäster i katalogen med en personlig Microsoft-konto) kan logga in i programmet. Antingen det egna domän namnet för Azure AD-klienten eller klient organisationens GUID-identifierare kan användas. Du kan också använda klient organisationen, `9188040d-6c67-4c5b-b112-36a304b66dad` i stället för `consumers` klient organisationen.  |

Utfärdaren skiljer sig över nationella moln – t. ex. `https://login.microsoftonline.de` för Azure AD Germany-instansen. Om du inte använder det offentliga molnet granskar du de [nationella moln slut punkterna](authentication-national-cloud.md#azure-ad-authentication-endpoints) för att hitta det som passar dig bäst. Se till att klienten och finns `/v2.0/` i din begäran så att du kan använda v 2.0-versionen av slut punkten.

> [!TIP]
> Prova! Klicka [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) om du vill se `common` konfigurationen.

### <a name="sample-request"></a>Exempel förfrågan

Använd följande för att anropa UserInfo-slutpunkten för den gemensamma utfärdaren i det offentliga molnet:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Exempelsvar

Metadata är ett enkelt JavaScript Object Notation (JSON)-dokument. Se följande kodfragment för ett exempel. Innehållet beskrivs fullständigt i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Om din app har anpassade signerings nycklar som ett resultat av funktionen för [anspråks mappning](active-directory-claims-mapping.md) måste du lägga till en `appid` frågeparameter som innehåller app-ID: t för att få en `jwks_uri` pekare till appens information om signerings nyckeln. Exempel: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

Normalt använder du det här Metadatadokumentet för att konfigurera ett OpenID Connect-bibliotek eller SDK. i biblioteket används metadata för att utföra sitt arbete. Men om du inte använder ett förbyggt OpenID Connect-bibliotek kan du följa stegen i resten av den här artikeln för att logga in i en webbapp med hjälp av Microsoft Identity Platform.

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

När din webbapp behöver autentisera användaren kan den dirigera användaren till `/authorize` slut punkten. Den här begäran liknar det första benet i [OAuth 2,0-auktoriseringskod](v2-oauth2-auth-code-flow.md), med följande viktiga distinkta:

* Begäran måste innehålla `openid` omfånget i `scope` parametern.
* `response_type`Parametern måste innehålla `id_token` .
* Begäran måste innehålla `nonce` parametern.

> [!IMPORTANT]
> För att kunna begära en ID-token från/Authorization-slutpunkten måste appens registrering på [registrerings portalen](https://portal.azure.com) ha implicit beviljande av id_tokens aktiverat på fliken autentisering (som anger `oauth2AllowIdTokenImplicitFlow` flaggan i [applikations manifestet](reference-app-manifest.md) till `true` ). Om den inte är aktive rad `unsupported_response` returneras ett fel: "det tillhandahållna värdet för indataparametern response_type tillåts inte för den här klienten. Förväntat värde är ' Code '

Ett exempel:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Du kan använda `{tenant}` värdet i sökvägen till begäran för att kontrol lera vem som kan logga in på programmet. De tillåtna värdena är `common` , `organizations` , `consumers` och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `response_type` | Obligatorisk | Måste inkludera `id_token` för OpenID Connect-inloggning. Det kan också innehålla andra `response_type` värden, till exempel `code` . |
| `redirect_uri` | Rekommenderas | Omdirigerings-URI för appen, där autentiseringsbegäranden kan skickas och tas emot av din app. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i portalen, förutom att den måste vara URL-kodad. Om detta inte finns väljer slut punkten en registrerad redirect_uri slumpmässigt för att skicka tillbaka användaren till. |
| `scope` | Obligatorisk | En blankstegsavgränsad lista över omfång. För att OpenID ska kunna ansluta måste den innehålla omfånget `openid` , som översätts till "logga in dig"-behörighet i medgivande gränssnittet. Du kan också inkludera andra omfattningar i den här begäran för att begära medgivande. |
| `nonce` | Obligatorisk | Ett värde som ingår i begäran, som genereras av appen, som kommer att inkluderas i det resulterande id_token svärdet som ett anspråk. Appen kan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. |
| `response_mode` | Rekommenderas | Anger den metod som ska användas för att skicka den resulterande auktoriseringskod tillbaka till din app. Det kan vara `form_post` eller `fragment`. För webb program rekommenderar vi att du använder `response_mode=form_post` , för att säkerställa den säkraste överföringen av tokens till ditt program. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Ett slumpmässigt genererat unikt värde används vanligt vis för att [förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Stadiet används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn där användaren var på. |
| `prompt` | Valfritt | Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är `login` , `none` och `consent` . `prompt=login`Anspråket tvingar användaren att ange sina autentiseringsuppgifter för begäran, vilket innebär att enkel inloggning används. `prompt=none`Anspråket är det motsatta. Detta påstående garanterar att användaren inte visas med interaktiva prompter på. Om begäran inte kan slutföras i bakgrunden via enkel inloggning returnerar Microsoft Identity Platform ett fel. `prompt=consent`Anspråket utlöser dialog rutan OAuth-medgivande när användaren loggar in. Dialog rutan uppmanar användaren att bevilja behörighet till appen. |
| `login_hint` | Valfritt | Du kan använda den här parametern för att fylla i fältet användar namn och e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar använder ofta den här parametern vid omautentisering, när de redan har extraherat användar namnet från en tidigare inloggning med hjälp av `preferred_username` anspråket. |
| `domain_hint` | Valfritt | Användarens sfär i en federerad katalog.  Detta hoppar över den e-postbaserad identifierings process som användaren går igenom på inloggnings sidan för en något mer strömlinjeformad användar upplevelse. För klienter som är federerade via en lokal katalog som AD FS resulterar detta ofta i en sömlös inloggning på grund av den befintliga inloggningssessionen. |

I det här läget uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen. Microsoft Identity Platform kontrollerar att användaren har samtyckt till de behörigheter som anges i `scope` Frågeparametern. Om användaren inte har samtyckt till någon av dessa behörigheter, uppmanas Microsoft Identity Platform att användaren godkänner de behörigheter som krävs. Du kan läsa mer om [behörigheter, medgivande och appar för flera klient organisationer](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner medgivande, returnerar Microsoft Identity Platform ett svar till din app vid den angivna omdirigerings-URI: n med hjälp av metoden som anges i `response_mode` parametern.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar när du använder `response_mode=form_post` ser ut så här:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `id_token` | Den ID-token som appen begärde. Du kan använda `id_token` parametern för att verifiera användarens identitet och påbörja en session med användaren. Mer information om ID-token och deras innehåll finns i [ `id_tokens` referensen](id-tokens.md). |
| `state` | Om en `state` parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem. Ett fel svar ser ut så här:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar och för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slut punkts fel i tillstånd

I följande tabell beskrivs felkoder som kan returneras i `error` parametern för fel svaret:

| Felkod | Beskrivning | Klient åtgärd |
| --- | --- | --- |
| `invalid_request` | Protokoll fel, till exempel en obligatorisk parameter som saknas. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| `unauthorized_client` | Klient programmet kan inte begära en auktoriseringskod. |Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan begära att användaren får instruktioner för att installera programmet och lägga till det i Azure AD. |
| `access_denied` | Resurs ägaren nekade medgivande. |Klient programmet kan meddela användaren att den inte kan fortsätta om inte användaren samtycks. |
| `unsupported_response_type` |Auktoriseringsservern stöder inte svars typen i begäran. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| `server_error` | Ett oväntat fel uppstod i servern. |Gör om begäran. Dessa fel kan orsakas av tillfälliga förhållanden. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt fel. |
| `temporarily_unavailable` | Servern är tillfälligt upptagen och kan inte hantera begäran. |Gör om begäran. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt tillstånd. |
| `invalid_resource` | Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så är den inte korrekt konfigurerad. |Detta anger att resursen, om den finns, inte har kon figurer ATS i klienten. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="validate-the-id-token"></a>Validera ID-token

Det räcker bara att ta emot ett id_token inte alltid för att autentisera användaren. Du kan också behöva verifiera id_token signaturen och verifiera anspråk i token enligt appens krav. Precis som alla OIDC-plattformar använder Microsoft Identity Platform [JSON Web tokens (JWTs)](https://tools.ietf.org/html/rfc7519) och kryptering med offentliga nycklar för att signera ID-token och kontrol lera att de är giltiga.

Alla appar drar inte nytta av att verifiera ID-token – inbyggda appar och appar på en enda sida, till exempel sällan fördelarna med att validera ID-token.  Någon med fysisk åtkomst till enheten (eller webbläsaren) kan kringgå verifieringen på många sätt – från att redigera webb trafiken till enheten för att tillhandahålla falska tokens och nycklar för att bara felsöka programmet för att hoppa över verifierings logiken.  Å andra sidan måste webbappar och API: er som använder en ID-token för auktorisering validera ID-token noggrant eftersom de har hantera åtkomst till data.

När du har verifierat signaturen för id_token behöver du bara verifiera några anspråk. Se [ `id_token` referensen](id-tokens.md) för mer information, inklusive [validera token](id-tokens.md#validating-an-id_token) och [viktig information om förnyelse av signerings nyckel](active-directory-signing-key-rollover.md). Vi rekommenderar att du använder ett bibliotek för att parsa och validera token – det finns minst en tillgänglig för de flesta språk och plattformar.

Du kanske också vill verifiera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet/privilegier
* Att se till att en viss styrka autentisering har skett, t. ex. [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

När du har verifierat id_token kan du starta en-session med användaren och använda anspråk i id_token för att hämta information om användaren i din app. Den här informationen kan användas för visning, poster, anpassning osv.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokoll diagram: hämtning av åtkomst-token

Många webbappar behöver inte bara signera användaren i, utan även för att få åtkomst till en webb tjänst för användarens räkning genom att använda OAuth. I det här scenariot kombineras OpenID-anslutningar för användarautentisering samtidigt som du får en auktoriseringskod som du kan använda för att hämta åtkomsttoken om du använder OAuth Authorization Code Flow.

Det fullständiga OpenID Connect-flödet för inloggning och hämtning av token ser ut ungefär som i nästa diagram. Vi beskriver varje steg i detalj i nästa avsnitt av artikeln.

![OpenID Connect-protokoll: token-hämtning](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Hämta en åtkomsttoken för att anropa UserInfo

Om du vill hämta en token för OIDC UserInfo-slutpunkten ändrar du inloggnings förfrågan:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Du kan också använda [auktoriseringskod](v2-oauth2-auth-code-flow.md), [enhets kod flödet](v2-oauth2-device-code.md)eller en [uppdateringstoken](v2-oauth2-auth-code-flow.md#refresh-the-access-token) i stället för `response_type=token` för att hämta en token för din app.

> [!TIP]
> Klicka på följande länk för att utföra den här begäran. När du har loggat in omdirigeras webbläsaren till `https://localhost/myapp/` med en ID-token och en token i adress fältet. Observera att denna begäran `response_mode=fragment` endast använder i demonstrations syfte – för en webapp rekommenderar vi att du använder `form_post` för ytterligare säkerhet där det är möjligt. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Svar på lyckad token

Ett lyckat svar från att använda `response_mode=form_post` ser ut så här:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Svars parametrar innebär samma sak oavsett vilket flöde som används för att hämta dem.

| Parameter | Beskrivning |
| --- | --- |
| `access_token` | Den token som ska användas för att anropa UserInfo-slutpunkten.|
| `token_type` | Alltid "Bearer" |
| `expires_in`| Hur länge tills åtkomsttoken upphör att gälla, i sekunder. |
| `scope` | De behörigheter som beviljats för åtkomsttoken.  Observera att eftersom UserInfo-slutpunkten finns i MS Graph, kan det finnas ytterligare diagram omfattningar som listas här (t. ex. user. Read) om de tidigare har beviljats till appen.  Det beror på att en token för en specifik resurs alltid innehåller alla behörigheter som för närvarande beviljas till klienten.  |
| `id_token` | Den ID-token som appen begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. Du hittar mer information om ID-tokens och deras innehåll i [ `id_tokens` referensen](id-tokens.md). |
| `state` | Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till omdirigerings-URI: n så att appen kan hantera dem på rätt sätt. Ett fel svar ser ut så här:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som du kan använda för att klassificera typer av fel som inträffar och för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa dig att identifiera rotor saken till ett autentiseringsfel. |

En beskrivning av möjliga fel koder och rekommenderade klient svar finns i [felkoder för slut punkts fel](#error-codes-for-authorization-endpoint-errors).

När du har en auktoriseringskod och en ID-token kan du logga in användaren i och hämta åtkomsttoken för deras räkning. För att kunna signera användaren i måste du validera ID-token [exakt enligt beskrivningen](id-tokens.md#validating-an-id_token). Om du vill hämta åtkomsttoken följer du stegen som beskrivs i [dokumentationen för OAuth Code Flow](v2-oauth2-auth-code-flow.md#request-an-access-token).

### <a name="calling-the-userinfo-endpoint"></a>Anropar UserInfo-slutpunkten

Läs informationen om [UserInfo](userinfo.md#calling-the-api) för att se hur anropet till UserInfo-slutpunkten med denna token.

## <a name="send-a-sign-out-request"></a>Skicka en inloggningsbegäran

När du vill logga ut användaren från din app räcker det inte att rensa appens cookies eller på annat sätt avsluta användarens session. Du måste också omdirigera användaren till Microsoft Identity Platform för att kunna logga ut. Om du inte gör detta autentiserar användaren om din app utan att ange sina autentiseringsuppgifter igen, eftersom de kommer att ha en giltig enkel inloggnings-session med Microsoft Identity Platform.

Du kan omdirigera användaren till listan `end_session_endpoint` i OpenID Connect metadata-dokumentet:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Villkor | Beskrivning |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Rekommenderas | URL: en som användaren omdirigeras till efter att den har loggat ut. Om parametern inte ingår visas ett allmänt meddelande som genereras av Microsoft Identity Platform. URL: en måste matcha en av de omdirigerings-URI: er som registrerats för ditt program i registrerings portalen för appen. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar användaren till `end_session_endpoint` , rensar Microsoft Identity Platform användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad i andra program som använder Microsoft-konton för autentisering. Om du vill att dessa program ska kunna signera användaren samtidigt skickar Microsoft Identity Platform en HTTP GET-begäran till registrerade `LogoutUrl` för alla program som användaren är inloggad på. Program måste svara på den här begäran genom att rensa alla sessioner som identifierar användaren och returnera ett `200` svar. Om du vill stödja enkel utloggning i ditt program måste du implementera en sådan `LogoutUrl` i din program kod. Du kan ställa in `LogoutUrl` från registrerings portalen för appen.

## <a name="next-steps"></a>Nästa steg

* Läs [dokumentationen om UserInfo](userinfo.md)
* Lär dig hur du [anpassar värdena i en token](active-directory-claims-mapping.md) med data från dina lokala system. 
* Lär dig hur du [lägger till fler standard anspråk i tokens](active-directory-optional-claims.md).  
