---
title: Förstå OAuth 2,0 Authorization Code Flow i Azure AD
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att ge åtkomst till webb program och webb-API: er i din klient med hjälp av Azure Active Directory och OAuth 2,0.'
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5f987ab15201e4c4dabf147ac468184881e9ed17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85551639"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Auktorisera åtkomst till Azure Active Directory-webbprogram med beviljandeflödet för OAuth 2.0-kod

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Om du inte talar om för servern vilken resurs du planerar att anropa, kommer servern inte att utlösa principer för villkorlig åtkomst för den resursen. För att du ska kunna använda MFA-utlösaren måste du inkludera en resurs i din URL. 
>

I Azure Active Directory (Azure AD) används OAuth 2.0 för att du ska kunna bevilja åtkomst till webbprogram och webb-API:er i din klientorganisation. Den här guiden är språk oberoende och beskriver hur du skickar och tar emot HTTP-meddelanden utan att använda några av våra [bibliotek med öppen källkod](active-directory-authentication-libraries.md).

OAuth 2,0 Authorization Code Flow beskrivs i [avsnittet 4,1 i oauth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.1). Den används för att utföra autentisering och auktorisering i de flesta program typer, inklusive Web Apps och internt installerade appar.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrera ditt program med din AD-klient
Registrera först ditt program med din Azure Active Directory (Azure AD)-klient. Det ger dig en program-ID för ditt program och låter det ta emot tokens.

