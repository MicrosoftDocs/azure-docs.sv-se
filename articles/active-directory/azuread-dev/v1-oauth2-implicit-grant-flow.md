---
title: Förstå det OAuth2 implicita bidrags flödet i Azure AD | Microsoft Docs
description: Lär dig mer om Azure Active Directory implementering av OAuth2 implicita bidrags flöde och om det är rätt för ditt program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154516"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Förstå OAuth2-flödet för implicit bidrag i Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth2 implicita bidrag är Notorious för tilldelning med den längsta listan med säkerhets problem i OAuth2-specifikationen. Men det är den metod som implementeras av ADAL JS och den som vi rekommenderar när du skriver SPA-program. Vad ger? Det är allt som är fördelaktigt: och när det blir slut är den implicita tilldelningen den bästa metoden som du kan utföra för program som använder ett webb-API via Java Script från en webbläsare.

## <a name="what-is-the-oauth2-implicit-grant"></a>Vad är OAuth2 implicit beviljad?

Quintessential [OAuth2 Authorization Code Granting](https://tools.ietf.org/html/rfc6749#section-1.3.1) är den auktorisering som använder två separata slut punkter. Slut punkten för auktorisering används för användar interaktions fasen, som resulterar i en auktoriseringskod. Token-slutpunkten används sedan av klienten för att utväxla koden för en åtkomsttoken, och ofta även en uppdateringstoken. Webb program krävs för att presentera sina egna programautentiseringsuppgifter för token-slutpunkten, så att auktoriseringsservern kan autentisera klienten.

[OAuth2 implicita bidrag](https://tools.ietf.org/html/rfc6749#section-1.3.2) är en variant av andra auktoriserings bidrag. Det gör att en klient kan hämta en åtkomsttoken (och id_token när du använder [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)) direkt från slut punkten för auktorisering, utan att kontakta token-slutpunkten eller autentisera klienten. Den här varianten har utformats för JavaScript-baserade program som körs i en webbläsare: i den ursprungliga OAuth2-specifikationen returneras tokens i ett URI-fragment. Det gör token-bitarna tillgängliga för JavaScript-koden i klienten, men den garanterar att de inte tas med i omdirigeringar mot servern. I OAuth2 implicit beviljande, utfärdar behörighets slut punkten direkt till klienten med hjälp av en omdirigerings-URI som tidigare har angetts. Det har också fördelen att ta bort eventuella krav för cross origin-anrop, vilket är nödvändigt om JavaScript-programmet krävs för att kontakta token-slutpunkten.

En viktig egenskap för den implicita tilldelningen av OAuth2 är faktum att sådana flöden aldrig returnerar uppdateringstoken till klienten. I nästa avsnitt visas hur detta inte är nödvändigt och skulle faktiskt vara ett säkerhets problem.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2 implicita bidrag

OAuth2-specifikationen deklarerar att den implicita beviljandet har gjorts för att aktivera användar agent program – det vill säga, JavaScript-program som körs i en webbläsare. Att definiera egenskaper för sådana program är att JavaScript-kod används för att få åtkomst till server resurser (vanligt vis ett webb-API) och för att uppdatera program användar upplevelsen i enlighet med detta. Tänk på program som Gmail eller Outlook Web Access: när du väljer ett meddelande från din inkorg ändras endast panelen för meddelande visualisering så att den nya markeringen visas, medan resten av sidan förblir oförändrad. Den här egenskapen är i motsats till traditionella omdirigerade webbappar, där varje användar interaktion resulterar i ett full sid återanslående och en full sid åter givning av det nya server svaret.

Program som tar den JavaScript-baserade metoden till dess extrema kallas för program med en sida eller SPAs. Idén är att dessa program endast har en första HTML-sida och tillhör ande Java Script, och att alla efterföljande interaktioner drivs av webb-API-anrop som utförs via Java Script. Hybrid metoder, där programmet huvudsakligen är återanslående, men som utför tillfälliga JS-anrop, är inte ovanliga – diskussionen om implicit flödes användning är också relevant för dem.

Omdirigerade program säkrar vanligt vis förfrågningar via cookies, men den metoden fungerar inte även för JavaScript-program. Cookies fungerar endast mot den domän de har genererats för, medan JavaScript-anrop kan dirigeras till andra domäner. I själva verket är det ofta fallet: Tänk på att program som anropar Microsoft Graph API, Office API, Azure API – alla finns utanför domänen där programmet hanteras. En växande trend för JavaScript-program är att inte ha någon server del, som förlitar sig på 100% på webb-API: er från tredje part för att implementera sin affärs funktion.

För närvarande är det bästa sättet att skydda anrop till ett webb-API att använda metoden OAuth2 Bearer-token, där varje anrop åtföljs av en OAuth2-åtkomsttoken. Webb-API: et undersöker inkommande åtkomsttoken och, om den hittar en nödvändig omfattning, beviljar åtkomst till den begärda åtgärden. Det implicita flödet ger en bekväm mekanism för JavaScript-program för att få åtkomst-token för ett webb-API, vilket ger flera fördelar avseende cookies:

* Token kan erhållas på ett tillförlitligt sätt utan behov av cross origin-anrop – obligatorisk registrering av omdirigerings-URI: n som token är retur garantier till att token inte har avplacerats
* JavaScript-program kan hämta så många åtkomsttoken som de behöver, för så många webb-API: er som de är riktade till – utan begränsning för domäner
* HTML5-funktioner som session eller lokal lagring ger fullständig kontroll över cachelagring av token och livs längd hantering, medan cookies-hanteringen är ogenomskinlig för appen
* Åtkomst-token är inte mottagliga för CSRF-attacker (Cross-Site request förfalskning)

Det implicita tilldelnings flödet utfärdar inte uppdaterade token, av säkerhets skäl. En uppdateringstoken är inte lika begränsad som åtkomst-token, vilket ger mycket mer energi inflictingt mycket mer skada om den läcker ut. I det implicita flödet levereras token i URL: en, och därför är risken för avlyssning högre än i tillåtndet av auktoriseringskod.

Ett JavaScript-program har dock en annan mekanism för att förnya åtkomsttoken utan att efter fråga användaren om autentiseringsuppgifter. Programmet kan använda en dold iframe för att utföra nya Tokenbegäran mot behörighets slut punkten för Azure AD: så länge webbläsaren fortfarande har en aktiv session (läsa: har en sessions-cookie) mot Azure AD-domänen kan autentiseringsbegäran utföras utan att användaren behöver vidta några åtgärder.

Den här modellen ger JavaScript-programmet möjlighet att oberoende förnya åtkomsttoken och till och med hämta nya för ett nytt API (förutsatt att användaren tidigare har meddelats för dem). På så sätt undviker du den extra bördan för att förvärva, underhålla och skydda en hög värdes artefakt, till exempel en Refresh-token. Artefakten som gör den tysta förnyelsen möjlig, Azure AD-sessionens cookie, hanteras utanför programmet. En annan fördel med den här metoden är att en användare kan logga ut från Azure AD med hjälp av något av de program som loggats in i Azure AD, som körs i någon av flikarna i webbläsaren. Detta resulterar i att du tar bort Azure AD-sessionens cookie och att JavaScript-programmet automatiskt förlorar möjligheten att förnya token för den inloggade användaren.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Är den implicita beviljandet lämplig för min app?

Den implicita tilldelningen ger fler risker än andra bidrag och de områden som du behöver tänka på är väl dokumenterade (till exempel [missbruk av åtkomsttoken för att personifiera resurs ägaren i implicit flöde][OAuth2-Spec-Implicit-Misuse] och [OAuth 2,0 hot modell och säkerhets aspekter][OAuth2-Threat-Model-And-Security-Implications]). Den högre risk profilen är dock stor på grund av det faktum att den är avsedd att aktivera program som kör aktiv kod och som hanteras av en fjär resurs till en webbläsare. Om du planerar en SPA-arkitektur, inte har några Server dels komponenter eller tänker anropa ett webb-API via Java Script, rekommenderas användningen av det implicita flödet för hämtning av token.

Om ditt program är en intern klient är det implicita flödet inte en bra anpassning. Frånvaron av Azure AD-sessionens cookie i kontexten för en intern klient berövar ditt program från att underhålla en lång livs längd session. Det innebär att ditt program kommer att upprepade gånger uppmana användaren att hämta åtkomsttoken för nya resurser.

Om du utvecklar ett webb program som innehåller en server del och använder ett API från sin backend-kod, är det implicita flödet inte heller en bra anpassning. Andra bidrag ger dig mycket mer kraft. Till exempel ger OAuth2-klientens autentiseringsuppgifter möjlighet att hämta token som återspeglar de behörigheter som tilldelats själva programmet, i stället för användar delegeringar. Det innebär att klienten har möjlighet att underhålla program mässig åtkomst till resurser även om en användare inte aktive ras aktivt i en session och så vidare. Inte bara det, men sådana bidrag ger högre säkerhets garantier. Till exempel går det inte att komma åt token via användarens webbläsare, de riskerar inte att sparas i webb läsar historiken och så vidare. Klient programmet kan också utföra stark autentisering när du begär en token.

## <a name="next-steps"></a>Nästa steg

* Se [så här integrerar du ett program med Azure AD][ACOM-How-To-Integrate] för ytterligare djup i program integrerings processen.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
