---
title: Auktorisera webb program åtkomst med OpenID Connect & Azure AD | Microsoft Docs
description: 'Den här artikeln beskriver hur du använder HTTP-meddelanden för att ge åtkomst till webb program och webb-API: er i din klient med hjälp av Azure Active Directory och OpenID Connect.'
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85551556"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Ge åtkomst till webbprogram med hjälp av OpenID Connect och Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) är ett enkelt identitets lager som byggts ovanpå OAuth 2,0-protokollet. OAuth 2,0 definierar mekanismer [**för att hämta och använda åtkomsttoken**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att komma åt skyddade resurser, men de definierar inte standard metoder för att tillhandahålla identitets information. OpenID Connect implementerar autentisering som ett tillägg till auktoriseringen av OAuth 2,0. Den innehåller information om slutanvändaren i form av en [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) som verifierar användarens identitet och ger grundläggande profil information om användaren.

OpenID Connect är vår rekommendation om du skapar ett webb program som finns på en server och som nås via en webbläsare.

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

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflöde med OpenID Connect

Det mest grundläggande inloggnings flödet innehåller följande steg – var och en av dem beskrivs i detalj nedan.

![OpenId Connect Authentication Flow](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument för OpenID Connect-metadata

OpenID Connect beskriver ett metadataobjekt som innehåller merparten av den information som krävs för att en app ska kunna genomföra inloggningen. Detta omfattar information som webb adresserna som ska användas och platsen för tjänstens offentliga signerings nycklar. Du hittar dokumentet för OpenID Connect-metadata på:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata är ett enkelt JavaScript Object Notation (JSON)-dokument. Se följande kodfragment för ett exempel. Kodfragmentets innehåll beskrivs fullständigt i [OpenID Connect-specifikationen](https://openid.net). Observera att om du anger ett klient-ID snarare än `common` i stället för {Tenant} ovan kommer det att resultera i klient-/regionsspecifika URI: er i det returnerade JSON-objektet.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Om din app har anpassade signerings nycklar som ett resultat av funktionen för [anspråks mappning](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) måste du lägga till en `appid` frågeparameter som innehåller app-ID: t för att få en `jwks_uri` pekare till appens information om signerings nyckeln. Exempel: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` innehåller en `jwks_uri` av `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

När ditt webb program behöver autentisera användaren måste användaren dirigera användaren till `/authorize` slut punkten. Den här begäran liknar det första benet i [OAuth 2,0 Authorization Code Flow](v1-protocols-oauth-code.md), med några viktiga skillnader:

* Begäran måste innehålla omfånget `openid` i `scope` parametern.
* `response_type`Parametern måste innehålla `id_token` .
* Begäran måste innehålla `nonce` parametern.

Därför skulle en exempel förfrågan se ut så här:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| tenant |krävs |`{tenant}`Värdet i sökvägen till begäran kan användas för att styra vem som kan logga in på programmet. De tillåtna värdena är klient identifierare, till exempel `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` för klient oberoende token |
| client_id |krävs |Det program-ID som tilldelats din app när du registrerade den med Azure AD. Du hittar det här i Azure Portal. Klicka på **Azure Active Directory**, klicka på **app-registreringar**, Välj programmet och leta upp program-ID: t på program sidan. |
| response_type |krävs |Måste inkludera `id_token` för OpenID Connect-inloggning. Det kan också innehålla andra response_types, till exempel `code` eller `token` . |
| omfång | rekommenderas | OpenID Connect-specifikationen kräver omfånget `openid` , som översätts till behörigheten "logga in dig" i medgivande gränssnittet. Den här och andra OIDC-omfattningarna ignoreras vid slut punkten för v 1.0, men är fortfarande en bra metod för standardkompatibla klienter. |
| Nnär |krävs |Ett värde som ingår i begäran, som genereras av appen, som ingår i det resulterande `id_token` kravet. Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng eller GUID som kan användas för att identifiera ursprunget för begäran. |
| redirect_uri | rekommenderas |Appens redirect_uri, där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. Om det saknas skickas användar agenten tillbaka till en av de omdirigerings-URI: er som registrerats för appen, slumpmässigt. Den maximala längden är 255 byte |
| response_mode |valfri |Anger den metod som ska användas för att skicka den resulterande authorization_code tillbaka till din app. De värden som stöds är `form_post` för *http-formulär post* och `fragment` URL- *fragment*. För webb program rekommenderar vi att du använder `response_mode=form_post` för att säkerställa den säkraste överföringen av tokens till ditt program. Standardvärdet för alla flöden, inklusive en id_token `fragment` .|
| state |rekommenderas |Ett värde som ingår i begäran som returneras i token-svaret. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| visas |valfri |Anger vilken typ av användar interaktion som krävs. För närvarande är de enda giltiga värdena inloggning ", none och medgivande. `prompt=login` tvingar användaren att ange sina autentiseringsuppgifter för den begäran och negera enkel inloggning. `prompt=none` är motsatt – det garanterar att användaren inte visas med interaktiva prompter. Om begäran inte kan slutföras i bakgrunden via enkel inloggning, returnerar slut punkten ett fel. `prompt=consent` utlöser dialog rutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörighet till appen. |
| login_hint |valfri |Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar använder ofta den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med hjälp av `preferred_username` anspråket. |

I det här läget uppmanas användaren att ange sina autentiseringsuppgifter och slutföra autentiseringen.

### <a name="sample-response"></a>Exempelsvar

Ett exempel svar som skickas till den som `redirect_uri` anges i inloggnings förfrågan när användaren har autentiserats kan se ut så här:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |`id_token`Appen som begärdes. Du kan använda `id_token` för att verifiera användarens identitet och påbörja en session med användaren. |
| state |Ett värde som ingår i begäran som också returneras i svaret från token. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till så att `redirect_uri` appen kan hantera dem på rätt sätt:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

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

## <a name="validate-the-id_token"></a>Verifiera id_token

Det `id_token` räcker inte att ta emot en användare för att autentisera användaren. du måste verifiera signaturen och verifiera anspråken i `id_token` kraven för din app. Azure AD-slutpunkten använder JSON-webbtoken (JWTs) och kryptering med offentliga nycklar för att signera tokens och kontrol lera att de är giltiga.

Du kan välja att validera `id_token` i-klient koden, men en vanlig metod är att skicka `id_token` till en backend-server och utföra verifieringen där. 

Du kanske också vill verifiera ytterligare anspråk beroende på ditt scenario. Några vanliga valideringar är:

* Se till att användaren/organisationen har registrerat sig för appen.
* Se till att användaren har rätt behörighet/privilegier med hjälp av `wids` eller- `roles` anspråk. 
* Att se till att en viss styrka autentisering har skett, t. ex. Multi-Factor Authentication.

När du har verifierat `id_token` kan du starta en-session med användaren och använda anspråken i `id_token` för att få information om användaren i din app. Den här informationen kan användas för visning, poster, anpassning osv. `id_tokens` Läs [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)om du vill ha mer information om och anspråk.

## <a name="send-a-sign-out-request"></a>Skicka en inloggningsbegäran

När du vill signera användaren från appen räcker det inte att ta bort appens cookies eller på annat sätt avsluta sessionen med användaren. Du måste också omdirigera användaren till `end_session_endpoint` för utloggning. Om du inte gör det kommer användaren att kunna autentisera till din app igen utan att ange sina autentiseringsuppgifter igen, eftersom de kommer att ha en giltig enkel inloggnings session med Azure AD-slutpunkten.

Du kan helt enkelt omdirigera användaren till listan `end_session_endpoint` i OpenID Connect-Metadatadokumentet:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter | Typ | Beskrivning |
| --- | --- | --- |
| post_logout_redirect_uri |rekommenderas |URL: en som användaren ska omdirigeras till efter en lyckad utloggning.  URL: en måste matcha en av de omdirigerings-URI: er som registrerats för ditt program i registrerings portalen för appen.  Om *post_logout_redirect_uri* inte ingår visas ett allmänt meddelande för användaren. |

## <a name="single-sign-out"></a>Enkel utloggning

När du omdirigerar användaren till `end_session_endpoint` , rensar Azure AD användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad i andra program som använder Azure AD för autentisering. Om du vill att dessa program ska kunna signera ut användaren samtidigt skickar Azure AD en HTTP GET-begäran till registrerade `LogoutUrl` för alla program som användaren för närvarande är inloggad på. Program måste svara på den här begäran genom att rensa alla sessioner som identifierar användaren och returnera ett `200` svar. Om du vill stödja enkel inloggning i ditt program måste du implementera en sådan `LogoutUrl` i din program kod. Du kan ställa in `LogoutUrl` från Azure Portal:

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj din Active Directory genom att klicka på ditt konto i det övre högra hörnet på sidan.
3. I den vänstra navigerings panelen väljer du **Azure Active Directory** och väljer sedan **Appregistreringar** och väljer ditt program.
4. Klicka på **Inställningar**, sedan på **Egenskaper** och leta upp text rutan **utloggnings-URL** . 

## <a name="token-acquisition"></a>Hämtning av token
Många webbappar behöver inte bara signera användaren i, utan även få åtkomst till en webb tjänst åt den användaren med hjälp av OAuth. I det här scenariot kombineras OpenID-anslutningar för användarautentisering samtidigt `authorization_code` som du kan använda för att komma åt `access_tokens` det med [OAuth Authorization Code Flow](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Hämta åtkomsttoken
Om du vill hämta åtkomsttoken måste du ändra inloggnings förfrågan från ovan:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Genom att inkludera behörighets omfattningar i begäran och använda `response_type=code+id_token` , `authorize` ser slut punkten till att användaren har godkänt de behörigheter som anges i `scope` Frågeparametern och returnerar appen en auktoriseringskod till Exchange för en åtkomsttoken.

### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar, som skickats till `redirect_uri` med `response_mode=form_post` , ser ut så här:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| id_token |`id_token`Appen som begärdes. Du kan använda `id_token` för att verifiera användarens identitet och påbörja en session med användaren. |
| kod |Den authorization_code som appen begärde. Appen kan använda auktoriseringskod för att begära en åtkomsttoken för mål resursen. Authorization_codes är korta livs längd och upphör normalt att gälla efter 10 minuter. |
| state |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till så att `redirect_uri` appen kan hantera dem på rätt sätt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| fel |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| error_description |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

En beskrivning av möjliga felkoder och deras rekommenderade klient åtgärder finns i [felkoder för slut punkts fel](#error-codes-for-authorization-endpoint-errors).

När du har fått en auktorisering `code` och en kan `id_token` du logga in användaren i och hämta [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för deras räkning. För att kunna signera användaren i måste du verifiera `id_token` exakt enligt beskrivningen ovan. För att få åtkomst till tokens, kan du följa stegen som beskrivs i avsnittet "Använd auktoriseringskod för att begära en åtkomsttoken" i vår dokumentation om [OAuth-kod flödet](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [åtkomsttoken](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Läs mer om- [ `id_token` och-anspråk](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
