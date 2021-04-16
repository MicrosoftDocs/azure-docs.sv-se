---
title: ID-token för Microsofts identitetsplattform | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du använder id_tokens som genereras av Slutpunkter för Azure AD v1.0 och Microsoft Identity Platform (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530023"
---
# <a name="microsoft-identity-platform-id-tokens"></a>ID-token för Microsoft-identitetsplattform

`id_tokens` skickas till klientprogrammet som en del av ett [OpenID Connect flöde](v2-protocols-oidc.md) (OIDC). De kan skickas tillsammans med eller i stället för en åtkomsttoken och används av klienten för att autentisera användaren.

## <a name="using-the-id_token"></a>Använda id_token

ID-token ska användas för att verifiera att en användare är den de utger sig för att vara och få ytterligare användbar information om dem – den bör inte användas för auktorisering i stället för en [åtkomsttoken](access-tokens.md). Anspråken som den tillhandahåller kan användas för UX i ditt program, som nycklar i en databas [och](#using-claims-to-reliably-identify-a-user-subject-and-object-id)ge åtkomst till klientprogrammet.  

## <a name="claims-in-an-id_token"></a>Anspråk i en id_token

`id_tokens`är [](https://tools.ietf.org/html/rfc7519) JWT(JSON-webbtoken), vilket innebär att de består av en rubrik, nyttolast och signaturdel. Du kan använda huvudet och signaturen för att verifiera tokens äkthet, medan nyttolasten innehåller information om användaren som begärs av klienten. Förutom där det anges visas alla JWT-anspråk som anges här i både v1.0- och v2.0-token.

### <a name="v10"></a>V1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Visa denna v1.0-exempeltoken [i jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Visa denna v2.0-exempeltoken [i jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Huvudanspråk

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`typ` | Sträng – alltid "JWT" | Anger att token är en JWT-token.|
|`alg` | Sträng | Anger algoritmen som användes för att signera token. Exempel: "RS256" |
|`kid` | Sträng | Tumavtryck för den offentliga nyckel som används för att verifiera denna token. Genereras i både v1.0 och v2.0 `id_tokens` . |
|`x5t` | Sträng | Samma (används och värde) som `kid` . Detta är dock ett äldre anspråk som endast genereras i v1.0 `id_tokens` för kompatibilitetsändamål. |

### <a name="payload-claims"></a>Nyttolastanspråk

Den här listan visar JWT-anspråk som finns i de id_tokens som standard (förutom där det anges).  Din app kan dock använda [valfria anspråk för](active-directory-optional-claims.md) att begära ytterligare JWT-anspråk i id_token.  Dessa kan vara allt `groups` från anspråk till information om användarens namn.

|Begär | Format | Beskrivning |
|-----|--------|-------------|
|`aud` |  Sträng, en app-ID-URI | Identifierar den avsedda mottagaren av token. I `id_tokens` är målgruppen appens program-ID, som tilldelats appen i Azure Portal. Din app bör verifiera det här värdet och avvisa token om värdet inte matchar. |
|`iss` |  Sträng, en STS-URI | Identifierar den säkerhetstokentjänst (STS) som konstruerar och returnerar token samt den Azure AD-klientorganisation där användaren autentiserades. Om token utfärdades av v2.0-slutpunkten slutar URI:en i `/v2.0` .  GUID som anger att användaren är en konsumentanvändare från en Microsoft-konto är `9188040d-6c67-4c5b-b112-36a304b66dad` . Din app bör använda GUID-delen av anspråket för att begränsa den uppsättning klienter som kan logga in på appen, om tillämpligt. |
|`iat` |  int, en UNIX-tidsstämpel | "Utfärdat på" anger när autentiseringen för denna token inträffade.  |
|`idp`|Sträng, vanligtvis en STS-URI | Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med värdet för Issuer-anspråket såvida inte användarkontot inte finns i samma klientorganisation som utfärdaren , till exempel gäster. Om anspråket inte finns innebär det att värdet för `iss` kan användas i stället.  För personliga konton som används i en organisationskontext (till exempel ett personligt konto som bjudits in till en Azure AD-klientorganisation) kan anspråket vara "live.com" eller en `idp` STS-URI som innehåller Microsoft-konto-klientorganisationen `9188040d-6c67-4c5b-b112-36a304b66dad` . |
|`nbf` |  int, en UNIX-tidsstämpel | "nbf"-anspråket (inte före) identifierar den tid innan JWT INTE FÅR godkännas för bearbetning.|
|`exp` |  int, en UNIX-tidsstämpel | Anspråket "exp" (förfallotid) identifierar den förfallotid på eller efter vilken JWT INTE FÅR godkännas för bearbetning.  Det är viktigt att observera att en resurs kan avvisa token före den här tiden också – om till exempel en ändring av autentisering krävs eller om en tokenåterkallning har identifierats. |
| `c_hash`| Sträng |Kodhashen ingår endast i ID-token när ID-token utfärdas med en OAuth 2.0-auktoriseringskod. Den kan användas för att verifiera auktoriseringskodens äkthet. Mer information om hur du utför den här verifieringen finns [OpenID Connect specifikationen](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Sträng |Hash-värdet för åtkomsttoken ingår endast i ID-token när ID-token utfärdas från slutpunkten med en `/authorize` OAuth 2.0-åtkomsttoken. Den kan användas för att verifiera autentiseringen av en åtkomsttoken. Mer information om hur du utför den här verifieringen finns [OpenID Connect specifikationen](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Detta returneras inte för ID-token från `/token` slutpunkten. |
|`aio` | Täckande sträng | Ett internt anspråk som används av Azure AD för att registrera data för tokenåter återanvändning. Bör ignoreras.|
|`preferred_username` | Sträng | Det primära användarnamnet som representerar användaren. Det kan vara en e-postadress, ett telefonnummer eller ett allmänt användarnamn utan något angivet format. Värdet är föränderligt och kan ändras med tiden. Eftersom det är föränderligt får det här värdet inte användas för att fatta auktoriseringsbeslut. `profile`Omfånget krävs för att ta emot det här anspråket.|
|`email` | Sträng | Anspråket `email` finns som standard för gästkonton som har en e-postadress.  Din app kan begära e-postanspråk för hanterade användare (de från samma klientorganisation som resursen) med hjälp av det `email` [valfria anspråket](active-directory-optional-claims.md).  På v2.0-slutpunkten kan din app också begära OpenID Connect-omfånget – du behöver inte begära både det valfria anspråket och omfånget för att `email` hämta anspråket.  E-postanspråk stöder endast adresserbar e-post från användarens profilinformation. |
|`name` | Sträng | Anspråket `name` ger ett läsbart värde som identifierar ämnet för token. Värdet är inte garanterat unikt, det är föränderligt och är utformat för att endast användas i visningssyfte. `profile`Omfånget krävs för att ta emot det här anspråket. |
|`nonce`| Sträng | Nonce matchar parametern som ingår i den ursprungliga /authorize-begäran till IDP. Om den inte matchar bör ditt program avvisa token. |
|`oid` | Sträng, ett GUID | Den oföränderliga identifieraren för ett objekt i Microsofts identitetssystem, i det här fallet ett användarkonto. Det här ID:t identifierar användaren unikt i flera program – två olika program som loggar in samma användare får samma värde i `oid` anspråket. Den Microsoft Graph returnerar detta ID som `id` egenskap för ett visst användarkonto. Eftersom tillåter `oid` att flera appar korrelerar användare krävs `profile` omfånget för att ta emot det här anspråket. Observera att om en enskild användare finns i flera klientorganisationsklienter innehåller användaren ett annat objekt-ID i varje klientorganisation – de betraktas som olika konton, även om användaren loggar in på varje konto med samma autentiseringsuppgifter. Anspråket `oid` är ett GUID och kan inte återanvändas. |
|`roles`| Strängmatris | Den uppsättning roller som har tilldelats till den användare som loggar in. |
|`rh` | Täckande sträng |Ett internt anspråk som används av Azure för att förnya token. Bör ignoreras. |
|`sub` | Sträng | Det huvudnamn som token anger information om, till exempel användaren av en app. Det här värdet kan inte ändras och kan inte tilldelas eller återanvändas igen. Ämnet är en parvis identifierare – det är unikt för ett visst program-ID. Om en enskild användare loggar in i två olika appar med två olika klient-ID får dessa appar två olika värden för ämnesanspråk. Detta kanske inte är önskat beroende på din arkitektur och sekretesskrav. |
|`tid` | Sträng, ett GUID | Ett GUID som representerar den Azure AD-klientorganisation som användaren kommer från. För arbets- och skolkonton är GUID det oföränderliga klientorganisations-ID:t för den organisation som användaren tillhör. För personliga konton är värdet `9188040d-6c67-4c5b-b112-36a304b66dad` . `profile`Omfånget krävs för att ta emot det här anspråket. |
|`unique_name` | Sträng | Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är unikt vid en given tidpunkt, men eftersom e-postmeddelanden och andra identifierare kan återanvändas kan det här värdet visas igen på andra konton och bör därför endast användas i visningssyfte. Utfärdas endast i v1.0 `id_tokens` . |
|`uti` | Täckande sträng | Ett internt anspråk som används av Azure för att förnya token. Bör ignoreras. |
|`ver` | Sträng, antingen 1.0 eller 2.0 | Anger versionen av id_token. |
|`hasgroups`|Boolesk|Om det finns är alltid sant, vilket anger att användaren finns i minst en grupp. Används i stället för gruppanspråk för JWT i implicita beviljandeflöden om det fullständiga gruppanspråket skulle utöka URI-fragmentet utöver URL-längdgränserna (för närvarande 6 eller fler grupper). Anger att klienten ska använda Microsoft Graph API för att fastställa användarens grupper ( `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ).|
|`groups:src1`|JSON-objekt | För tokenbegäranden som inte är längdbegränsade (se ovan), men som fortfarande är för stora för token, inkluderas en länk till den fullständiga `hasgroups` grupplistan för användaren. För JWT som ett distribuerat anspråk för SAML som ett nytt anspråk i stället för `groups` anspråket. <br><br>**Exempel på JWT-värde:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Mer information finns i Grupper [överage-anspråk](#groups-overage-claim).|

> [!NOTE]
> V1.0 och v2.0 id_token skillnader i mängden information som de kommer att överföra enligt exemplen ovan. Versionen baseras på den slutpunkt som den begärdes från. Befintliga program använder sannolikt Azure AD-slutpunkten, men nya program bör använda "Microsoft Identity Platform".
>
> - v1.0: Azure AD-slutpunkter: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0: Slutpunkter för Microsoft Identity Platform: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Använda anspråk för att på ett tillförlitligt sätt identifiera en användare (ämne och objekt-ID)

När du identifierar en användare (till exempel genom att leta upp dem i en databas eller bestämmer vilka behörigheter de har) är det viktigt att använda information som förblir konstant och unik över tid. Äldre program använder ibland fält som e-postadress, telefonnummer eller UPN.  Alla dessa kan ändras med tiden och kan även återanvändas med tiden – när en anställd byter namn eller en medarbetare får en e-postadress som matchar en tidigare medarbetares, inte längre närvarande medarbetares. Därför är det **viktigt** att ditt program inte använder läsbara data till att identifiera en användare – läsbar för människor innebär i allmänhet att någon läser den och vill ändra den. Använd i stället de anspråk som tillhandahålls av OIDC-standarden eller tilläggsanspråken från Microsoft – `sub` `oid` anspråken och .

För att lagra information per användare, använd eller `sub` ensamt (som GUID:er är unika) med som används för routning eller `oid` `tid` horisontell partitionering om det behövs.  Om du behöver dela data mellan tjänster är `oid` + `tid` det bäst eftersom alla appar får samma `oid` och anspråk för en viss `tid` användare.  Anspråket på Microsoft Identity Platform är "parvis" – det är unikt baserat på en kombination av `sub` tokenmottagare, klientorganisation och användare.  Därför får två appar som begär ID-token för en viss användare olika `sub` anspråk, men samma `oid` anspråk för den användaren.

>[!NOTE]
> Använd inte anspråket `idp` för att lagra information om en användare i ett försök att korrelera användare mellan klienter.  Den fungerar inte, när anspråken och för en användare ändras mellan klientorganisationen, som design för att säkerställa att program inte kan `oid` `sub` spåra användare mellan klienter.  
>
> Gästscenarier, där en användare finns i en klientorganisation och autentiseras i en annan, bör behandla användaren som om de vore en helt ny användare av tjänsten.  Dina dokument och behörigheter i Contoso-klientorganisationen bör inte gälla i Fabrikam-klientorganisationen. Detta är viktigt för att förhindra oavsiktligt dataläckage mellan klienter.

### <a name="groups-overage-claim"></a>Anspråk för övergrupps-anspråk
För att säkerställa att tokenstorleken inte överskrider storleksgränserna för HTTP-huvud begränsar Azure AD antalet objekt-ID:er som ingår i `groups` anspråket. Om en användare är medlem i fler grupper än överlagringsgränsen (150 för SAML-token, 200 för JWT-token) ger Azure AD inte gruppanspråk i token. I stället innehåller den ett överbefordransanspråk i token som anger att programmet ska fråga Microsoft Graph-API:et för att hämta användarens gruppmedlemskap.

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

## <a name="id-token-lifetime"></a>Livslängd för ID-token

Som standard är en ID-token giltig i 1 timme – efter 1 timme måste klienten hämta en ny ID-token.

Du kan justera livslängden för en ID-token för att kontrollera hur ofta klientprogrammet upphör att gälla programsessionen och hur ofta det kräver att användaren autentiserar igen (tyst eller interaktivt). Mer information finns i [Konfigurerbara livslängder för token.](active-directory-configurable-token-lifetimes.md)

## <a name="validating-an-id_token"></a>Verifiera en id_token

Att verifiera en liknar det första steget för att verifiera en åtkomsttoken – klienten kan verifiera att rätt utfärdare har skickat tillbaka token och att den inte har `id_token` manipulerats. [](access-tokens.md#validating-tokens) Eftersom `id_tokens` alltid är en JWT-token finns det många bibliotek för att verifiera dessa token – vi rekommenderar att du använder en av dessa i stället för att göra det själv.  Observera att endast konfidentiella klienter (de med en hemlighet) ska verifiera ID-token.  Offentliga program (kod som körs helt på en enhet eller ett nätverk som du inte styr – till exempel en användares webbläsare eller deras hemnätverk) har inte nytta av att verifiera ID-token eftersom en obehörig användare kan fånga upp och redigera de nycklar som används för validering av token.

Information om hur du verifierar token manuellt finns i steginformationen [för att verifiera en åtkomsttoken](access-tokens.md#validating-tokens). När du har verifierat signaturen för token ska följande JWT-anspråk verifieras i id_token (dessa kan också göras av ditt tokenvalideringsbibliotek):

* Tidsstämplar: `iat` `nbf` tidsstämplar , `exp` och bör alla faller före eller efter aktuell tid, efter behov.
* Målgrupp: `aud` anspråket ska matcha appens ID för ditt program.
* Nonce: `nonce` anspråket i nyttolasten måste matcha nonce-parametern som skickades till /authorize-slutpunkten under den första begäran.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [åtkomsttoken](access-tokens.md)
* Anpassa JWT-anspråken i din id_token med [valfria anspråk](active-directory-optional-claims.md).
