---
title: Medgivande protokoll för Microsoft Identity Platform admin
description: En beskrivning av auktorisering i Microsoft Identity Platform, inklusive omfång, behörigheter och medgivande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b6fb5f680dfa5e2c87533083e3df4c2bae1ed12a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097031"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Administrativt medgivande på Microsoft Identity Platform

Vissa behörigheter kräver medgivande från en administratör innan de kan beviljas inom en klient organisation.  Du kan också använda administratörs medgivande slut punkten för att bevilja behörighet till en hel klient.

## <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderat: signera användaren i din app

När du skapar ett program som använder den administrativa medgivande slut punkten behöver appen normalt en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan ingå i appens registrerings flöde, en del av appens inställningar, eller så kan det vara ett dedikerat "Connect"-flöde. I många fall är det meningsfullt att appen visar vyn "Anslut" bara när en användare har loggat in med ett arbets-eller skol Microsoft-konto.

När du registrerar användaren i din app kan du identifiera den organisation som administratören tillhör innan de ber dem att godkänna de nödvändiga behörigheterna. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisations användare. Följ [självstudierna för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md)för att logga in användaren i.

## <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalog administratör

När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till Microsoft Identity Platform *admin medgivande-slutpunkten*.

```none
https://login.microsoftonline.com/{tenant}/v2.0/adminconsent
        ?client_id=6731de76-14a6-49ae-97bc-6eba6914391e
        &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
        &redirect_uri=http://localhost/myapp/permissions
        &state=12345
```

| Parameter | Villkor | Beskrivning |
| :--- | :--- | :--- |
| `tenant` | Krävs | Den katalog klient som du vill begära behörighet från. Kan anges i GUID eller eget namn format eller allmänt refereras till `organizations` som visas i exemplet. Använd inte "common", eftersom personliga konton inte kan tillhandahålla administrativt medgivande, förutom i kontexten för en klient. För att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter använder du klient-ID när det är möjligt. |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `redirect_uri` | Obligatorisk |Den omdirigerings-URI där du vill att svaret på din app ska hanteras. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i registrerings portalen för appen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Använd tillstånd för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
|`scope` | Obligatorisk | Definierar den uppsättning behörigheter som begärs av programmet. Detta kan vara antingen statiskt (med `/.default` ) eller dynamiska omfång. Detta kan inkludera OIDC-omfattningarna ( `openid` , `profile` , `email` ). |

I det här läget kräver Azure AD en klient administratör för att logga in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som du har begärt i `scope` parametern.  Om du har använt ett statiskt ( `/.default` )-värde kommer det att fungera som v 1.0 admin medgivande-slutpunkten och begära medgivande för alla omfattningar som finns i de nödvändiga behörigheterna (både användare och app). För att begära program behörigheter måste du använda `/.default` värdet. Om du inte vill att administratörer ska se en specifik behörighet på skärmen för administratörs godkännande hela tiden när du använder `/.default` , är det bästa sättet att inte placera behörigheten i avsnittet nödvändiga behörigheter. I stället kan du använda dynamiskt medgivande för att lägga till de behörigheter som du vill ska visas på skärmen för medgivande vid körning, i stället för att använda `/.default` .

### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser det lyckade svaret ut så här:

```none
http://localhost/myapp/permissions
    ?admin_consent=True
    &tenant=fa00d692-e9c7-4460-a743-29f2956fd429
    &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
    &state=12345
```

| Parameter | Beskrivning |
| :--- | :--- |
| `tenant`| Den katalog klient som beviljade programmet de behörigheter som begärdes, i GUID-format.|
| `state` | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på.|
| `scope` | Den uppsättning behörigheter som har beviljats åtkomst till för programmet.|
| `admin_consent` | Kommer att anges till `True` .|

### <a name="error-response"></a>Fel svar

```none
http://localhost/myapp/permissions
        ?admin_consent=True
        &tenant=fa15d692-e9c7-4460-a743-29f2956fd429
        &error=consent_required
        &error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z
        &state=12345
```

Om du lägger till parametrarna som visas i ett lyckat svar visas fel parametrarna nedan.

| Parameter | Beskrivning |
|:-------------------|:-------------------------------------------------------------------------------------------------|
| `error` | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel.|
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken till ett fel.|
| `tenant`| Den katalog klient som beviljade programmet de behörigheter som begärdes, i GUID-format.|
| `state` | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på.|
| `admin_consent` | Anges till `True` för att indikera att det här svaret har inträffat på ett flöde för administratörs godkännande.|

## <a name="next-steps"></a>Nästa steg
- Se så [här konverterar du en app till flera innehavare](howto-convert-app-to-be-multi-tenant.md)
- Lär dig hur [medgivande stöds i OAuth 2,0-protokoll skiktet under den utfärdande flödes koden för auktoriseringskod](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Lär dig [hur ett program med flera klienter kan använda medgivande ramverket](./howto-convert-app-to-be-multi-tenant.md) för att implementera användar-och administratörs medgivande, stöd för mer avancerade program mönster på flera nivåer.
- Förstå [Azure AD Application medgivande-upplevelser](application-consent-experience.md)
