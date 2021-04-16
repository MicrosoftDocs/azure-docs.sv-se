---
title: Microsoft Identity-plattformen och OAuth2.0 On-Behalf-Of-flödes-| Azure
titleSuffix: Microsoft identity platform
description: Den här artikeln beskriver hur du använder HTTP-meddelanden för att implementera tjänst-till-tjänstautentisering med hjälp av OAuth2.0 On-Behalf-Of-flödet.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1c8ea1580047910cb2d6634aad885d61e99113f3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529973"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity-plattformen och OAuth 2.0 On-Behalf-Of-flödet


OAuth 2.0 On-Behalf-Of-flödet (OBO) fungerar som ett användningsfall där ett program anropar en tjänst/ett webb-API, som i sin tur måste anropa en annan tjänst/webb-API. Tanken är att sprida den delegerade användaridentiteten och behörigheterna via begärandekedjan. För att mellannivåtjänsten ska kunna göra autentiserade begäranden till den underordnade tjänsten måste den skydda en åtkomsttoken från Microsoft Identity Platform för användarens räkning.

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder de Microsoft Authentication Libraries (MSAL) som stöds i stället för att hämta token och anropa skyddade [webb-API:er.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Ta även en titt på de [exempelappar som använder MSAL](sample-v2-code.md).


Från och med maj 2018 kan vissa implicit flöde `id_token` härledda inte användas för OBO-flöde. Ensidesappar (SPA) bör  skicka en åtkomsttoken till en konfidentiell klient på mellannivå för att utföra OBO-flöden i stället. Mer information om vilka klienter som kan utföra OBO-anrop finns i [begränsningar.](#client-limitations)

## <a name="protocol-diagram"></a>Protokolldiagram

Anta att användaren har autentiserats i ett program med [OAuth 2.0-auktoriseringskoden](v2-oauth2-auth-code-flow.md) för att bevilja flöde eller ett annat inloggningsflöde. Nu har programmet en åtkomsttoken för *API A* (token A) med användarens anspråk och medgivande till åtkomst till webb-API:et på mellannivå (API A). Nu måste API A göra en autentiserad begäran till det underordnade webb-API:et (API B).

De steg som följer utgör OBO-flödet och förklaras med hjälp av följande diagram.

![Visar flödet OAuth2.0 On-Behalf-Of](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientprogrammet skickar en begäran till API A med token A (med ett `aud` anspråk på API A).
1. API A autentiserar till slutpunkten för utfärdande av token för Microsoft-identitetsplattform och begär en token för åtkomst till API B.
1. Slutpunkten för utfärdande av tokenutfärdande för Microsoft-identitetsplattform verifierar API A:s autentiseringsuppgifter tillsammans med token A och utfärdar åtkomsttoken för API B (token B) till API A.
1. Token B anges av API A i auktoriseringshuvudet för begäran till API B.
1. Data från den skyddade resursen returneras av API B till API A och sedan till klienten.

I det här scenariot har mellannivåtjänsten ingen användarinteraktion för att få användarens medgivande till åtkomst till det underordnade API:et. Därför visas alternativet att bevilja åtkomst till det underordnade API:et direkt som en del av medgivandesteget under autentiseringen. Information om hur du ställer in detta för din app finns i [Få medgivande för mellannivåprogrammet](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Begäran om åtkomsttoken på mellannivå

Om du vill begära en åtkomsttoken gör du en HTTP POST till den klientspecifika tokenslutpunkten för Microsoft-identitetsplattformen med följande parametrar.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Det finns två fall beroende på om klientprogrammet väljer att skyddas av en delad hemlighet eller ett certifikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Första fallet: Begäran om åtkomsttoken med en delad hemlighet

När du använder en delad hemlighet innehåller en begäran om tjänst-till-tjänst-åtkomsttoken följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typ av tokenbegäran. För en begäran som använder en JWT måste värdet vara `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Obligatorisk | Program-ID:t (klienten) [som Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelat till din app. |
| `client_secret` | Obligatorisk | Den klienthemlighet som du genererade för din app Azure Portal - Appregistreringar sidan. |
| `assertion` | Obligatorisk | Den åtkomsttoken som skickades till API:et på mellannivå.  Denna token måste ha ett målgruppsanspråk ( ) för appen som gör `aud` denna OBO-begäran (appen som betecknas av `client-id` fältet ). Program kan inte lösa in en token för en annan app (så om en klient skickar ett API till en token som är avsedd för MS Graph kan INTE API:et lösa in den med hjälp av OBO.  Den bör i stället avvisa token).  |
| `scope` | Obligatorisk | En blankstegsavgränsad lista över omfång för tokenbegäran. Mer information finns i [omfång.](v2-permissions-and-consent.md) |
| `requested_token_use` | Obligatorisk | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges till `on_behalf_of` . |

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken och en uppdateringstoken `user.read` med omfång för https://graph.microsoft.com webb-API:et.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Andra fallet: Begäran om åtkomsttoken med ett certifikat

En begäran om tjänst-till-tjänst-åtkomsttoken med ett certifikat innehåller följande parametrar:

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| `grant_type` | Krävs | Typ av tokenbegäran. För en begäran som använder en JWT måste värdet vara `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Obligatorisk |  Program-ID:t (klienten) [som Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) har tilldelat till din app. |
| `client_assertion_type` | Obligatorisk | Värdet måste vara `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Obligatorisk | En försäkran (en JSON-webbtoken) som du behöver för att skapa och signera med certifikatet som du registrerade som autentiseringsuppgifter för ditt program. Information om hur du registrerar certifikatet och formatet för försäkran finns i [certifikatautentiseringsuppgifter.](active-directory-certificate-credentials.md) |
| `assertion` | Obligatorisk |  Den åtkomsttoken som skickades till API:et på mellannivå.  Denna token måste ha ett målgruppsanspråk ( ) för appen som gör `aud` denna OBO-begäran (appen som betecknas av `client-id` fältet ). Program kan inte lösa in en token för en annan app (så om en klient skickar ett API till en token som är avsedd för MS Graph kan INTE API:et lösa in den med hjälp av OBO.  Den bör i stället avvisa token).  |
| `requested_token_use` | Obligatorisk | Anger hur begäran ska bearbetas. I OBO-flödet måste värdet anges till `on_behalf_of` . |
| `scope` | Obligatorisk | En blankstegsavgränsad lista över omfång för tokenbegäran. Mer information finns i [omfång.](v2-permissions-and-consent.md)|

Observera att parametrarna är nästan desamma som i fallet med begäran av delad hemlighet, förutom att parametern ersätts `client_secret` med två parametrar: a `client_assertion_type` och `client_assertion` .

#### <a name="example"></a>Exempel

Följande HTTP POST begär en åtkomsttoken med `user.read` omfång för https://graph.microsoft.com webb-API:et med ett certifikat.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Svar på åtkomsttoken på mellannivå

Ett lyckat svar är ett JSON OAuth 2.0-svar med följande parametrar.

| Parameter | Beskrivning |
| --- | --- |
| `token_type` | Anger värdet för tokentyp. Den enda typ som Microsoft Identity-plattformen stöder är `Bearer` . Mer information om bearer-token finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| `scope` | Omfånget för åtkomst som beviljas i token. |
| `expires_in` | Hur lång tid, i sekunder, som åtkomsttoken är giltig. |
| `access_token` | Den begärda åtkomsttoken. Den anropande tjänsten kan använda denna token för att autentisera till den mottagande tjänsten. |
| `refresh_token` | Uppdateringstoken för den begärda åtkomsttoken. Den anropande tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella åtkomsttoken upphör att gälla. Uppdateringstoken anges bara om `offline_access` omfånget begärdes. |

### <a name="success-response-example"></a>Exempel på lyckat svar

I följande exempel visas ett lyckat svar på en begäran om en åtkomsttoken för https://graph.microsoft.com webb-API:et.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

Ovanstående åtkomsttoken är en v1.0-formaterad token för Microsoft Graph. Det beror på att tokenformatet baseras på den **resurs som** används och inte är relaterade till de slutpunkter som används för att begära det. Den Microsoft Graph är konfigurerad för att acceptera v1.0-token, så Microsoft Identity Platform skapar v1.0-åtkomsttoken när en klient begär token för Microsoft Graph. Andra appar kan indikera att de vill ha v2.0-formattoken, v1.0-format-token eller till och med upphovsrättsskyddade eller krypterade tokenformat.  Både v1.0- och v2.0-slutpunkterna kan generera något av tokenformaten – på så sätt kan resursen alltid få rätt format för token oavsett hur eller var token begärdes av klienten. 

Endast program bör titta på åtkomsttoken. Klienterna **får inte** inspektera dem. Att granska åtkomsttoken för andra appar i koden leder till att din app oväntat bryts när appen ändrar formatet för sina token eller börjar kryptera dem. 

### <a name="error-response-example"></a>Exempel på felsvar

Ett felsvar returneras av [tokenslutpunkten](../authentication/concept-mfa-howitworks.md)när du försöker hämta en åtkomsttoken för det underordnade API:et, om nedströms-API:et har en princip för villkorsstyrd åtkomst (till exempel multifaktorautentisering ) inställd på den. Tjänsten på mellannivå bör visa det här felet i klientprogrammet så att klientprogrammet kan tillhandahålla användarinteraktion för att uppfylla principen för villkorsstyrd åtkomst.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Använda åtkomsttoken för att få åtkomst till den skyddade resursen

Nu kan tjänsten på mellannivå använda den token som inhämtas ovan för att göra autentiserade begäranden till det underordnade webb-API:et genom att ange token i `Authorization` -huvudet.

### <a name="example"></a>Exempel

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-försäkran som erhållits med ett OAuth2.0 OBO-flöde

Vissa OAuth-baserade webbtjänster måste ha åtkomst till andra webbtjänst-API:er som accepterar SAML-försäkran i icke-interaktiva flöden. Azure Active Directory kan tillhandahålla en SAML-försäkran som svar på ett On-Behalf-Of-flöde som använder en SAML-baserad webbtjänst som en målresurs.

Det här är ett icke-standardtillägg till flödet OAuth 2.0 On-Behalf-Of som gör att ett OAuth2-baserat program kan komma åt webbtjänst-API-slutpunkter som använder SAML-token.

> [!TIP]
> När du anropar en SAML-skyddad webbtjänst från en klientwebbapp kan du helt enkelt anropa API:et och initiera ett normalt interaktivt autentiseringsflöde med användarens befintliga session. Du behöver bara använda ett OBO-flöde när ett tjänst-till-tjänst-anrop kräver en SAML-token för att tillhandahålla användarkontext.
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Hämta en SAML-token med hjälp av en OBO-begäran med en delad hemlighet

En tjänst-till-tjänst-begäran för en SAML-försäkran innehåller följande parametrar:

| Parameter | Typ | Description |
| --- | --- | --- |
| grant_type |krävs | Typ av tokenbegäran. För en begäran som använder en JWT måste värdet vara **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Påstående |krävs | Värdet för den åtkomsttoken som används i begäran.|
| client_id |krävs | App-ID:t som tilldelades till den anropande tjänsten under registreringen med Azure AD. Om du vill hitta app-ID:t i Azure Portal väljer du **Active Directory,** väljer katalogen och väljer sedan programnamnet. |
| client_secret |krävs | Nyckeln som registrerats för den anropande tjänsten i Azure AD. Det här värdet bör ha antecknats vid tidpunkten för registreringen. |
| resource |krävs | App-ID-URI för den mottagande tjänsten (skyddad resurs). Det här är den resurs som ska vara målgruppen för SAML-token. Om du vill hitta appens ID-URI i Azure Portal väljer **du Active Directory** och sedan katalogen. Välj programnamnet, välj **Alla inställningar** och välj sedan **Egenskaper.** |
| requested_token_use |krävs | Anger hur begäran ska bearbetas. I flödet On-Behalf-Of måste värdet vara **on_behalf_of**. |
| requested_token_type | krävs | Anger vilken typ av token som begärs. Värdet kan vara **urn:ietf:params:oauth:token-type:saml2** eller **urn:ietf:params:oauth:token-type:saml1** beroende på kraven för den använda resursen. |

Svaret innehåller en SAML-token som är kodad i UTF8 och Base64url.

- **SubjectConfirmationData** för en SAML-försäkran som kommer från ett OBO-anrop: Om målprogrammet kräver ett mottagarvärde i **SubjectConfirmationData** måste värdet vara en svars-URL utan jokertecken i resursprogramkonfigurationen.
- **Noden SubjectConfirmationData: Noden** kan inte innehålla ett **InResponseTo-attribut** eftersom den inte ingår i ett SAML-svar. Programmet som tar emot SAML-token måste kunna acceptera SAML-försäkran utan ett **InResponseTo-attribut.**

- **Medgivande:** Medgivande måste ha beviljats för att ta emot en SAML-token som innehåller användardata i ett OAuth-flöde. Information om behörigheter och att få administratörens medgivande finns [i Behörigheter och medgivande i Azure Active Directory v1.0-slutpunkten](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Svar med SAML-försäkran

| Parameter | Beskrivning |
| --- | --- |
| token_type |Anger värdet för tokentyp. Den enda typ som Azure AD stöder är **Bearer**. Mer information om bearer-token finns [i OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| omfång |Omfånget för åtkomst som beviljas i token. |
| expires_in |Hur lång tid åtkomsttoken är giltig (i sekunder). |
| expires_on |Den tid då åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC till förfallotiden. Det här värdet används för att fastställa livslängden för cachelagrade token. |
| resource |App-ID-URI för den mottagande tjänsten (skyddad resurs). |
| access_token |Parametern som returnerar SAML-försäkran. |
| refresh_token |Uppdateringstoken. Den anropande tjänsten kan använda denna token för att begära en annan åtkomsttoken när den aktuella SAML-försäkran upphör att gälla. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Resurs: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>Få medgivande för mellannivåprogrammet

Beroende på arkitekturen eller användningen av ditt program kan du överväga olika strategier för att säkerställa att OBO-flödet lyckas. I samtliga fall är slutmålet att säkerställa att rätt medgivande ges så att klientappen kan anropa mellannivåappen och mellannivåappen har behörighet att anropa backend-resursen.

> [!NOTE]
> Tidigare hade Microsoft-konto (personliga konton) inte stöd för fältet "Känt klientprogram" och kunde inte heller visa kombinerat medgivande.  Detta har lagts till och alla appar på Microsoft Identity-plattformen kan använda den kända klientprogram metoden för att få medgivande för OBO-anrop.

### <a name="default-and-combined-consent"></a>/.default och kombinerat medgivande

Mellannivåprogrammet lägger till klienten i listan över kända klientprogram i manifestet, och sedan kan klienten utlösa ett kombinerat medgivandeflöde för både sig själv och mellannivåprogrammet. På Microsoft Identity-plattformen görs detta med hjälp av [ `/.default` omfånget](v2-permissions-and-consent.md#the-default-scope). När du utlöser en skärm för medgivande med hjälp av kända klientprogram och visar skärmen för medgivande behörigheter för både klienten till MELLANNIVÅ-API:et och begär även de behörigheter som krävs av `/.default` API:et på mellannivå.  Användaren ger medgivande för båda programmen och sedan fungerar OBO-flödet.

### <a name="pre-authorized-applications"></a>Förtillåtna program

Resurser kan indikera att ett visst program alltid har behörighet att ta emot vissa omfång. Detta är främst användbart för att göra anslutningar mellan en klient och en backend-resurs smidigare. En resurs kan deklarera flera förtillåtna program – alla sådana program kan begära dessa behörigheter i ett OBO-flöde och ta emot dem utan att användaren ger sitt medgivande.

### <a name="admin-consent"></a>Administratörsmedgivande

En klientorganisationsadministratör kan garantera att program har behörighet att anropa sina nödvändiga API:er genom att tillhandahålla administratörsmedgivande för programmet på mellannivå. För att göra detta kan administratören hitta mellannivåprogrammet i sin klientorganisation, öppna sidan nödvändiga behörigheter och välja att ge behörighet för appen. Mer information om administratörsmedgivande finns i dokumentationen [om medgivande och behörigheter.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Användning av ett enda program

I vissa fall kanske du bara har en enda parkoppling av mellannivå- och klientklienten. I det här scenariot kan det vara enklare att göra det här till ett enda program, och därmed helt och hållet negera behovet av ett program på mellannivå. Om du vill autentisera mellan frontend-API:et och webb-API:et kan du använda cookies, en id_token eller en åtkomsttoken som begärs för själva programmet. Begär sedan medgivande från det här enskilda programmet till backend-resursen.

## <a name="client-limitations"></a>Klientbegränsningar

Om en klient använder det implicita flödet för att hämta ett id_token och klienten även har jokertecken i en svars-URL kan id_token inte användas för ett OBO-flöde.  Åtkomsttoken som förvärvas via det implicita beviljandeflödet kan dock fortfarande lösas in av en konfidentiell klient även om den initierande klienten har en registrerad svars-URL med jokertecken.

## <a name="next-steps"></a>Nästa steg

Läs mer om OAuth 2.0-protokollet och ett annat sätt att utföra tjänst-till-tjänst-autentisering med klientautentiseringsuppgifter.

* [Beviljande av autentiseringsuppgifter för OAuth 2.0-klient i Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-kodflöde i Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Använda `/.default` omfånget](v2-permissions-and-consent.md#the-default-scope)
