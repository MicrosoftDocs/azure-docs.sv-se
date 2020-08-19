---
title: Kod flöde för OAuth 2,0-enhet | Azure
titleSuffix: Microsoft identity platform
description: Logga in användare utan webbläsare. Bygg inbäddade och webbläsar lösa autentiserings flöden med hjälp av enhets godkännande.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272458"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity Platform och OAuth 2,0-enhetens Authorization-flöde

Microsoft Identity Platform stöder auktorisering av [enhets behörighet](https://tools.ietf.org/html/rfc8628), vilket gör att användarna kan logga in på inmatade enheter, till exempel en smart TV, IoT-enhet eller skrivare.  Om du vill aktivera det här flödet har enheten användaren besöka en webb sida i webbläsaren på en annan enhet för att logga in.  När användaren loggar in kan enheten hämta åtkomsttoken och uppdatera tokens efter behov.

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Protokoll diagram

Hela enhets kod flödet ser ut ungefär som nästa diagram. Vi beskriver vart och ett av stegen längre fram i den här artikeln.

![Enhets kod flöde](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Begäran om enhets godkännande

Klienten måste först kontrol lera med autentiseringsservern för en enhet och användar kod som används för att initiera autentiseringen. Klienten samlar in den här begäran från `/devicecode` slut punkten. I den här begäran ska klienten även innehålla de behörigheter som krävs för att hämta från användaren. Från den tidpunkt då den här begäran skickas har användaren bara 15 minuter på sig att logga in (det vanliga värdet för `expires_in` ), så gör bara den här begäran när användaren har angett att de är redo att logga in.

> [!TIP]
> Försök att köra denna begäran i Postman!
> [![Försök att köra denna begäran i Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Villkor | Beskrivning |
| --- | --- | --- |
| `tenant` | Krävs | Kan vara/vanliga,/consumers eller/organizations.  Det kan också vara den katalog klient som du vill begära behörighet från i GUID eller eget namn format.  |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `scope` | Obligatorisk | En blankstegsavgränsad lista med [omfattningar](v2-permissions-and-consent.md) som du vill att användaren ska godkänna.  |

### <a name="device-authorization-response"></a>Svar på enhets auktorisering

Ett lyckat svar är ett JSON-objekt som innehåller den information som krävs för att tillåta användaren att logga in.

| Parameter | Format | Beskrivning |
| ---              | --- | --- |
|`device_code`     | Sträng | En lång sträng som används för att verifiera sessionen mellan klienten och auktoriseringsservern. Klienten använder den här parametern för att begära åtkomsttoken från auktoriseringsservern. |
|`user_code`       | Sträng | En kort sträng som visas för den användare som används för att identifiera sessionen på en sekundär enhet.|
|`verification_uri`| URI | Den URI som användaren ska gå till med `user_code` i för att logga in. |
|`expires_in`      | int | Antalet sekunder innan `device_code` och `user_code` upphör att gälla. |
|`interval`        | int | Antalet sekunder som klienten ska vänta mellan avsöknings begär Anden. |
| `message`        | Sträng | En läslig sträng med instruktioner för användaren. Detta kan lokaliseras genom att inkludera en **frågeparameter** i formulärets begäran `?mkt=xx-XX` och fylla i lämplig språk kultur kod. |

> [!NOTE]
> `verification_uri_complete`Svars fältet ingår inte eller stöds inte för tillfället.  Vi nämner detta eftersom om du läser den [standard](https://tools.ietf.org/html/rfc8628) som visas som `verification_uri_complete` en valfri del av enhets kod flödet standard.

## <a name="authenticating-the-user"></a>Autentisera användaren

När klienten har tagit emot, `user_code` `verification_uri` visar klienten dessa för användaren och instruerar dem att logga in med sin mobil telefon eller PC-webbläsare.

Om användaren autentiseras med ett personligt konto (på/vanliga eller/consumers) kommer de att uppmanas att logga in igen för att kunna överföra autentiserings tillstånd till enheten.  De kommer också att uppmanas att ge sitt medgivande, för att säkerställa att de är medvetna om de behörigheter som beviljas.  Detta gäller inte för arbets-eller skol konton som används för att autentisera.

När användaren autentiseras på `verification_uri` , ska klienten avsöka `/token` slut punkten för den begärda token med hjälp av `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Krävs | Beskrivning|
| -------- | -------- | ---------- |
| `tenant`  | Krävs | Samma klient organisation eller klient Ali Aset som används i den första begäran. |
| `grant_type` | Obligatorisk | Måste vara `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obligatorisk | Måste matcha det `client_id` som används i den första begäran. |
| `device_code`| Obligatorisk | `device_code`Returnerade i begäran om enhets godkännande.  |

### <a name="expected-errors"></a>Förväntade fel

Enhets kod flödet är ett avsöknings protokoll så att klienten måste vänta på att få fel innan användaren har autentiserats.

| Fel | Beskrivning | Klient åtgärd |
| ------ | ----------- | -------------|
| `authorization_pending` | Användaren har inte autentiserat autentisering, men har inte avbrutit flödet. | Upprepa begäran efter minst `interval` sekunder. |
| `authorization_declined` | Slutanvändaren nekade begäran om auktorisering.| Stoppa avsökningen och återgå till ett oautentiserat tillstånd.  |
| `bad_verification_code`| `device_code`Skickade till `/token` slut punkten kändes inte igen. | Kontrol lera att klienten skickar rätt `device_code` i begäran. |
| `expired_token` | Minst `expires_in` sekunder har passerat och autentisering är inte längre möjligt med detta `device_code` . | Stoppa avsökningen och återgå till ett oautentiserat tillstånd. |

### <a name="successful-authentication-response"></a>Godkänt autentiserings svar

Ett lyckat svar på token kommer att se ut så här:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | Beskrivning |
| --------- | ------ | ----------- |
| `token_type` | Sträng| Alltid "Bearer. |
| `scope` | Separerade blankstegsavgränsad strängar | Om en åtkomsttoken returnerades listar det de omfattningar som åtkomsttoken är giltig för. |
| `expires_in`| int | Antal sekunder innan den inkluderade åtkomsttoken är giltig för. |
| `access_token`| Ogenomskinlig sträng | Utfärdat för de [omfattningar](v2-permissions-and-consent.md) som begärdes.  |
| `id_token`   | JWT | Utfärdas om den ursprungliga `scope` parametern omfattade `openid` omfånget.  |
| `refresh_token` | Ogenomskinlig sträng | Utfärdas om den ursprungliga `scope` parametern ingår `offline_access` .  |

Du kan använda uppdateringstoken för att hämta nya åtkomsttoken och uppdatera tokens med samma flöde som dokumenteras i dokumentationen för OAuth- [kodaren](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
