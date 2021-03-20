---
title: Fel kods referens
titleSuffix: Azure AD B2C
description: En lista över felkoder som kan returneras av Azure Active Directory B2Cs tjänsten.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6ae806b7666d83652e6b82bac16d89f2f9ce7aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215441"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Felkoder: Azure Active Directory B2C

Följande fel kan returneras av tjänsten Azure Active Directory B2C.

| Felkod | Meddelande |
| ---------- | ------- |
| `AADB2C90002` | CORS-resursen {0} returnerade en 404 hittades inte. |
| `AADB2C90006` | Omdirigerings-URI: n {0} som tillhandahölls i begäran har inte registrerats för klient-ID: t {1} . |
| `AADB2C90007` | Programmet som är associerat med klient-ID: t {0} har inga registrerade omdirigerings-URI: er. |
| `AADB2C90008` | Begäran innehåller inte någon klient-ID-parameter. |
| `AADB2C90010` | Begäran innehåller ingen omfattnings parameter. |
| `AADB2C90011` | Klient-ID: t {0} som tillhandahölls i begäran matchar inte klient-ID: t som har {1} registrerats i principen. |
| `AADB2C90012` | Omfånget {0} som tillhandahölls i förfrågan stöds inte. |
| `AADB2C90013` | Den begärda svars typen {0} som tillhandahölls i begäran stöds inte. |
| `AADB2C90014` | Det begärda svars läget {0} som tillhandahölls i begäran stöds inte. |
| `AADB2C90016` | Den begärda klient kontroll typen {0} matchar inte den förväntade typen {1} . |
| `AADB2C90017` | Den klient kontroll som tillhandahölls i begäran är ogiltig: {0} |
| `AADB2C90018` | Klient-ID: t {0} som anges i begäran har inte registrerats i klient organisationen {1} . |
| `AADB2C90019` | Nyckel containern med ID {0} i klient organisationen har ingen {1} giltig nyckel. Orsak: {2} . |
| `AADB2C90021` | Den tekniska profilen {0} finns inte i principen {1} för klienten {2} . |
| `AADB2C90022` | Det gick inte att returnera metadata för principen {0} i klient organisationen {1} . |
| `AADB2C90023` | Profilen {0} innehåller inte den nödvändiga metadata-nyckeln {1} . |
| `AADB2C90025` | Profilen {0} i principen {1} i klienten {2} innehåller inte den nödvändiga kryptografiska nyckeln {3} . |
| `AADB2C90027` | De grundläggande autentiseringsuppgifterna {0} som har angetts för är ogiltiga. Kontrol lera att autentiseringsuppgifterna är korrekta och att åtkomsten har beviljats av resursen. |
| `AADB2C90028` | Det angivna klient certifikatet för {0} är ogiltigt. Kontrol lera att certifikatet är korrekt, innehåller en privat nyckel och att åtkomsten har beviljats av resursen. |
| `AADB2C90031` | Principen {0} anger ingen standard användar resa. Se till att principen eller dess föräldrar anger en standard användar resa som en del av avsnittet för förlitande part. |
| `AADB2C90035` | Tjänsten är inte tillgänglig för tillfället. Försök igen om några minuter. |
| `AADB2C90036` | Begäran innehåller ingen URI för att omdirigera användaren att publicera logga ut. Ange en URI i fältet post_logout_redirect_uri parameter. |
| `AADB2C90037` | Ett fel uppstod när begäran bearbetades. Kontakta administratören för den plats som du försöker få åtkomst till. |
| `AADB2C90039` | Begäran innehåller en klient kontroll, men den angivna principen {0} i klienten {1} saknar en Client_secret i RelyingPartyPolicy. |
| `AADB2C90040` | Användar resan {0} innehåller inte något steg för att skicka anspråk. |
| `AADB2C90043` | Frågan som ingår i begäran innehåller ogiltiga värden. ' None ', ' inloggning ", ' medgivande ' eller ' select_account ' förväntades. |
| `AADB2C90044` | Anspråket {0} stöds inte av anspråks matcharen {1} . |
| `AADB2C90046` | Vi har problem med att läsa in ditt nuvarande tillstånd. Du kanske vill försöka starta-sessionen från början. |
| `AADB2C90047` | Resursen {0} innehåller skript fel som förhindrar att den läses in. |
| `AADB2C90048` | Ett ohanterat undantag har inträffat på servern. |
| `AADB2C90051` | Det gick inte att hitta några lämpliga anspråks leverantörer. |
| `AADB2C90052` | Ogiltigt användar namn eller lösen ord. |
| `AADB2C90053` | Det gick inte att hitta en användare med de angivna autentiseringsuppgifterna. |
| `AADB2C90054` | Ogiltigt användar namn eller lösen ord. |
| `AADB2C90055` | Omfånget {0} som anges i begäran måste ange en resurs, till exempel https://example.com/calendar.read . |
| `AADB2C90057` | Det tillhandahållna programmet har inte kon figurer ATS för att tillåta OAuth-implicit flöde. |
| `AADB2C90058` | Det tillhandahållna programmet har inte kon figurer ATS för att tillåta offentliga klienter. |
| `AADB2C99059` | Den angivna begäran måste presentera en code_challenge. Krävs för appar med en sida med hjälp av flödes kods flödet.| 
| `AADB2C90067` | Omutloggning omdirigerings-URI: n {0} har ett ogiltigt format. Ange en https-baserad URL, till exempel " https://example.com/return " eller för interna klienter använder den inbyggda IETF-klient-URI: n "urn: IETF: WG: OAuth: 2.0: OOB". |
| `AADB2C90068` | Det angivna programmet med ID {0} är inte giltigt för den här tjänsten. Använd ett program som skapats via B2C-portalen och försök igen. |
| `AADB2C90075` | Anspråks utbytet {0} som anges i steg {1} returnerade http-felsvaret med koden {2} och skälet {3} . |
| `AADB2C90077` | Användaren har inte någon befintlig session och begär ande prompt-parameter har värdet {0} . |
| `AADB2C90079` | Klienter måste skicka ett client_secret när de löser in ett konfidentiellt bidrag. |
| `AADB2C90080` | Den tillhandahållna tilldelningen har upphört att gälla. Autentisera igen och försök igen. Aktuell tid: {0} , beviljande tid för utfärdande {1} :, under förfallo tid för att tilldela glidande fönster: {2} . |
| `AADB2C90081` | Den angivna client_secret matchar inte det förväntade värdet för den här klienten. Korrigera client_secret och försök igen. |
| `AADB2C90083` | Begäran saknar den nödvändiga parametern: {0} . |
| `AADB2C90084` | Offentliga klienter ska inte skicka en client_secret när de löser in ett offentligt förvärvat bidrag. |
| `AADB2C90085` | Ett internt fel uppstod i tjänsten. Autentisera igen och försök igen. |
| `AADB2C90086` | Det angivna grant_type [ {0} ] stöds inte. |
| `AADB2C90087` | Den tillhandahållna beviljandet har inte utfärdats för den här versionen av protokoll slut punkten. |
| `AADB2C90088` | Den tillhandahållna beviljandet har inte utfärdats för den här slut punkten. Faktiskt värde: {0} och förväntat värde: {1} |
| `AADB2C90091` | Användaren avbröt. |
| `AADB2C90092` | Det angivna programmet med ID {0} är inaktiverat för klient organisationen {1} . Aktivera programmet och försök igen. |
| `AADB2C90107` | Programmet med ID: t {0} kan inte hämta en ID-token, antingen på grund av att OpenID-omfånget inte tillhandahölls i begäran eller att programmet inte har behörighet för det. |
| `AADB2C90108` | Orchestration-steget {0} anger ingen CpimIssuerTechnicalProfileReferenceId när ett förväntades. |
| `AADB2C90110` | Omfattnings parametern måste innehålla "OpenID" när du begär ett response_type som innehåller id_token. |
| `AADB2C90111` | Ditt konto har låsts. Kontakta supporten för att låsa upp den och försök sedan igen. |
| `AADB2C90114` | Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare. |
| `AADB2C90115` | När du begär koden response_type måste omfattnings parametern innehålla en resurs-eller klient-ID för åtkomsttoken och "OpenID" för ID-token. Ta även med offline_access för uppdateringstoken. |
| `AADB2C90117` | Omfånget {0} som tillhandahölls i begäran stöds inte. |
| `AADB2C90118` | Användaren har glömt sitt lösen ord. |
| `AADB2C90120` | Den maximala ålders parametern {0} som anges i begäran är ogiltig. Högsta ålder måste vara ett heltal mellan {1} och {2} . |
| `AADB2C90122` | Indatamängden för " {0} " som togs emot i begäran misslyckades med verifiering av HTTP-begäran. Se till att indatamängden inte innehåller tecken som < eller &. |
| `AADB2C90128` | Det konto som är associerat med detta anslag finns inte längre. Autentisera igen och försök igen. |
| `AADB2C90129` | Det tillhandahållna bidraget har återkallats. Autentisera igen och försök igen. |
| `AADB2C90145` | Inga overifierade telefonnummer hittades och principen tillåter inte att en användare anges. |
| `AADB2C90146` | Omfånget {0} som anges i begäran anger fler än en resurs för en åtkomsttoken, vilket inte stöds. |
| `AADB2C90149` | {0}Det gick inte att läsa in skriptet. |
| `AADB2C90151` | Användaren har överskridit det högsta tillåtna antalet återförsök för Multi-Factor Authentication. |
| `AADB2C90152` | En Multi-Factor Polle-begäran kunde inte få svar från tjänsten. |
| `AADB2C90154` | En begäran om Multi-Factor Verification kunde inte hämta ett sessions-ID från tjänsten. |
| `AADB2C90155` | En begäran om Multi-Factor Verification misslyckades med orsaken {0} . |
| `AADB2C90156` | En begäran om Multi-Factor Validation misslyckades med orsaken {0} . |
| `AADB2C90157` | Användaren har överskridit det maximala antalet återförsök för ett självkontrollerat steg. |
| `AADB2C90158` | En egen kontrollerad verifierings förfrågan misslyckades med orsaken {0} . |
| `AADB2C90159` | En begäran om kontrollerad verifiering misslyckades med orsaken {0} . |
| `AADB2C90161` | Ett självkontrollerat sändnings svar misslyckades med orsaken {0} . |
| `AADB2C90165` | SAML-initierings meddelandet med ID: t {0} hittades inte i läget. |
| `AADB2C90168` | HTTP-Redirect begäran innehåller inte den nödvändiga parametern {0} för en signerad begäran. |
| `AADB2C90178` | Signerings certifikatet {0} har ingen privat nyckel. |
| `AADB2C90182` | Den angivna code_verifier överensstämmer inte med tillhör ande code_challenge |
| `AADB2C90183` | Den angivna code_verifier är ogiltig |
| `AADB2C90184` | Den tillhandahållna code_challenge_method stöds inte. Värden som stöds är Plain eller S256 |
| `AADB2C90188` | Den tekniska SAML-profilen ' {0} ' anger en PartnerEntity-URL för ' {1} ', men hämtningen av metadata Miss lyckas med orsaken ' {2} '. |
| `AADB2C90194` | Anspråk som {0} anges för Bearer-token finns inte i de tillgängliga anspråken. Tillgängliga anspråk {1} . |
| `AADB2C90205` | Det här programmet har inte tillräcklig behörighet för den här webb resursen för att utföra åtgärden. |
| `AADB2C90206` | En tids gräns har inträffat vid initiering av klienten. |
| `AADB2C90208` | Den angivna id_token_hint-parametern har upphört att gälla. Ange en annan token och försök igen. |
| `AADB2C90209` | Den tillhandahållna id_token_hint-parametern innehåller inte en accepterad mål grupp. Giltiga mål grupps värden: ' {0} '. Ange en annan token och försök igen. |
| `AADB2C90210` | Det gick inte att verifiera den angivna id_token_hint parametern. Ange en annan token och försök igen. |
| `AADB2C90211` | Begäran innehöll en ofullständig tillstånds-cookie. |
| `AADB2C90212` | Begäran innehöll en ogiltig tillstånds-cookie. |
| `AADB2C90220` | Nyckel behållaren i klient organisationen {0} med lagrings identifieraren {1} finns men innehåller inget giltigt certifikat. Certifikatet kan ha upphört att gälla eller också kan certifikatet bli aktivt i framtiden (NBF). |
| `AADB2C90223` | Ett fel uppstod vid en sanerad CORS-resurs. |
| `AADB2C90224` | Resurs ägarens flöde har inte Aktiver ATS för programmet. |
| `AADB2C90225` | Användar namnet eller lösen ordet som angavs i begäran är ogiltigt. |
| `AADB2C90226` | Angivet token Exchange stöds bara via HTTP POST. |
| `AADB2C90232` | Den angivna id_token_hint-parametern innehåller ingen godkänd utfärdare. Giltiga utfärdare: ' {0} '. Ange en annan token och försök igen. |
| `AADB2C90233` | Den angivna id_token_hint parametern kunde inte verifiera signaturen. Ange en annan token och försök igen. |
| `AADB2C90235` | Den angivna id_token har upphört att gälla. Ange en annan token och försök igen. |
| `AADB2C90237` | Den tillhandahållna id_token innehåller inte en giltig mål grupp. Giltiga mål grupps värden: ' {0} '. Ange en annan token och försök igen. |
| `AADB2C90238` | Den tillhandahållna id_token innehåller ingen giltig utfärdare. Giltiga Issuer-värden: ' {0} '. Ange en annan token och försök igen. |
| `AADB2C90239` | Den angivna id_token misslyckades med att verifiera signaturen. Ange en annan token och försök igen. |
| `AADB2C90240` | Den tillhandahållna id_token är felaktig och kunde inte parsas. Ange en annan token och försök igen. |
| `AADB2C90242` | Den tekniska SAML-profilen {0} anger PARTNERENTITY CDATA som inte kan läsas in av skäl {1} . |
| `AADB2C90243` | IDP-klient nyckeln/hemligheten har inte kon figurer ATS korrekt. |
| `AADB2C90244` | Det finns för många begär Anden just nu. Vänta en stund och försök igen. |
| `AADB2C90248` | Resurs ägar flödet kan bara användas av program som skapats via B2C-administrations portalen. |
| `AADB2C90250` | Den generiska inloggnings slut punkten stöds inte. |
| `AADB2C90255` | Det angivna anspråks utbytet i den tekniska profilen {0} slutfördes inte som förväntat. Du kanske vill försöka starta-sessionen från början. |
| `AADB2C90261` | Anspråks utbytet {0} som anges i steg {1} returnerade http-felsvar som inte kunde parsas. |
| `AADB2C90272` | Parametern id_token_hint har inte angetts i begäran. Ange token och försök igen. |
| `AADB2C90273` | Ett ogiltigt svar togs emot: {0} |
| `AADB2C90274` | Providerns metadata anger ingen enskild utloggnings tjänst eller så är slut punkts bindningen inte en av typen "urn: Oasis: Names: TC: SAML: 2.0: bindings: HTTP-Redirect" eller "urn: Oasis: Names: TC: SAML: 2.0: bindningar: HTTP-POST". |
| `AADB2C90276` | Begäran stämmer inte överens med kontroll inställningen {0} : {1} i technicalProfile {2} för principen ' {3} Tenant ' {4} '. |
| `AADB2C90277` | Orchestration-steget {0} i användar resan {1} för principen {2} innehåller ingen innehålls definitions referens. |
| `AADB2C90279` | Det tillhandahållna klient-ID: t {0} matchar inte det klient-ID som utfärdade tilldelningen. |
| `AADB2C90284` | Programmet med identifieraren {0} har inte beviljats tillstånd och kan inte användas för lokala konton. |
| `AADB2C90285` | Det gick inte att hitta programmet med identifieraren {0} . |
| `AADB2C90288` | UserJourney med ID: t som {0} refereras i TechnicalProfile {1} för uppdatering av token-inlösen för klienten {2} finns inte i principen {3} eller någon av dess grundläggande principer. |
| `AADB2C90289` | Vi påträffade ett fel vid anslutning till identitets leverantören. Försök igen senare. |
| `AADB2C90296` | Programmet har inte kon figurer ATS korrekt. Kontakta administratören för den plats som du försöker få åtkomst till. |
| `AADB2C99005` | Begäran innehåller en ogiltig omfattnings parameter som innehåller ett otillåtet Character {0} . |
| `AADB2C99006` | Azure AD B2C kan inte hitta tillägg-appen med app-ID {0} . https://go.microsoft.com/fwlink/?linkid=851224Mer information finns på. |
| `AADB2C99011` | Värdet för metadata {0} har inte angetts i TechnicalProfile {1} i principen {2} . |
| `AADB2C99013` | Den angivna kombinationen av grant_type [ {0} ] och token_type [ {1} ] stöds inte. |
| `AADB2C99015` | Profilen {0} i principen {1} i klienten {2} saknar alla InputClaims som krävs för flödet för autentiseringsuppgifter för resurs ägar lösen ord. |
