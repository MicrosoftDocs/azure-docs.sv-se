---
title: Microsoft Identity Platform ID-token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du använder id_tokens som har spridits av Azure AD v 1.0 och Microsoft Identity Platform (v 2.0) slut punkter.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 21e0b800e06b7a5ad0351ff53d26a5e2cd2ba71c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175396"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity Platform ID-token

`id_tokens` skickas till klient programmet som en del av ett [OpenID Connect](v2-protocols-oidc.md) -flöde (OIDC). De kan skickas tillsammans med eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-id_token"></a>Använda id_token

ID-token ska användas för att verifiera att en användare är den som han eller hon ansöker för att få ytterligare värdefull information om dem – den bör inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). Anspråken som det tillhandahåller kan användas för UX i ditt program, som [nycklar i en databas](#using-claims-to-reliably-identify-a-user-subject-and-object-id)och för att ge åtkomst till klient programmet.  

## <a name="claims-in-an-id_token"></a>Anspråk i en id_token

`id_tokens` är [JWTs](https://tools.ietf.org/html/rfc7519) (JSON Web tokens), vilket innebär att de består av en rubrik, en nytto last och en signatur. Du kan använda rubriken och signaturen för att verifiera tokens äkthet, medan nytto lasten innehåller information om användaren som begärdes av din klient. Om inget annat anges visas alla JWT-anspråk som anges här i både v 1.0-och v 2.0-token.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Visa den här v 1.0-exempel-token i [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Visa denna v 2.0-exempel-token i [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Huvud anspråk

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`typ` | Sträng-Always-JWT | Anger att token är en JWT-token.|
|`alg` | Sträng | Anger algoritmen som användes för att signera token. Exempel: "RS256" |
|`kid` | Sträng | Tumavtryck för den offentliga nyckel som används för att verifiera denna token. Genereras i både v 1.0 och v 2.0 `id_tokens` . |
|`x5t` | Sträng | Samma (används och värdet) som `kid` . Detta är dock ett äldre anspråk som endast har genererats i v 1.0 `id_tokens` för kompatibilitet. |

### <a name="payload-claims"></a>Nytto Last anspråk

I den här listan visas de JWT-anspråk som är i de flesta id_tokens som standard (utom där antecknas).  Din app kan dock använda [valfria anspråk](active-directory-optional-claims.md) för att begära ytterligare JWT-anspråk i id_token.  Detta kan vara ett intervall från `groups` anspråk till information om användarens namn.

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, en app-ID-URI | Identifierar den avsedda mottagaren för token. I `id_tokens` är mål gruppen appens program-ID som har tilldelats din app i Azure Portal. Din app bör validera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS-URI | Identifierar säkerhetstokentjänst som konstruerar och returnerar token och Azure AD-klienten där användaren autentiserades. Om token har utfärdats av v 2.0-slutpunkten avslutas URI: n `/v2.0` .  GUID som anger att användaren är en konsument användare från en Microsoft-konto `9188040d-6c67-4c5b-b112-36a304b66dad` . Din app ska använda en GUID-del av anspråket för att begränsa den uppsättning innehavare som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | "Utfärdat vid" anger när autentiseringen för denna token ägde rum.  |
|`idp`|Sträng, vanligt vis en STS-URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Värdet är identiskt med värdet för Issuer-anspråket om inte användar kontot inte finns i samma klient organisation som utfärdaren-gäster, till exempel. Om anspråket inte finns, innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisations kontext (till exempel ett personligt konto som bjudits in till en Azure AD-klient) `idp` kan anspråk vara "Live.com" eller en STS-URI som innehåller Microsoft-konto klient `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, en UNIX-tidsstämpel | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning.|
|`exp` |  int, en UNIX-tidsstämpel | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning.  Det är viktigt att Observera att en resurs kanske avvisar token före den här tiden, till exempel om en ändring i autentisering krävs eller om en åter kallelse av token har identifierats. |
| `c_hash`| Sträng |Kod-hash ingår endast i ID-token när ID-token utfärdas med en OAuth 2,0-auktoriseringskod. Den kan användas för att verifiera äktheten för en auktoriseringskod. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Sträng |Hashen för åtkomsttoken ingår bara i ID-tokens när ID-token utfärdas från `/authorize` slut punkten med en OAuth 2,0-åtkomsttoken. Den kan användas för att verifiera äktheten på en åtkomsttoken. Mer information om hur du utför den här verifieringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Detta returneras inte för ID-token från `/token` slut punkten. |
|`aio` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure AD för att registrera data för åter användning av token. Ignoreras.|
|`preferred_username` | Sträng | Det primära användar namnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett generiskt användar namn utan angivet format. Värdet är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får inte det här värdet användas för att fatta auktoriseringsbeslut. `profile`Omfånget krävs för att ta emot det här anspråket.|
|`email` | Sträng | `email`Anspråket finns som standard för gäst konton som har en e-postadress.  Din app kan begära e-postanspråk för hanterade användare (från samma klient som resursen) med hjälp av det `email` [valfria anspråket](active-directory-optional-claims.md).  På v 2.0-slutpunkten kan din app även begära `email` OpenID Connect-omfånget – du behöver inte begära både det valfria anspråket och omfattningen för att hämta anspråket.  E-postanspråket stöder bara adresser bara e-post från användarens profil information. |
|`name` | Sträng | `name`Anspråket ger ett läsligt värde som identifierar ämnet för token. Värdet är inte garanterat unikt, det är föränderligt och har utformats för att endast användas i visnings syfte. `profile`Omfånget krävs för att ta emot det här anspråket. |
|`nonce`| Sträng | Nonce matchar den parameter som ingår i den ursprungliga/Authorize-begäran till IDP. Om den inte matchar bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Det oföränderliga ID: t för ett objekt i Microsoft Identity system, i det här fallet ett användar konto. Det här ID: t identifierar unikt användaren för alla program – två olika program som loggar in på samma användare får samma värde i `oid` anspråket. Microsoft Graph returnerar detta ID som `id` egenskap för ett angivet användar konto. Eftersom `oid` tillåter flera appar att korrelera användare måste `profile` omfånget ta emot detta anspråk. Observera att om en enskild användare finns i flera klienter, kommer användaren att innehålla ett annat objekt-ID i varje klient – de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. `oid`Anspråket är ett GUID och kan inte återanvändas. |
|`roles`| Strängmatris | Den uppsättning roller som har tilldelats användaren som loggar in. |
|`rh` | Ogenomskinlig sträng |Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`sub` | Sträng | Den huvudprincip som token förutsätter information för, t. ex. användaren av en app. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Ämnet är en identifierad identifierare – den är unik för ett visst program-ID. Om en enskild användare loggar in i två olika appar med två olika klient-ID: n, får dessa appar två olika värden för ämnes anspråket. Detta kan vara så eller kanske inte önskas beroende på dina krav på arkitektur och sekretess. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar den Azure AD-klient som användaren är från. För arbets-och skol konton är GUID det oåterkalleliga klient-ID: t för den organisation som användaren tillhör. För personliga konton är värdet `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Omfånget krävs för att ta emot det här anspråket. |
|`unique_name` | Sträng | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är unikt vid varje viss tidpunkt, men när e-postmeddelanden och andra identifierare kan återanvändas kan det här värdet visas igen på andra konton och bör därför endast användas för visning. Endast utfärdat i v 1.0 `id_tokens` . |
|`uti` | Ogenomskinlig sträng | Ett internt anspråk som används av Azure för att omverifiera token. Ignoreras. |
|`ver` | Sträng, antingen 1,0 eller 2,0 | Anger versionen för id_token. |
|`hasgroups`|Boolesk|Om det här alternativet är sant är det alltid sant, vilket innebär att användaren finns i minst en grupp. Används i stället för Groups-anspråket för JWTs i implicita beviljande flöden om det fullständiga grupp kravet skulle utöka URI-fragmentet över längd gränserna för URL: er (för närvarande 6 eller fler grupper). Anger att klienten ska använda Microsoft Graph API för att fastställa användarens grupper ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|JSON-objekt | För Tokenbegäran som inte är begränsade (se `hasgroups` ovan) men fortfarande är för stora för token kommer en länk till listan över fullständiga grupper för användaren att inkluderas. För JWTs som ett distribuerat anspråk för SAML som ett nytt anspråk i stället för `groups` anspråket. <br><br>**Exempel-JWT-värde**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Mer information finns i [överutnyttjade anspråk](#groups-overage-claim).|

> [!NOTE]
> V 1.0-och v 2.0-id_token har skillnader i mängden information som de kommer att ha som visas i exemplen ovan. Versionen baseras på slut punkten varifrån den begärdes. Även om befintliga program ofta använder Azure AD-slutpunkten bör nya program använda "Microsoft Identity Platform".
>
> - v 1.0: Azure AD-slut punkter: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: slut punkter för Microsoft Identity Platform: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Använda anspråk för att identifiera en användare på ett tillförlitligt sätt (ämne och objekt-ID)

När du identifierar en användare (t. ex. genom att titta på dem i en databas eller bestämma vilka behörigheter de har) är det viktigt att du använder information som är konstant och unik över tid. Äldre program använder ibland fält som e-postadress, telefonnummer eller UPN.  Alla dessa kan ändras över tid och kan också återanvändas över tid – när en anställd ändrar sitt namn eller om en medarbetare får en e-postadress som matchar den tidigare, inte längre är anställd). Det är därför **viktigt** att ditt program inte använder data som kan läsas av människor för att identifiera en användare som är mänskligt läsbar, vilket innebär att någon kommer att läsa det och vill ändra det. Använd i stället de anspråk som tillhandahålls av OIDC-standarden eller de tillägg anspråk som tillhandahålls av Microsoft-- `sub` och- `oid` anspråk.

För att korrekt lagra information per användare, använder `sub` eller `oid` ensamma (som GUID: er är unika), med `tid` används för routning eller horisontell partitionering vid behov.  Om du behöver dela data mellan tjänster `oid` + `tid` är det bäst att alla appar får samma `oid` och `tid` anspråk för en specifik användare.  `sub`Anspråket i Microsoft Identity Platform är "par-klokt" – det är unikt baserat på en kombination av token mottagare, klient organisation och användare.  Därför får två appar som begär ID-token för en specifik användare olika `sub` anspråk, men samma `oid` anspråk för den användaren.

>[!NOTE]
> Använd inte `idp` anspråket för att lagra information om en användare i ett försök att korrelera användare mellan klienter.  Det kommer inte att fungera, eftersom `oid` `sub` anspråk för en användare ändras mellan klienter, efter design, för att säkerställa att program inte kan spåra användare mellan klienter.  
>
> Gäst scenarier, där en användare är i bostaden och autentiseras i en annan, ska behandla användaren som om de är en helt ny användare i tjänsten.  Dina dokument och behörigheter i Contoso-klienten bör inte gälla i Fabrikam-klienten. Detta är viktigt för att förhindra oavsiktlig data läckage mellan klienter.

### <a name="groups-overage-claim"></a>Gruppera överliggande anspråk
För att säkerställa att token-storleken inte överskrider storleks gränserna för HTTP-huvudet begränsar Azure AD antalet objekt-ID: n som ingår i `groups` anspråket. Om en användare är medlem i fler grupper än överkvoten (150 för SAML-tokens, 200 för JWT-token), genererar inte Azure AD grupp anspråket i token. I stället innehåller den ett överanvändning-anspråk i token som indikerar att programmet ska fråga Microsoft Graph-API: et för att hämta användarens grupp medlemskap.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="validating-an-id_token"></a>Verifiera en id_token

Validering `id_token` av en liknar det första steget i att [Verifiera en åtkomsttoken](access-tokens.md#validating-tokens) – din klient kan verifiera att rätt utfärdare har skickat tillbaka token och att den inte har manipulerats. Eftersom `id_tokens` är alltid en JWT-token finns det många bibliotek för att validera dessa tokens – vi rekommenderar att du använder någon av dessa i stället för att göra det själv.  Observera att endast konfidentiella klienter (de med en hemlighet) ska validera ID-token.  Offentliga program (kod som körs helt på en enhet eller nätverk som du inte kontrollerar – till exempel en användares webbläsare eller sitt hem nätverk) drar inte nytta av att validera ID-token, eftersom en obehörig användare kan avlyssna och redigera nycklar som används för verifiering av token.

Information om hur du validerar token manuellt finns i avsnittet om hur du [verifierar en](access-tokens.md#validating-tokens)åtkomsttoken. När signaturen för token har verifierats ska följande JWT-anspråk val IDE ras i id_token (de kan också utföras av ditt verifierings bibliotek för token):

* Tidsstämplar: `iat` -, `nbf` -och- `exp` tidsstämplar bör alla falla före eller efter den aktuella tiden, efter behov.
* Mål grupp: `aud` anspråket ska matcha app-ID för ditt program.
* Nonce: `nonce` anspråk i nytto lasten måste matcha nonce-parametern som överförts till/Authorize-slutpunkten under den första begäran.

## <a name="next-steps"></a>Nästa steg

* Läs mer [om åtkomsttoken](access-tokens.md)
* Anpassa JWT-anspråk i id_token med [valfria anspråk](active-directory-optional-claims.md).
