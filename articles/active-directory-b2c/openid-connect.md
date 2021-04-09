---
title: Webb inloggning med OpenID Connect-Azure Active Directory B2C
description: Bygg webb program med OpenID Connect-autentiseringsprotokollet i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 87415fc98bbcc9331ae4ff6282a65c85b570042d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579781"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webb inloggning med OpenID Connect i Azure Active Directory B2C

OpenID Connect är ett autentiseringsprotokoll som byggts ovanpå OAuth 2,0 och som kan användas för att på ett säkert sätt logga användare i webb program. Genom att använda Azure Active Directory B2C (Azure AD B2C) implementeringen av OpenID Connect kan du registrera dig för registrering, inloggning och andra identitets hanterings upplevelser i dina webb program för att Azure Active Directory (Azure AD). Den här guiden visar hur du gör detta på ett språk oberoende sätt. Den beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda våra bibliotek med öppen källkod.

> [!NOTE]
> De flesta av autentiseringsmetoderna för autentisering med öppen källkod hämtar och validerar JWT-token för ditt program. Vi rekommenderar att du utforskar dessa alternativ i stället för att implementera din egen kod. Mer information finns i [Översikt över Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md)och [Microsoft Identity Web Authentication Library](../active-directory/develop/microsoft-identity-web.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) utökar protokollet OAuth 2,0- *auktorisering* för användning som *autentiseringsprotokoll* . Med det här autentiseringsprotokollet kan du utföra enkel inloggning. Den introducerar begreppet *ID-token*, vilket gör att klienten kan verifiera användarens identitet och få grundläggande profil information om användaren.