1. Logga in på [Azure-portalen](https://portal.azure.com).
   
1. Välj din Azure AD-klient genom att välja ditt konto i det övre högra hörnet på sidan, följt av att välja **växel katalog** navigering och sedan välja lämplig klient. 
   - Hoppa över det här steget om du bara har en Azure AD-klient under ditt konto, eller om du redan har valt rätt Azure AD-klient.
   
1. I Azure Portal söker du efter och väljer **Azure Active Directory**.
   
1. På den **Azure Active Directory** vänstra menyn väljer du **registrerade appar** och väljer sedan **ny registrering**.
   
1. Följ anvisningarna och skapa ett nytt program. Det spelar ingen roll om det är ett webb program eller ett offentligt klient program (mobilt & Desktop) för den här självstudien, men om du vill ha specifika exempel för webb program eller offentliga klient program kan du läsa våra [snabb starter](v1-overview.md).
   
   - **Namn** är appens namn och beskriver appen för användarna.
   - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
   - Ange **omdirigerings-URI**. För webb program är detta den grundläggande URL: en för din app där användarna kan logga in.  Till exempel `http://localhost:12345`. För en offentlig klient (mobil & Desktop) använder Azure AD den för att returnera svar från token. Ange ett specifikt värde för ditt program.  Till exempel `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. När du har slutfört registreringen tilldelar Azure AD programmet ett unikt klient-ID ( **program-ID**). Du behöver det här värdet i nästa avsnitt, så kopiera det från program sidan.
   
1. Om du vill hitta ditt program i Azure Portal väljer du **Appregistreringar** och väljer sedan **Visa alla program**.

## <a name="oauth-20-authorization-flow"></a>OAuth 2,0-auktoriseringsarkiv

På hög nivå ser hela auktoriseringsarkivet för ett program ut ungefär så här:

![Kod flöde för OAuth-auth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Begär en auktoriseringskod

Kod flödet för auktorisering börjar med klienten som dirigerar användaren till `/authorize` slut punkten. I den här förfrågan anger klienten de behörigheter som krävs för att hämta från användaren. Du kan få behörighets slut punkten för OAuth 2,0 för din klient genom att välja **Appregistreringar > slut punkter** i Azure Portal.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| tenant |krävs |`{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klient identifierare, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient oberoende token |
| client_id |krävs |Det program-ID som tilldelats din app när du registrerade den med Azure AD. Du hittar det här i Azure Portal. Klicka på **Azure Active Directory** på sid panelen tjänster, klicka på **Appregistreringar** och välj programmet. |
| response_type |krävs |Måste innehålla `code` för flödet av auktoriseringskod. |
| redirect_uri |rekommenderas |Appens redirect_uri, där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. Använd standardvärdet för interna & mobila appar `https://login.microsoftonline.com/common/oauth2/nativeclient` . |
| response_mode |valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Kan vara `query` , `fragment` eller `form_post` . `query` innehåller koden som en frågesträngparametern i omdirigerings-URI: n. Om du begär en ID-token med det implicita flödet kan du inte använda `query` enligt vad som anges i [OpenID-specifikationen](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Om du bara begär koden kan du använda `query` , `fragment` , eller `form_post` . `form_post` kör ett inlägg som innehåller koden för omdirigerings-URI: n. Standardvärdet är `query` för ett kod flöde.  |
| state |rekommenderas |Ett värde som ingår i begäran som också returneras i svaret från token. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| resource | rekommenderas |App-ID-URI för mål webb-API (säker resurs). Du hittar app-ID-URI: n i Azure-portalen genom att klicka på **Azure Active Directory**, klicka på **program registreringar**, öppna programmets **inställnings** sida och sedan klicka på **Egenskaper**. Det kan också vara en extern resurs som `https://graph.microsoft.com` . Detta krävs i någon av antingen auktoriserings-eller Tokenbegäran. För att se till att färre autentiseringar begärs, kan du placera det i auktoriseringsbegäran för att se till att medgivande tas emot från användaren. |
| omfång | **ignoreras** | För v1 Azure AD-appar måste omfattningar konfigureras statiskt i Azure-portalen under program **inställningarna**, vilka **behörigheter som krävs**. |
| visas |valfri |Ange vilken typ av användar interaktion som krävs.<p> Giltiga värden är: <p> *login*: användaren måste uppmanas att autentisera igen. <p> *select_account*: användaren uppmanas att välja ett konto och avbryta enkel inloggning. Användaren kan välja ett befintligt inloggat konto, ange sina autentiseringsuppgifter för ett Sparad konto eller välja att använda ett annat konto helt och hållet. <p> *medgivande*: användar medgivande har beviljats, men måste uppdateras. Användaren bör tillfrågas om samtycke. <p> *admin_consent*: en administratör bör tillfrågas om godkännande för alla användares räkning i organisationen |
| login_hint |valfri |Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar använder ofta den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med hjälp av `preferred_username` anspråket. |
| domain_hint |valfri |Innehåller ett tips om den klient eller domän som användaren ska använda för att logga in. Värdet för domain_hint är en registrerad domän för klient organisationen. Om klienten är federerad till en lokal katalog dirigeras AAD om till den angivna klient Federations servern. |
| code_challenge_method | rekommenderas    | Den metod som används för att koda `code_verifier` för `code_challenge` parametern. Kan vara en av `plain` eller `S256` . Om det utesluts, `code_challenge` antas vara oformaterad text om `code_challenge` ingår. Azure AAD v 1.0 stöder både `plain` och `S256` . Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | rekommenderas    | Används för att skydda auktoriseringskod-bidrag via bevis nyckel för Code Exchange (PKCE) från en intern eller offentlig klient. Krävs om ingår `code_challenge_method` . Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Om användaren är en del av en organisation kan en administratör av organisationen godkänna eller avböja användarens räkning eller tillåta att användaren godkänner det. Användaren får bara tillåtelse om administratören tillåter det.
>
>

Vid det här tillfället uppmanas användaren att ange sina autentiseringsuppgifter och samtycker till de behörigheter som begärs av appen i Azure-portalen. När användaren autentiserar och ger tillåtelse skickar Azure AD ett svar till din app på `redirect_uri` adressen i din begäran med koden.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar kan se ut så här:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beskrivning |
| --- | --- |
| admin_consent |Värdet är true om en administratör godkänner en begäran om medgivande. |
| kod |Auktoriseringskod som programmet begärde. Programmet kan använda auktoriseringskod för att begära en åtkomsttoken för mål resursen. |
| session_state |Ett unikt värde som identifierar den aktuella användarsessionen. Det här värdet är ett GUID, men bör behandlas som ett ogenomskinligt värde som skickas utan undersökning. |
| state |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Det är en bra idé för programmet att kontrol lera att tillstånds värden i begäran och svaret är identiska innan du använder svaret. Detta hjälper till att identifiera [CSRF-attacker (Cross-Site request förfalskning)](https://tools.ietf.org/html/rfc6749#section-10.12) mot klienten. |

### <a name="error-response"></a>Fel svar
Fel svar kan också skickas till `redirect_uri` så att programmet kan hantera dem på rätt sätt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |Ett fel kods värde som definieras i avsnitt 5,2 i [Authorization Framework för OAuth 2,0](https://tools.ietf.org/html/rfc6749). I nästa tabell beskrivs de fel koder som returneras av Azure AD. |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsett att vara användarvänligt. |
| state |State-värdet är ett slumpmässigt genererat värde som inte kan återanvändas och som skickas i begäran och som returneras i svaret för att förhindra förfalskning av CSRF-attacker (Cross-Site Request). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Felkoder för slut punkts fel i tillstånd
I följande tabell beskrivs de olika fel koderna som kan returneras i `error` parametern för fel svaret.

| Felkod | Beskrivning | Klient åtgärd |
| --- | --- | --- |
| invalid_request |Protokoll fel, till exempel en obligatorisk parameter som saknas. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| unauthorized_client |Klient programmet har inte behörighet att begära en auktoriseringskod. |Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| access_denied |Resurs ägare nekade medgivande |Klient programmet kan meddela användaren att den inte kan fortsätta om inte användaren samtycks. |
| unsupported_response_type |Auktoriseringsservern stöder inte svars typen i begäran. |Åtgärda och skicka begäran på nytt. Detta är ett utvecklings fel som vanligt vis fångas under den första testningen. |
| server_error |Ett oväntat fel uppstod i servern. |Gör om begäran. Dessa fel kan orsakas av tillfälliga förhållanden. Klient programmet kan förklara för användaren att dess svar har fördröjts på grund av ett tillfälligt fel. |
| temporarily_unavailable |Servern är tillfälligt upptagen och kan inte hantera begäran. |Gör om begäran. Klient programmet kan förklara för användaren att dess svar har fördröjts på grund av ett tillfälligt tillstånd. |
| invalid_resource |Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så har den inte kon figurer ATS korrekt. |Detta anger att resursen, om den finns, inte har kon figurer ATS i klient organisationen. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Använd auktoriseringskod för att begära en åtkomsttoken
Nu när du har skaffat en auktoriseringskod och har beviljats behörighet av användaren kan du lösa in koden för en åtkomsttoken till önskad resurs genom att skicka en POST-begäran till `/token` slut punkten:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| tenant |krävs |`{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klient identifierare, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient oberoende token |
| client_id |krävs |Det program-ID som tilldelats din app när du registrerade den med Azure AD. Du hittar det här i Azure Portal. Program-ID visas i inställningarna för appens registrering. |
| grant_type |krävs |Måste vara `authorization_code` för flöde för auktoriseringskod. |
| kod |krävs |Det `authorization_code` som du köpte i föregående avsnitt |
| redirect_uri |krävs | En `redirect_uri` registrerad i klient programmet. |
| client_secret |krävs för webb program, tillåts inte för offentliga klienter |Program hemligheten som du skapade i Azure Portal för din app under **nycklar**. Den kan inte användas i en intern app (offentlig klient) eftersom client_secrets inte kan lagras på ett tillförlitligt sätt på enheter. Det krävs för webbappar och webb-API: er (alla konfidentiella klienter) som kan lagra `client_secret` säkert på Server sidan. Client_secret ska vara URL-kodad innan den skickas. |
| resource | rekommenderas |App-ID-URI för mål webb-API (säker resurs). Du hittar app-ID-URI: n i Azure-portalen genom att klicka på **Azure Active Directory**, klicka på **program registreringar**, öppna programmets **inställnings** sida och sedan klicka på **Egenskaper**. Det kan också vara en extern resurs som `https://graph.microsoft.com` . Detta krävs i någon av antingen auktoriserings-eller Tokenbegäran. För att se till att färre autentiseringar begärs, kan du placera det i auktoriseringsbegäran för att se till att medgivande tas emot från användaren. Om både begäran om auktorisering och Tokenbegäran, måste resurs parametrarna överensstämma. | 
| code_verifier | valfri | Samma code_verifier som användes för att hämta authorization_code. Krävs om PKCE användes i begäran om beviljande av auktoriseringskod. Mer information finns i [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Du hittar app-ID-URI: n i Azure-portalen genom att klicka på **Azure Active Directory**, klicka på **program registreringar**, öppna programmets **inställnings** sida och sedan klicka på **Egenskaper**.

### <a name="successful-response"></a>Lyckat svar
Azure AD returnerar en [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) efter ett lyckat svar. För att minimera nätverks anrop från klient programmet och deras associerade latens, ska klient programmet cachelagra åtkomsttoken för token som anges i OAuth 2,0-svaret. Du fastställer livs längden för token genom att använda antingen `expires_in` `expires_on` parameter värden eller.

Om en webb-API-resurs returnerar en `invalid_token` felkod, kan det betyda att resursen har fastställt att token har upphört att gälla. Om klient-och resurs klock tiderna skiljer sig (kallas "tids skevning") kan det hända att den token som upphör att gälla innan token rensas från klientens cacheminne. Om detta inträffar rensar du token från cachen, även om den fortfarande ligger inom dess beräknade livs längd.

Ett lyckat svar kan se ut så här:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den begärda åtkomsttoken.  Detta är en ogenomskinlig sträng – den beror på vad resursen förväntar sig att ta emot och är inte avsedd för klienten att titta på. Appen kan använda denna token för att autentisera till den skyddade resursen, till exempel ett webb-API. |
| token_type |Anger värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. Mer information om Bearer-token finns i [OAuth 2.0 Authorization Framework: användningen av token token (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Hur länge åtkomsttoken är giltig (i sekunder). |
| expires_on |Tiden då åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. Det här värdet används för att fastställa livs längden för cachelagrade token. |
| resource |URI för app-ID för webb-API (säker resurs). |
| omfång |Personifierings behörigheter som beviljats till klient programmet. Standard behörigheten är `user_impersonation` . Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. |
| refresh_token |En OAuth 2,0-uppdateringstoken. Appen kan använda denna token för att hämta ytterligare åtkomsttoken när den aktuella åtkomsttoken upphör att gälla. Uppdaterade token är långvariga och kan användas för att bevara åtkomsten till resurser under längre tids perioder. |
| id_token |En osignerad JSON Web Token (JWT) som representerar en [ID-token](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Appen kan base64Url avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men det bör inte förlita sig på dem för några tillstånds-eller säkerhets gränser. |

Mer information om JSON-webbtoken finns i [utkast specifikationen för JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Mer information `id_tokens` finns i [v 1.0 OpenID Connect-flödet](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Fel svar
Fel vid slut punkt för utfärdande av token är HTTP-felkoder eftersom klienten anropar slut punkten för utfärdande av token direkt. Förutom HTTP-statuskoden returnerar slut punkten för utfärdande av Azure AD-token även ett JSON-dokument med objekt som beskriver felet.

Ett exempel fel svar kan se ut så här:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |
| error_codes |En lista med STS-specificerade felkoder som kan hjälpa dig i diagnostik. |
| timestamp |Tiden då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan hjälpa i diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa dig i diagnostiken mellan komponenter. |

#### <a name="http-status-codes"></a>HTTP-statuskoder
I följande tabell visas de HTTP-statuskod som slut punkten för utfärdande av token returnerar. I vissa fall räcker det med fel koden för att beskriva svaret, men om det finns fel måste du parsa det tillhör ande JSON-dokumentet och undersöka dess felkod.

| HTTP-kod | Beskrivning |
| --- | --- |
| 400 |Standard-HTTP-kod. Används i de flesta fall och är vanligt vis på grund av en felformaterad begäran. Åtgärda och skicka begäran på nytt. |
| 401 |Autentiseringen misslyckades. Förfrågan saknar till exempel parametern client_secret. |
| 403 |Auktoriseringen misslyckades. Användaren har till exempel inte behörighet att komma åt resursen. |
| 500 |Ett internt fel har inträffat på tjänsten. Gör om begäran. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Felkoder för token slut punkts fel
| Felkod | Beskrivning | Klient åtgärd |
| --- | --- | --- |
| invalid_request |Protokoll fel, till exempel en obligatorisk parameter som saknas. |Åtgärda och skicka begäran på nytt |
| invalid_grant |Auktoriseringskod är ogiltig eller har upphört att gälla. |Testa en ny begäran till `/authorize` slut punkten |
| unauthorized_client |Den autentiserade klienten har inte behörighet att använda den här typen av Authorization-beviljande. |Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| invalid_client |Klientautentisering misslyckades. |Klientautentiseringsuppgifterna är inte giltiga. För att åtgärda detta uppdaterar program administratören autentiseringsuppgifterna. |
| unsupported_grant_type |Auktoriseringsservern stöder inte typen för auktoriserings beviljande. |Ändra beviljad typ i begäran. Den här typen av fel bör bara inträffa under utvecklingen och identifieras under den första testningen. |
| invalid_resource |Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så har den inte kon figurer ATS korrekt. |Detta anger att resursen, om den finns, inte har kon figurer ATS i klient organisationen. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| interaction_required |Begäran kräver användar interaktion. Till exempel krävs ytterligare ett autentiserings steg. | I stället för en icke-interaktiv begäran försöker du igen med en interaktiv auktoriseringsbegäran för samma resurs. |
| temporarily_unavailable |Servern är tillfälligt upptagen och kan inte hantera begäran. |Gör om begäran. Klient programmet kan förklara för användaren att dess svar har fördröjts på grund av ett tillfälligt tillstånd. |

## <a name="use-the-access-token-to-access-the-resource"></a>Använd åtkomsttoken för att få åtkomst till resursen
Nu när du har skaffat en kan `access_token` du använda token i begär anden till webb-API: er, genom att inkludera den i `Authorization` rubriken. I [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) -specifikationen beskrivs hur du använder Bearer-token i HTTP-begäranden för att komma åt skyddade resurser.

### <a name="sample-request"></a>Exempel förfrågan
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Fel svar
Skyddade resurser som implementerar RFC 6750 utfärdar HTTP-statuskod. Om begäran inte innehåller autentiseringsuppgifter för autentisering eller saknar token, innehåller svaret en `WWW-Authenticate` rubrik. När en begäran Miss lyckas svarar resurs servern med HTTP-statuskod och en felkod.

Följande är ett exempel på ett misslyckat svar när klientbegäran inte inkluderar Bearer-token:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Fel parametrar
| Parameter | Beskrivning |
| --- | --- |
| authorization_uri |Den URI (fysiska slut punkten) för auktoriseringsservern. Det här värdet används också som en uppslags nyckel för att få mer information om servern från en identifierings slut punkt. <p><p> Klienten måste verifiera att auktoriseringsservern är betrodd. När resursen skyddas av Azure AD räcker det med att verifiera att URL: en börjar med `https://login.microsoftonline.com` eller ett annat värdnamn som stöds av Azure AD. En klient organisations resurs måste alltid returnera en klient-Specific Authorization URI. |
| fel |Ett fel kods värde som definieras i avsnitt 5,2 i [Authorization Framework för OAuth 2,0](https://tools.ietf.org/html/rfc6749). |
| error_description |En mer detaljerad beskrivning av felet. Det här meddelandet är inte avsett att vara användarvänligt. |
| resource_id |Returnerar resursens unika identifierare. Klient programmet kan använda den här identifieraren som `resource` parameter värde när den begär en token för resursen. <p><p> Det är viktigt att klient programmet kan verifiera det här värdet, annars kan en skadlig tjänst medföra en **utöknings bar behörighets** attack <p><p> Den rekommenderade strategin för att förhindra ett angrepp är att kontrol lera att `resource_id` matchar basen i webb-API-URL: en som används. Om till exempel används `https://service.contoso.com/data` `resource_id` kan det vara `https://service.contoso.com/` . Klient programmet måste avvisa en `resource_id` som inte börjar med bas-URL, såvida det inte finns ett tillförlitligt alternativ för att verifiera id: t. |

#### <a name="bearer-scheme-error-codes"></a>Fel koder för Bearer-schema
I RFC 6750-specifikationen definieras följande fel för resurser som använder WWW-Authenticate-huvudet och Bearer-schemat i svaret.

| HTTP-statuskod | Felkod | Beskrivning | Klient åtgärd |
| --- | --- | --- | --- |
| 400 |invalid_request |Begäran är inte korrekt formaterad. Den kan till exempel sakna en parameter eller använda samma parameter två gånger. |Åtgärda felet och försök att utföra begäran igen. Den här typen av fel bör bara inträffa under utvecklingen och identifieras vid inledande testning. |
| 401 |invalid_token |Åtkomsttoken saknas, är ogiltig eller har återkallats. Värdet för parametern error_description ger ytterligare information. |Begär en ny token från auktoriseringsservern. Ett oväntat fel har uppstått om den nya token Miss lyckas. Skicka ett fel meddelande till användaren och försök igen efter slumpmässiga fördröjningar. |
| 403 |insufficient_scope |Åtkomsttoken innehåller inte de personifierings behörigheter som krävs för att komma åt resursen. |Skicka en ny auktoriseringsbegäran till behörighets slut punkten. Om svaret innehåller omfattnings parametern använder du värdet scope i begäran till resursen. |
| 403 |insufficient_access |Ämnet för token har inte de behörigheter som krävs för att få åtkomst till resursen. |Be användaren att använda ett annat konto eller begära behörighet till den angivna resursen. |

## <a name="refreshing-the-access-tokens"></a>Uppdatera åtkomsttoken

Åtkomsttoken är korta och måste uppdateras när de har gått ut för att fortsätta att få åtkomst till resurser. Du kan uppdatera `access_token` genom att skicka en annan `POST` begäran till `/token` slut punkten, men den här gången ger i `refresh_token` stället för `code` .  Uppdateringstoken är giltiga för alla resurser som din klient redan har gett tillåtelse att komma åt, och en uppdateringstoken som utfärdats på en begäran för `resource=https://graph.microsoft.com` kan användas för att begära en ny åtkomsttoken för `resource=https://contoso.com/api` . 

Uppdaterade token har inte angivna livs längder. Normalt är livs längden för uppdateringstoken relativt lång. Men i vissa fall går det inte att uppdatera token, återkallas eller saknar tillräcklig behörighet för önskad åtgärd. Ditt program måste vänta och hantera fel som returneras av slut punkten för utfärdande av token korrekt.

När du får ett svar med ett uppdaterings-token tar du bort den aktuella uppdateringstoken och begär en ny auktoriseringskod eller åtkomsttoken. I synnerhet när du använder en uppdateringstoken i flödet bevilja flöde, om du får ett svar med `interaction_required` `invalid_grant` -eller-felkoderna, tar du bort uppdateringstoken och begär en ny auktoriseringskod.

En exempel förfrågan till den **klient-/regionsspecifika** slut punkten (du kan också använda den **vanliga** slut punkten) för att få en ny åtkomsttoken med hjälp av en uppdateringstoken ser ut så här:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar på token kommer att se ut så här:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parameter | Beskrivning |
| --- | --- |
| token_type |Tokentyp. Det enda värde som stöds är **Bearer**. |
| expires_in |Den återstående livstiden för token i sekunder. Ett typiskt värde är 3600 (en timme). |
| expires_on |Datum och tid då token upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. |
| resource |Identifierar den skyddade resurs som åtkomst-token kan användas för åtkomst till. |
| omfång |Personifierings behörigheter som beviljats till det interna klient programmet. Standard behörigheten är **user_impersonation**. Ägaren till mål resursen kan registrera alternativa värden i Azure AD. |
| access_token |Den nya åtkomsttoken som begärdes. |
| refresh_token |En ny OAuth 2,0-refresh_token som kan användas för att begära nya åtkomsttoken när den som anges i det här svaret upphör att gälla. |

### <a name="error-response"></a>Fel svar
Ett exempel fel svar kan se ut så här:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |
| error_codes |En lista med STS-specificerade felkoder som kan hjälpa dig i diagnostik. |
| timestamp |Tiden då felet inträffade. |
| trace_id |En unik identifierare för begäran som kan hjälpa i diagnostik. |
| correlation_id |En unik identifierare för begäran som kan hjälpa dig i diagnostiken mellan komponenter. |

En beskrivning av felkoderna och den rekommenderade klient åtgärden finns i [felkoder för fel i token slut punkt](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure AD v 1.0-slutpunkten och hur du lägger till autentisering och auktorisering i dina webb program och webb-API: er finns i [exempel program](sample-v1-code.md).
