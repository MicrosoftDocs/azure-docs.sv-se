---
title: Azure Active Directory referens för att bryta ändringar
description: Lär dig mer om ändringar som gjorts i Azure AD-protokollen som kan påverka ditt program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 2/22/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c5e7f556f37a1d6d53e0a938490f1099a7be776a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647429"
---
# <a name="whats-new-for-authentication"></a>Vad är nytt för autentisering?

> Få ett meddelande om uppdateringar till den här sidan genom att klistra in URL: en i din RSS-feed läsare:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

Autentiserings systemet ändrar och lägger till funktioner kontinuerligt för att förbättra kompatibiliteten för säkerhet och standarder. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om följande uppgifter:

- De senaste funktionerna
- Kända problem
- Protokoll ändringar
- Föråldrade funktioner

> [!TIP]
> Den här sidan uppdateras regelbundet, så besök ofta. Om inget annat anges placeras ändringarna bara på plats för nyligen registrerade program.

## <a name="upcoming-changes"></a>Kommande ändringar

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>Villkorlig åtkomst utlöser endast för explicit begärda omfattningar

**Effektivt datum**: mars 2021

**Påverkade slut punkter**: v 2.0

**Protokoll som påverkas**: alla flöden som använder [dynamiskt medgivande](v2-permissions-and-consent.md#requesting-individual-user-consent)

Program som använder dynamiskt medgivande har idag fått alla behörigheter som de har godkänt för, även om de inte har begärts i `scope` parametern efter namn.  Detta kan orsaka att en app begär det `user.read` , men med medgivande till `files.read` , att tvingas att skicka den villkorliga åtkomst som tilldelats för `files.read` behörigheten. 

För att minska antalet onödiga åtkomst-prompter, ändrar Azure AD det sätt på vilket de begärda omfången tillhandahålls till program, så att endast uttryckligen begärda omfattningar utlöser villkorlig åtkomst. Den här ändringen kan leda till att appar är beroende av Azure AD: s tidigare beteende (dvs. ger alla behörigheter även när de inte begärdes) att brytas, eftersom de token som de begär saknar behörighet.

Appar får nu åtkomst-token med en blandning av behörigheter i detta, samt de som de har godkänt för och som inte kräver några begär Anden om villkorlig åtkomst.  Omfattningarna för åtkomsttoken avspeglas i token-svarets `scope` parameter. 

**Exempel**

En app har medgivande för `user.read` , `files.readwrite` och `tasks.read` . `files.readwrite` har principer för villkorlig åtkomst tillämpade på den, medan de andra två inte gör det. Om en app gör en Tokenbegäran för `scope=user.read` och den inloggade användaren inte har skickat några principer för villkorlig åtkomst, är den resulterande token för- `user.read` och- `tasks.read` behörigheterna. `tasks.read` ingår eftersom appen har ett godkännande för den, och det krävs ingen princip för villkorlig åtkomst för att tillämpas. 

Om appen sedan begär `scope=files.readwrite` det kommer den villkorliga åtkomst som krävs av klienten att utlösas, vilket tvingar appen att visa en interaktiv auth-prompt där principen för villkorlig åtkomst kan uppfyllas.  Den returnerade token har alla tre omfånget. 

Om appen sedan gör en sista begäran för något av de tre omfången (t. ex. `scope=tasks.read` ), kommer Azure AD se att användaren redan har slutfört de principer för villkorlig åtkomst som behövs för `files.readwrite` och sedan utfärdar en token med alla tre behörigheter i den. 


## <a name="may-2020"></a>Maj 2020

### <a name="azure-government-endpoints-are-changing"></a>Azure Government-slutpunkter ändras

**Start datum**: 5 maj (slutar gälla juni 2020) 

**Påverkade slut punkter**: alla

**Protokoll som påverkas**: alla flöden

Den 1 juni 2018, den officiella Azure Active Directory (AAD) som Azure Government ändrats från `https://login-us.microsoftonline.com` till `https://login.microsoftonline.us` . Den här ändringen gäller också för Microsoft 365 GCC hög och DoD, som även Azure Government AAD. Om du äger ett program inom en amerikansk myndighets klient måste du uppdatera ditt program för att logga in användare i `.us` slut punkten.  

Från och med den 5 maj kommer Azure AD att börja verkställa slut punkts ändringen, vilket hindrar myndighets användare från att logga in på appar som finns i amerikanska myndighets klienter med hjälp av den offentliga slut punkten ( `microsoftonline.com` ).  Påverkade appar påbörjar ett fel `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Det här felet indikerar att appen försöker logga in hos en amerikansk myndighets användare på den offentliga moln slut punkten. Om din app finns i en offentlig moln klient och är avsedd att stödja användare av amerikanska myndigheter, måste du [Uppdatera din app så att den stöder dem uttryckligen](./authentication-national-cloud.md). Detta kan kräva att du skapar en ny app-registrering i det amerikanska myndighets molnet. 

Verkställighet av den här ändringen görs med hjälp av en gradvis distribution baserat på hur ofta användare från molnet för amerikanska myndigheter loggar in på program-appar som loggar in i amerikanska myndighets användare som inte ofta ser tvångs tvång och appar som ofta används av amerikanska myndighets användare kommer att ha tillämpat tvång. Vi förväntar oss att tvång är slutfört för alla appar i juni 2020. 

Mer information finns i [blogg inlägget Azure Government för migreringen](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>Mars 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Användar lösen ord är begränsade till 256 tecken.

**Effektivt datum**: 13 mars 2020

**Påverkade slut punkter**: alla

**Protokoll som påverkas**: alla användar flöden.

Användare med lösen ord som är längre än 256 tecken som loggar in direkt i Azure AD (till skillnad från en federerad IDP som ADFS) kan inte logga in från den 13 mars 2020 och uppmanas att återställa sitt lösen ord i stället.  Administratörer kan ta emot förfrågningar för att hjälpa till att återställa användarens lösen ord.

Felet i inloggnings loggarna blir AADSTS 50052: InvalidPasswordExceedsMaxLength

Meddelande `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Reparation

Användaren kan inte logga in eftersom lösen ordet överskrider den tillåtna max längden. De bör kontakta administratören för att återställa lösen ordet. Om SSPR har Aktiver ATS för klient organisationen kan de återställa sitt lösen ord genom att följa länken "glömt lösen ordet".



## <a name="february-2020"></a>Februari 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Tomma fragment läggs till i varje HTTP-omdirigering från inloggnings slut punkten.

**Effektivt datum**: 8 februari 2020

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: OAuth-och OIDC-flöden som använder response_type = fråga – detta täcker [kod flödet för auktorisering](v2-oauth2-auth-code-flow.md) i vissa fall och det [implicita flödet](v2-oauth2-implicit-grant-flow.md).

När ett svar på autentisering skickas från login.microsoftonline.com till ett program via HTTP-omdirigering, läggs ett tomt fragment till i svars-URL: en.  Detta förhindrar en klass av omdirigerings attacker genom att se till att webbläsaren rensar alla befintliga fragment i autentiseringsbegäran.  Inga appar bör ha ett beroende på detta beteende.


## <a name="august-2019"></a>Augusti 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Ifyllning av POST formulär kommer att tvingas mer strikta blank steg och citat tecken ignoreras

**Ikraftträdande datum**: 2 september 2019

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: post överallt används ([klientautentiseringsuppgifter](./v2-oauth2-client-creds-grant-flow.md), [auktoriseringskod inlösen](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)och [Refresh token inlösen](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

Med början av veckan 9/2 verifieras autentiseringsbegäranden som använder POST-metoden med strängare HTTP-standarder.  Mer specifikt kommer blank steg och dubbla citat tecken (") inte längre att tas bort från begär ande formulär värden. De här ändringarna förväntas inte bryta några befintliga klienter och säkerställer att förfrågningar som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång. I framtiden (se ovan) planerar vi att även avvisa dubbla parametrar och ignorera strukturen i begär Anden.

Exempel:

Idag `?e=    "f"&g=h` parsas de likadant som de är `?e=f&g=h` `e`  ==  `f` .  Med den här ändringen kommer den nu att tolkas så att `e`  ==  `    "f"` -det är osannolikt att det är ett giltigt argument, och begäran kunde nu inte utföras.


## <a name="july-2019"></a>Juli 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Endast app-token för program med en enda klient utfärdas endast om klient programmet finns i resurs klienten

**Gällande datum**: 26 juli 2019

**Påverkade slut punkter**: både [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) och [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

**Protokoll som påverkas**: [klientautentiseringsuppgifter (endast app-token)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

En säkerhets ändring förblev 26 i juli som ändrar det sätt på vilket endast app-token (via tilldelning av klientautentiseringsuppgifter) utfärdas. Tidigare har program tillåtits att hämta token för att anropa alla andra appar, oavsett närvaro i klienten eller roller som meddelats för programmet.  Det här beteendet har uppdaterats så att resurser (ibland kallade webb-API: er) är en enskild klient (standard) och att klient programmet måste finnas i resurs klienten.  Observera att befintliga medgivande mellan klienten och API: n fortfarande inte behövs, och apparna bör fortfarande utföra sina egna verifierings kontroller för att säkerställa att det finns ett `roles` anspråk och innehåller det förväntade värdet för API: et.

Fel meddelandet för det här scenariot är för närvarande tillstånd:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Du kan åtgärda det här problemet genom att använda administratörs medgivande för att skapa klient program tjänstens huvud namn i din klient organisation eller skapa den manuellt.  Detta krav garanterar att klienten har gett programmet behörighet att köras i klienten.

#### <a name="example-request"></a>Exempelbegäran

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` I det här exemplet är resurs klienten (auktoritet) contoso.com, Resource-appen är en app med en klient som anropas `gateway.contoso.com/api` för Contoso-klienten och klient programmet är `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Om klient programmet har ett huvud namn för tjänsten i Contoso.com kan denna begäran fortsätta.  Om den inte gör det Miss känner dock begäran med ovanstående fel.

Om contoso Gateway-appen var ett program för flera innehavare, skulle begäran fortsätta oavsett vilken klient app som har ett huvud namn för tjänsten i Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omdirigerings-URI: er kan nu innehålla parametrar för frågesträng

**Gällande datum**: 22 juli 2019

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: alla flöden

I [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)kan Azure AD-program nu registrera och använda omdirigerings-URI: er (reply) med statiska frågeparametrar (till exempel `https://contoso.com/oauth2?idp=microsoft` ) för OAuth 2,0-begäranden.  Dynamiska omdirigerings-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och det kan inte användas för att bevara tillståndsinformation i en autentiseringsbegäran – för den, använder du `state` parametern.

Den statiska Frågeparametern omfattas av sträng matchning för omdirigering av URI: er som andra delar av omdirigerings-URI: n – om ingen sträng registreras som matchar den URI-avkodade redirect_uri, kommer begäran att avvisas.  Om URI: n hittas i appens registrering, kommer hela strängen att användas för att omdirigera användaren, inklusive parametern för statisk fråga.

Observera att i den här tiden (slutet av juli 2019), är app Registration-UX i Azure Portal fortfarande blockera frågeparametrar.  Du kan dock redigera applikations manifestet manuellt för att lägga till frågeparametrar och testa detta i appen.


## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Loopar till klienter avbryts

**Start datum**: 25 mars 2019

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: alla flöden

Klient program kan ibland vara felödande, vilket ger hundratals olika inloggnings begär Anden under en kort tids period.  Dessa förfrågningar kanske inte lyckas, men alla bidrar till dåliga användar upplevelser och förhöjda arbets belastningar för IDP, ökande svars tid för alla användare och minskad tillgänglighet för IDP.  Programmen körs utanför gränserna för normal användning och bör uppdateras så att de fungerar korrekt.

Klienter som utfärdar dubbla förfrågningar flera gånger skickas ett `invalid_grant` fel: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

De flesta klienter behöver inte ändra beteende för att undvika det här felet.  Det här felet påverkas bara av felkonfigurerade klienter (de utan cachelagring av token eller de som uppvisar prompterna redan).  Klienter spåras baserat på varje instans lokalt (via cookie) på följande faktorer:

* Användar tips, om något

* Omfattningar eller resurs som begärs

* Klient-ID

* Omdirigerings-URI

* Svars typ och läge

Appar som gör flera förfrågningar (15 +) under en kort tids period (5 minuter) får ett `invalid_grant` fel som förklarar att de är slingor.  De token som begärs har tillräckligt lång livs längd per timme (minst 10 minuter, 60 minuter som standard), så upprepade begär Anden under den här tids perioden är onödigt.

Alla appar bör hantera `invalid_grant` genom att visa en interaktiv fråga i stället för att begära en token i bakgrunden.  För att undvika det här felet bör klienterna se till att de cachelagrar de token som de tar emot korrekt.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Auktoriseringsregler kan inte längre återanvändas

**Start datum**: 15 november 2018

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: [kod flöde](v2-oauth2-auth-code-flow.md)

Från den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringsnivåer för appar. Den här säkerhets ändringen hjälper till att placera Azure AD i linje med OAuth-specifikationen och tillämpas på både v1-och v2-slutpunkter.

Om appen återanvänder auktoriseringsregler för att hämta tokens för flera resurser, rekommenderar vi att du använder koden för att hämta en uppdateringstoken, och sedan använder den uppdateringstoken för att hämta ytterligare token för andra resurser. Det går bara att använda auktoriseringsregler en gång, men uppdaterade token kan användas flera gånger i flera resurser. Alla nya appar som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant fel.

Mer information om uppdateringstoken finns i [Uppdatera åtkomsttoken](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Om du använder ADAL eller MSAL hanteras detta av biblioteket – Ersätt den andra instansen av "AcquireTokenByAuthorizationCodeAsync" med "AcquireTokenSilentAsync".

## <a name="may-2018"></a>Maj 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Det går inte att använda ID-token för OBO-flödet

**Datum: den** 1 maj 2018

**Påverkade slut punkter**: både v 1.0 och v 2.0

**Protokoll som påverkas**: implicit flöde och [på uppdrag av flöde](v2-oauth2-on-behalf-of-flow.md)

Efter den 1 maj 2018 går det inte att använda id_tokens som kontroll i ett OBO flöde för nya program. Åtkomsttoken ska användas i stället för att skydda API: er, även mellan en klient och mellan nivån för samma program. Appar som registrerats före den 1 maj 2018 kommer att fortsätta att fungera och kunna utväxla id_tokens för en åtkomsttoken. Detta mönster anses dock inte vara en bästa praxis.

För att undvika den här ändringen kan du göra följande:

1. Skapa ett webb-API för ditt program med ett eller flera omfång. Den här explicita start punkten ger bättre kornig kontroll och säkerhet.
1. I appens manifest, i [Azure Portal](https://portal.azure.com) eller i [appens registrerings Portal](https://apps.dev.microsoft.com), kontrollerar du att appen tillåts utfärda åtkomsttoken via det implicita flödet. Detta styrs via `oauth2AllowImplicitFlow` nyckeln.
1. När klient programmet begär en id_token via `response_type=id_token` , begär även en åtkomsttoken ( `response_type=token` ) för det webb-API som skapades ovan. När du använder v 2.0-slutpunkten `scope` bör parametern se ut ungefär så här `api://GUID/SCOPE` . På den v 1.0-slut punkten `resource` ska parametern vara app-URI: n för webb-API: et.
1. Skicka denna åtkomsttoken till mellan nivån i stället för id_token.