Eftersom den utökar OAuth 2,0, gör det också möjligt för program att säkert Hämta *åtkomsttoken*. Du kan använda åtkomsttoken för att få åtkomst till resurser som skyddas av en [Authorization Server](protocols-overview.md). OpenID Connect rekommenderas om du skapar ett webb program som finns på en server och har åtkomst till via en webbläsare. Mer information om tokens finns i [Översikt över tokens i Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C utökar standard OpenID Connect-protokollet för att göra mer än enkel autentisering och auktorisering. Den introducerar [användar flödes parametern](user-flow-overview.md), som gör att du kan använda OpenID Connect för att lägga till användar upplevelser till ditt program, till exempel registrering, inloggning och profil hantering.

## <a name="send-authentication-requests"></a>Skicka autentiseringsbegäranden

När ditt webb program behöver autentisera användaren och köra ett användar flöde, kan användaren dirigera användaren till `/authorize` slut punkten. Användaren vidtar åtgärder beroende på användar flödet.

I den här förfrågan anger klienten de behörigheter som krävs för att hämta från användaren i `scope` parametern och anger det användar flöde som ska köras. För att få en känsla för hur begäran fungerar kan du försöka att klistra in begäran i en webbläsare och köra den. Ersätt `{tenant}` med namnet på din klient. Ersätt `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` med app-ID: t för det program som du tidigare har registrerat i din klient. Ändra också princip namnet ( `{policy}` ) till det princip namn som du har i din klient organisation, till exempel `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| innehav | Ja | Namnet på din Azure AD B2C-klient |
| politik | Ja | Det användar flöde som ska köras. Ange namnet på ett användar flöde som du har skapat i Azure AD B2C klient organisationen. Till exempel: `b2c_1_sign_in` , `b2c_1_sign_up` , eller `b2c_1_edit_profile` . |
| client_id | Ja | Det program-ID som [Azure Portal](https://portal.azure.com/) tilldelats till ditt program. |
| Nnär | Ja | Ett värde som ingår i begäran (genereras av programmet) som ingår i det resulterande ID-token som ett anspråk. Programmet kan sedan kontrol lera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. |
| response_type | Ja | Måste innehålla en ID-token för OpenID Connect. Om ditt webb program också behöver tokens för att anropa ett webb-API kan du använda `code+id_token` . |
| omfång | Ja | En blankstegsavgränsad lista över omfång. `openid`Omfånget anger en behörighet för att logga in användaren och hämta data om användaren i form av ID-token. `offline_access`Omfånget är valfritt för webb program. Det anger att programmet behöver en *uppdateringstoken* för utökad åtkomst till resurser. |
| visas | Inga | Typ av användar interaktion som krävs. Det enda giltiga värdet för tillfället är `login` , vilket tvingar användaren att ange sina autentiseringsuppgifter för begäran. |
| redirect_uri | Inga | `redirect_uri`Parametern för ditt program, där autentiserings svar kan skickas och tas emot av ditt program. Den måste exakt matcha en av `redirect_uri` parametrarna som du registrerade i Azure Portal, förutom att den måste vara URL-kodad. |
| response_mode | Inga | Den metod som används för att skicka den resulterande auktoriseringskod tillbaka till ditt program. Det kan vara antingen `query` , `form_post` eller `fragment` .  `form_post`Svars läget rekommenderas för bästa säkerhet. |
| state | Inga | Ett värde som ingår i begäran som också returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Ett slumpmässigt genererat unikt värde används vanligt vis för att förhindra förfalsknings attacker på begäran från en annan plats. Statusen används också för att koda information om användarens tillstånd i programmet innan autentiseringsbegäran inträffade, t. ex. sidan de var på. |
| login_hint | Inga| Kan användas för att fylla i fältet inloggnings namn på inloggnings sidan. Mer information finns i [förkonfigurera inloggnings namnet](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | Inga| Innehåller ett tips för Azure AD B2C om den sociala identitets leverantör som ska användas för inloggning. Om ett giltigt värde ingår skickas användaren direkt till inloggnings sidan för identitets leverantören.  Mer information finns i [omdirigera inloggning till en social leverantör](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Anpassade parametrar | Inga| Anpassade parametrar som kan användas med [anpassade principer](custom-policy-overview.md). Till exempel en [dynamisk anpassad sid innehålls-URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)eller [nyckel lösare för nyckel värdes anspråk](claim-resolver-overview.md#oauth2-key-value-parameters). |

Nu uppmanas användaren att slutföra arbets flödet. Användaren kan behöva ange sitt användar namn och lösen ord, logga in med en social identitet eller registrera dig för katalogen. Det kan finnas andra antal steg beroende på hur användar flödet har definierats.

När användaren har slutfört användar flödet returneras ett svar till programmet vid den angivna `redirect_uri` parametern genom att använda metoden som anges i `response_mode` parametern. Svaret är detsamma för var och en av föregående fall, oberoende av användar flödet.

Ett lyckat svar med `response_mode=fragment` skulle se ut så här:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| id_token | ID-token som programmet begärde. Du kan använda ID-token för att verifiera användarens identitet och påbörja en session med användaren. |
| kod | Den auktoriseringskod som programmet begärde, om du använde `response_type=code+id_token` . Programmet kan använda auktoriseringskod för att begära en åtkomsttoken för en mål resurs. Auktoriseringskod upphör normalt att gälla efter 10 minuter. |
| state | Om en `state` parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrol lera att `state` värdena i begäran och svaret är identiska. |

Fel svar kan också skickas till- `redirect_uri` parametern så att programmet kan hantera dem på rätt sätt:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera de typer av fel som inträffar. |
| error_description | Ett fel meddelande som kan hjälpa till att identifiera rotor saken till ett autentiseringsfel. |
| state | Om en `state` parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrol lera att `state` värdena i begäran och svaret är identiska. |

## <a name="validate-the-id-token"></a>Validera ID-token

Det räcker bara att ta emot ID-token för att autentisera användaren. Verifiera signaturen för ID-token och verifiera anspråk i token enligt programmets krav. Azure AD B2C använder [JSON-Webbtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentliga nycklar för att signera tokens och kontrol lera att de är giltiga. 

> [!NOTE]
> De flesta av autentiseringsmetoderna för autentisering med öppen källkod verifierar JWT-token för ditt program. Vi rekommenderar att du utforskar dessa alternativ i stället för att implementera din egen verifierings logik. Mer information finns i [Översikt över Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md)och [Microsoft Identity Web Authentication Library](../active-directory/develop/microsoft-identity-web.md).

Azure AD B2C har en slut punkt för OpenID Connect-metadata som gör det möjligt för ett program att hämta information om Azure AD B2C vid körning. Den här informationen omfattar slut punkter, token innehåll och signerings nycklar för token. Det finns ett JSON-Metadatadokumentet för varje användar flöde i B2C-klienten. Till exempel finns metadata-dokumentet för `b2c_1_sign_in` användar flödet i `fabrikamb2c.onmicrosoft.com` :

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

En av egenskaperna för detta konfigurations dokument är `jwks_uri` , vars värde för samma användar flöde skulle vara:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

För att avgöra vilket användar flöde som användes för att signera en ID-token (och varifrån metadata ska hämtas) har du två alternativ. Först ingår användar flödes namnet i `acr` anspråket i ID-token. Ditt andra alternativ är att koda användar flödet i värdet för `state` parametern när du utfärdar begäran och sedan avkoda det för att avgöra vilket användar flöde som användes. Antingen är metoden giltig.

När du har köpt Metadatadokumentet från slut punkten för OpenID Connect-metadata kan du använda offentliga RSA 256-nycklar för att verifiera signaturen för ID-token. Det kan finnas flera nycklar i den här slut punkten, som var och en identifieras av ett `kid` anspråk. Huvudet på ID-token innehåller också ett `kid` anspråk som anger vilka av dessa nycklar som användes för att signera ID-token.

Om du vill verifiera token från Azure AD B2C måste du generera den offentliga nyckeln med exponenten (e) och modulus (n). Du måste bestämma hur du vill göra detta i respektive programmeringsspråk i enlighet med detta. Den officiella dokumentationen om generering av offentliga nycklar med RSA-protokollet hittar du här: https://tools.ietf.org/html/rfc3447#section-3.1

När du har verifierat signaturen för ID-token finns det flera anspråk som du måste verifiera. Till exempel:

- Verifiera `nonce` anspråk för att förhindra repetition av token-attacker. Värdet bör vara det du angav i inloggnings förfrågan.
- Verifiera `aud` anspråket för att säkerställa att ID-token har utfärdats för ditt program. Värdet ska vara programmets program-ID.
- Verifiera `iat` och `exp` -anspråk för att kontrol lera att ID-token inte har gått ut.

Det finns också flera fler verifieringar som du bör utföra. Valideringarna beskrivs i detalj i [specifikationen OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill verifiera ytterligare anspråk, beroende på ditt scenario. Några vanliga valideringar är:

- Se till att användaren/organisationen har registrerat sig för programmet.
- Se till att användaren har rätt behörighet/privilegier.
- Se till att en viss styrka autentisering har inträffat, till exempel Azure AD Multi-Factor Authentication.

När ID-token har verifierats kan du starta en session med användaren. Du kan använda anspråken i ID-token för att hämta information om användaren i ditt program. Användningen av den här informationen omfattar visning, poster och auktorisering.

## <a name="get-a-token"></a>Hämta en token

Om du behöver ditt webb program för att bara köra användar flöden kan du hoppa över efterföljande avsnitt. Dessa avsnitt gäller endast för webb program som behöver göra autentiserade anrop till ett webb-API och som också skyddas av Azure AD B2C.

Du kan lösa in den auktoriseringskod som du har köpt (med hjälp av `response_type=code+id_token` ) för en token till önskad resurs genom att skicka en `POST` begäran till `/token` slut punkten. I Azure AD B2C kan du [begära åtkomsttoken för andra API: er](access-tokens.md#request-a-token) som vanligt genom att ange deras omfång i begäran.

Du kan också begära en åtkomsttoken för appens egna Server dels webb-API genom att använda appens klient-ID som begärda omfång (vilket leder till en åtkomsttoken med klient-ID: t som mål grupp):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| innehav | Ja | Namnet på din Azure AD B2C-klient |
| politik | Ja | Det användar flöde som användes för att hämta auktoriseringskod. Du kan inte använda ett annat användar flöde i denna begäran. Lägg till den här parametern i frågesträngen, inte POST texten. |
| client_id | Ja | Det program-ID som [Azure Portal](https://portal.azure.com/) tilldelats till ditt program. |
| client_secret | Ja, i Web Apps | Den program hemlighet som genererades i [Azure Portal](https://portal.azure.com/). Klient hemligheter används i det här flödet för scenarier med webb program, där klienten kan lagra en klient hemlighet på ett säkert sätt. För interna app-scenarier (offentliga klienter) kan inte klient hemligheter lagras på ett säkert sätt, vilket därför inte används i det här flödet. Om du använder en klient hemlighet måste du ändra den regelbundet. |
| kod | Ja | Den auktoriseringskod som du hämtade i början av användar flödet. |
| grant_type | Ja | Typ av beviljande, som måste vara `authorization_code` för flödet för auktoriseringskod. |
| redirect_uri | Ja | `redirect_uri`Parametern för det program där du fick auktoriseringskod. |
| omfång | Inga | En blankstegsavgränsad lista över omfång. `openid`Omfånget anger en behörighet för att logga in användaren och hämta data om användaren i form av id_token parametrar. Den kan användas för att hämta tokens till programmets egna Server dels webb-API, som representeras av samma program-ID som klienten. `offline_access`Omfånget anger att programmet behöver en uppdateringstoken för utökad åtkomst till resurser. |

Ett lyckat token-svar ser ut så här:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| not_before | Tiden då token anses vara giltig, i epok tid. |
| token_type | Värdet för token-typ. `Bearer` är den enda typ som stöds. |
| access_token | Den signerade JWT-token som du begärde. |
| omfång | De omfattningar som token är giltig för. |
| expires_in | Hur lång tid som åtkomsttoken är giltig (i sekunder). |
| refresh_token | En OAuth 2,0-uppdateringstoken. Programmet kan använda denna token för att hämta ytterligare token när aktuell token upphör att gälla. Du kan använda uppdaterings-token för att behålla åtkomst till resurser under långa tids perioder. Omfånget `offline_access` måste ha använts i både begäran om auktorisering och token för att kunna ta emot en uppdateringstoken. |

Fel svar ser ut så här:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som inträffar. |
| error_description | Ett meddelande som kan hjälpa till att identifiera rotor saken till ett autentiseringsfel. |

## <a name="use-the-token"></a>Använda token

Nu när du har skaffat en åtkomsttoken kan du använda token i begär anden till dina Server dels webb-API: er genom att inkludera den i `Authorization` rubriken:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Uppdatera token

ID-token går ut under en kort tids period. Uppdatera token när de har gått ut för att fortsätta att ha åtkomst till resurser. Du kan uppdatera en token genom att skicka en annan `POST` begäran till `/token` slut punkten. Den här gången anger du `refresh_token` parametern i stället för `code` parametern:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| innehav | Ja | Namnet på din Azure AD B2C-klient |
| politik | Ja | Det användar flöde som användes för att hämta den ursprungliga uppdateringstoken. Du kan inte använda ett annat användar flöde i denna begäran. Lägg till den här parametern i frågesträngen, inte POST texten. |
| client_id | Ja | Det program-ID som [Azure Portal](https://portal.azure.com/) tilldelats till ditt program. |
| client_secret | Ja, i Web Apps | Den program hemlighet som genererades i [Azure Portal](https://portal.azure.com/). Klient hemligheter används i det här flödet för scenarier med webb program, där klienten kan lagra en klient hemlighet på ett säkert sätt. För interna app-scenarier (offentliga klienter) kan inte klient hemligheter lagras på ett säkert sätt, och därför används det inte i det här anropet. Om du använder en klient hemlighet måste du ändra den regelbundet. |
| grant_type | Ja | Typ av beviljande, som måste vara `refresh_token` för den här delen av Authorization Code Flow. |
| refresh_token | Ja | Den ursprungliga uppdateringstoken som hämtades i den andra delen av flödet. `offline_access`Omfånget måste användas både för auktoriserings-och Tokenbegäran för att kunna ta emot en uppdateringstoken. |
| redirect_uri | Inga | `redirect_uri`Parametern för det program där du fick auktoriseringskod. |
| omfång | Inga | En blankstegsavgränsad lista över omfång. `openid`Omfånget anger en behörighet för att logga in användaren och hämta data om användaren i form av ID-token. Den kan användas för att skicka tokens till programmets egna Server dels webb-API, som representeras av samma program-ID som klienten. `offline_access`Omfånget anger att programmet behöver en uppdateringstoken för utökad åtkomst till resurser. |

Ett lyckat token-svar ser ut så här:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| not_before | Tiden då token anses vara giltig, i epok tid. |
| token_type | Värdet för token-typ. `Bearer` är den enda typ som stöds. |
| access_token | Den signerade JWT-token som begärdes. |
| omfång | Omfattningen som token är giltig för. |
| expires_in | Hur lång tid som åtkomsttoken är giltig (i sekunder). |
| refresh_token | En OAuth 2,0-uppdateringstoken. Programmet kan använda denna token för att hämta ytterligare token när aktuell token upphör att gälla. Du kan använda uppdaterings-token för att behålla åtkomst till resurser under långa tids perioder. |

Fel svar ser ut så här:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivning |
| --------- | ----------- |
| fel | En kod som kan användas för att klassificera typer av fel som inträffar. |
| error_description | Ett meddelande som kan hjälpa till att identifiera rotor saken till ett autentiseringsfel. |

## <a name="send-a-sign-out-request"></a>Skicka en inloggningsbegäran

När du vill signera användaren från programmet räcker det inte att ta bort programmets cookies eller på annat sätt avsluta sessionen med användaren. Omdirigera användaren till Azure AD B2C för att logga ut. Om du inte gör det kan det hända att användaren kan autentisera till programmet igen utan att ange sina autentiseringsuppgifter igen. Mer information finns i [Azure AD B2C session](session-behavior.md).

Om du vill logga ut användaren omdirigerar du användaren till den `end_session` slut punkt som anges i dokumentet OpenID Connect-metadata som beskrivs ovan:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parameter | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| innehav | Ja | Namnet på din Azure AD B2C-klient |
| politik | Ja | Det användar flöde som du vill använda för att signera användaren från ditt program. |
| id_token_hint| Inga | En tidigare utfärdad ID-token för att skicka till utloggnings slut punkten som ett tips om slutanvändarens aktuella autentiserade session med klienten. `id_token_hint`Säkerställer att `post_logout_redirect_uri` är en registrerad svars-URL i Azure AD B2C program inställningar. Mer information finns i [skydda din omutloggning-omdirigering](#secure-your-logout-redirect). |
| client_id | Varken | Det program-ID som [Azure Portal](https://portal.azure.com/) tilldelats till ditt program.<br><br>\**Detta krävs när du använder `Application` konfiguration av isolerad SSO och _kräver ID-token_ i en utloggnings förfrågan har angetts till `No` .* |
| post_logout_redirect_uri | Inga | URL: en som användaren ska omdirigeras till efter en lyckad utloggning. Om den inte är inkluderad visar Azure AD B2C användaren ett allmänt meddelande. Om du inte anger något `id_token_hint` bör du inte registrera denna URL som en svars-URL i Azure AD B2C programmets inställningar. |
| state | Inga | Om en `state` parameter ingår i begäran ska samma värde visas i svaret. Programmet bör kontrol lera att `state` värdena i begäran och svaret är identiska. |

### <a name="secure-your-logout-redirect"></a>Skydda din utloggnings omdirigering

Efter utloggning omdirigeras användaren till den URI som anges i `post_logout_redirect_uri` parametern, oavsett vilka svars-URL: er som har angetts för programmet. Men om ett giltigt `id_token_hint` värde skickas och **Kräv ID-token i utloggnings begär Anden** aktive ras, verifierar Azure AD B2C att värdet för `post_logout_redirect_uri` matchar ett av programmets konfigurerade omdirigerings-URI: er innan omdirigeringen utförs. Om ingen matchande svars-URL har kon figurer ATS för programmet visas ett fel meddelande och användaren omdirigeras inte.

Information om hur du anger obligatorisk ID-token i utloggnings begär Anden finns i [Konfigurera sessionens beteende i Azure Active Directory B2C](session-behavior.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD B2C-sessionen](session-behavior.md).