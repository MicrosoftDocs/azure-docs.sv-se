---
title: Så här hanterar du ITP (intelligent Tracking Protection) i Safari | Azure
titleSuffix: Microsoft identity platform
description: SPA-autentisering (Single-Side app) när cookies från tredje part inte längre tillåts.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753962"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Hantera ITP i Safari och andra webbläsare där cookies från tredje part blockeras

I många webbläsare blockeras cookies från tredje part – cookies på begär anden till domäner som inte är samma som den som visas i webbläsarens fält. Detta avbryter det implicita flödet och kräver nya autentiserings mönster för att kunna logga in användare. I Microsoft Identity Platform använder vi Authorization Flow med PKCE och uppdaterar tokens för att hålla användare inloggade när cookies från tredje part blockeras.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Vad är ITP (intelligent Tracking Protection)?

Apple Safari har en funktion som är av sekretess skydd som kallas [intelligent spårnings skydd](https://webkit.org/tracking-prevention-policy/)eller *ITP*. ITP blockerar cookies från tredje part, cookies på begär Anden som korsar domäner.

En vanlig typ av användar spårning görs genom att läsa in en iframe till tredje parts webbplats i bakgrunden och använda cookies för att korrelera användaren över Internet. Tyvärr är det här mönstret också standard sättet att implementera det [implicita flödet](v2-oauth2-implicit-grant-flow.md) i en Enkels Ides appar (SPAs). När en webbläsare blockerar cookies från tredje part för att förhindra användar spårning, bryts även SPAs.

Safari är inte enskilt i blockering av cookies från tredje part för att förbättra användar sekretessen. Brave har blockerat cookies från tredje part som standard och krom (plattformen bakom Google Chrome och Microsoft Edge) har meddelat att de också kommer att sluta stödja cookies från tredje part i framtiden.

Den lösning som beskrivs i den här artikeln fungerar i alla dessa webbläsare, eller så blockeras cookies från tredje part.

## <a name="overview-of-the-solution"></a>Översikt över lösningen

För att fortsätta autentisera användare i SPAs måste Apps-utvecklare använda [Authorization Code Flow](v2-oauth2-auth-code-flow.md). I auth Code Flow utfärdar identitets leverantören en kod och SPA-filen löser in koden för en åtkomsttoken och en uppdateringstoken. När appen kräver ytterligare tokens kan den använda [token för uppdateringstoken](v2-oauth2-auth-code-flow.md#refresh-the-access-token) för att hämta nya tokens. MSAL.js 2,0, Microsoft Identity Platform-biblioteket för SPAs, implementerar auktoriseringskod-flödet för SPAs och, med mindre uppdateringar, en direkt ersättning för MSAL.js 1. x.

För Microsoft Identity Platform, SPAs och interna klienter följer liknande protokoll vägledning:

* Använda en [PKCE kod utmaning](https://tools.ietf.org/html/rfc7636)
    * PKCE *krävs* för SPAs på Microsoft Identity Platform. PKCE *rekommenderas* för interna och konfidentiella klienter.
* Ingen användning av en klient hemlighet

SPAs har två ytterligare begränsningar:

* [Omdirigerings-URI: n måste `spa` markeras som typ](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) Aktivera CORS på inloggnings slut punkter.
* Uppdateringstoken som utfärdats genom auktoriseringskod för `spa` omdirigering av URI: er har en varaktighet på 24 timmar i stället för en livs längd på 90 dagar.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram som visar OAuth 2-auktoriseringskod mellan en app med en enda sida och slut punkten för säkerhetstoken." border="false":::

## <a name="performance-and-ux-implications"></a>Prestanda-och UX-konsekvenser

Vissa program som använder det implicita flödet vid försök att logga in utan att dirigera om genom att öppna en inloggnings-iframe med `prompt=none` . I de flesta webbläsare svarar den här begäran med token för den för tillfället inloggade användaren (förutsatt att medgivande redan har beviljats). Det här mönstret kräver inte en fullständig omdirigering för att kunna signera användaren i, förbättra prestanda och användar upplevelse – användaren besöker webb sidan och är redan inloggad. Eftersom `prompt=none` i en iframe inte längre är ett alternativ när cookies från tredje part blockeras, måste programmen gå till inloggnings sidan i en ram på översta nivån för att få en auktoriseringskod utfärdad.

Det finns två sätt att utföra inloggning:

* **Full sid omdirigering**
    * Vid den första belastningen på SPA-filen omdirigerar du användaren till inloggnings sidan om det inte redan finns en session (eller om sessionen har upphört att gälla). Användarens webbläsare kommer att besöka inloggnings sidan, presentera de cookies som innehåller användarsessionen och sedan omdirigera tillbaka till programmet med koden och tokens i ett fragment.
    * Omdirigeringen leder till att SPA läses in två gånger. Följ metod tips för cachelagring av SPAs så att appen inte laddas ned helt två gånger.
    * Överväg att använda en för inläsning i appen som söker efter en inloggningssession och omdirigeras till inloggnings sidan innan appen helt packar upp och kör Java Script-nyttolasten.
* **Popup**
    * Om användar upplevelsen av en hel sida som omdirigeras inte fungerar för programmet kan du överväga att använda ett popup-fönster för att hantera autentisering.
    * När popup-fönstret avslutar omdirigeringen till programmet efter autentisering, kommer kod i omdirigeraren att lagra koden och tokens i lokal lagring för det program som ska användas. MSAL.js stöder popup-fönster för autentisering, som de flesta bibliotek.
    * Webbläsare minskar stödet för popup-fönster, så de kanske inte är det mest pålitliga alternativet. Användar interaktion med SPA innan du skapar popup-fönstret kan behövas för att uppfylla webb läsar kraven.

>[!NOTE]
> Apple [beskriver en popup-metod](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) som en tillfällig kompatibilitetskorrigering för att ge det ursprungliga fönstret åtkomst till cookies från tredje part. Apple kan ta bort den här delegeringen av behörigheter i framtiden, men den kommer inte att påverka vägledningen här. Här används popup-fönstret som en första parts navigering till inloggnings sidan så att en session hittas och en auth-kod kan anges. Detta bör fortsätta att arbeta i framtiden.

### <a name="a-note-on-iframe-apps"></a>En anteckning om iframe-appar

Ett vanligt mönster i Web Apps är att använda en iframe för att bädda in en app inuti en annan. Den översta ramen hanterar autentisering av användaren och det program som finns i iframe kan lita på att användaren är inloggad, Hämta token tyst med det implicita flödet. Hämtning av tyst token fungerar inte längre när cookies från tredje part blockeras – programmet som är inbäddat i iframe måste växla till att använda popup-fönster för att få åtkomst till användarens session eftersom den inte kan gå till inloggnings sidan.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Säkerhets aspekter av uppdateringstoken i webbläsaren

Utfärdande av uppdateringstoken till webbläsaren betraktas som ett säkerhets problem. XSS-attacker (Cross-Site Scripting) eller komprometterade JS-paket kan stjäla uppdateringstoken och använda den på distans tills den upphör att gälla eller återkallas. För att minimera risken för stulna uppdateringstoken kommer SPAs att utfärda token som är giltiga under bara 24 timmar. Efter 24 timmar måste appen Hämta en ny auktoriseringskod via ett RAM-minne på den översta nivån till inloggnings sidan.

Detta mönster för spårningstoken för begränsad livs längd valdes som balans mellan säkerhet och försämrat UX. Utan att uppdatera tokens eller cookies från tredje part blir auktoriseringskod (som rekommenderas av den rekommenderade [aktuella praxisen för OAuth-säkerhet](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) en förlust när nya eller ytterligare token krävs. En fullständig Omdirigerad sida eller popup krävs för varje enskild token, varje gång en token går ut (varje timme vanligt vis för Microsoft Identity Platform-token).

## <a name="next-steps"></a>Nästa steg

Läs mer om [flödet för auktoriseringskod](v2-oauth2-auth-code-flow.md).

Testa koden för auktoriseringskod med snabb starten för [MSAL.js 2,0](quickstart-v2-javascript-auth-code.md).
