---
title: Azure AD för utvecklare (v 1.0) | Azure
description: 'Lär dig grunderna för autentisering för Azure AD för utvecklare (v 1.0) som app-modellen, API: et, etablering och de vanligaste scenarierna för autentisering.'
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154737"
---
# <a name="what-is-authentication"></a>Vad är autentisering?

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

*Autentisering* innebär att en part måste ange giltiga uppgifter och utgör grunden för att skapa ett säkerhetsobjekt som används för identitets- och åtkomstkontroll. Enklare sagt är det en process där du bevisar att du är den du säger att du är. Autentisering förkortas ibland AuthN.

*Auktorisering* innebär att bevilja ett autentiserat säkerhetsobjekt behörighet att göra något. Det anger vilka du data du får åtkomst till och vad du kan gör med dem. Auktorisering förkortas ibland AuthZ.

Azure Active Directory för utvecklare (v 1.0) (Azure AD) fören klar autentiseringen för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt komma igång med att koda.

Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under ett beviljande flöde för OAuth 2.0-auktorisering – när resursägaren beviljar behörighet för klientprogrammet och tillåter klienten åtkomst till resursägarens resurser.
* Under resursåtkomst av klienten – enligt implementering av resursservern, med anspråksvärden som finns i åtkomsttoken för att fatta beslut om åtkomstkontroll utifrån dem.

## <a name="authentication-basics-in-azure-ad"></a>Grundläggande autentisering i Azure AD

Föreställ dig det enklaste scenariot där det krävs identitet: en användare i en webbläsare behöver autentiseras för ett webbprogram. Följande diagram visar det här scenariot:

![Översikt över inloggning till webbprogram](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Här är vad du behöver veta om de olika komponenterna som visas i diagrammet:

* Azure AD är identitetsprovidern. Identitets leverantören ansvarar för att verifiera identiteten för användare och program som finns i en organisations katalog och utfärdar säkerhetstoken efter lyckad autentisering av dessa användare och program.
* Ett program som vill kunna utföra autentisering av autentisering till Azure AD måste registreras i Azure Active Directory (Azure AD). Azure AD registrerar och identifierar unikt appen i katalogen.
* Utvecklare kan använda Azure AD-autentiseringsbiblioteken med öppen källkod för att göra autentiseringen enkel genom att hantera protokollinformationen åt dig. Mer information finns i Microsoft Identity Platform [v 2.0 Authentication libraries](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) och [v 1.0-autentiseringsinställningar](active-directory-authentication-libraries.md).
* När en användare har autentiserats måste programmet verifiera användarens säkerhetstoken för att säkerställa att autentiseringen lyckades. Du hittar snabbstarter, självstudier och kodexempel på flera olika språk och ramverk som visar vad programmet måste göra.
  * Om du snabbt vill bygga en app och lägga till funktioner som att hämta tokens, uppdatera tokens, logga in en användare, visa användarinformation med mera läser du avsnittet **Snabbstarter** i dokumentationen.
  * Om du vill ha djupgående, scenariobaserade procedurer för vanliga uppgifter för auktoriseringsutveckling som att hämta åtkomsttoken och använda dem i anrop till Microsoft Graph API och andra API:er, implementera inloggning med Microsoft med en traditionell webbläsarbaserad app som använder OpenID Connect, med mera, läser du avsnittet **Självstudier** i dokumentationen.
  * Du kan ladda ned kodexempel på [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Flödet för begäranden och svar för autentiseringsprocessen bestäms av autentiseringsprotokollet som du har använt, till exempel OAuth 2.0, OpenID Connect, WS-Federation eller SAML 2.0. Mer information om protokoll finns i avsnittet **begrepp > Authentication Protocol** i dokumentationen.

I exempelscenariot ovan kan du klassificera apparna enligt dessa två roller:

* Appar som behöver säker åtkomst till resurser
* Appar som har rollen av själva resursen

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiseringsscheman som stöds av Azure AD. Dessa flöden kan skapa en mängd olika tokens (id_tokens, uppdatera tokens, åtkomsttoken) och auktoriseringsregler, och kräver olika token för att de ska fungera. Det här diagrammet innehåller en översikt:

|Flöden | Innebär | id_token | åtkomsttoken | uppdatera token | auktoriseringskod | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Implicit flöde](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v1-oauth2-on-behalf-of-flow.md) | åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md) | | | x (endast app-only)| | |

Token som utfärdas via det implicita läget har en längd begränsning på grund av att de skickas tillbaka till webbläsaren via URL: en (där `response_mode` är `query` eller `fragment` ).  Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång.  Detta innebär att dessa tokens inte har `groups` eller är `wids` anspråk. 

Nu när du har en översikt över grunderna kan du läsa vidare för att förstå identitetens appmodell och API, hur etablering fungerar i Azure AD och länkar till detaljerad information om vanliga scenarier som Azure AD stöder.

## <a name="application-model"></a>Programmodell

Azure AD representerar program enligt en specifik modell som har utformats för att uppfylla två huvudfunktioner:

* **Identifiera appen enligt de autentiseringsprotokoll den stöder** – Detta innebär uppräkning av alla identifierare, URL:er, hemligheter och relaterad information som behövs vid identifieringen. Här gör Azure AD följande:

    * Innehåller alla data som krävs för att stödja autentisering vid körning.
    * Innehåller alla data för att besluta vilka resurser en app kan behöva för åtkomst och om en viss begäran ska uppfyllas och under vilka omständigheter.
    * Tillhandahåller infrastrukturen för implementering av appetableringen i apputvecklarens klientorganisation och i andra Azure AD-klientorganisationer.

* **Hantera användarmedgivande vid tokenbegäran och underlätta dynamisk etablering av appar över klientorganisationer** – här gör Azure AD följande:

    * Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
    * Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Azure AD beskriver ett **programobjekt** ett program som en abstrakt entitet. Utvecklare kan arbeta med program. I utvecklingsarbetet använder Azure AD ett angivet programobjekt som en skiss för att skapa ett **huvudnamn för tjänsten**, som representerar en konkret instans av ett program i en katalog eller klientorganisation. Det är tjänstens huvudnamn som definierar vad appen faktiskt kan göra i en specifik målkatalog, vilka som kan använda den, vilka resurser den har åtkomst till och så vidare. Azure AD skapar ett huvudnamn för tjänsten utifrån ett programobjekt genom **medgivande**.

I följande diagram visas ett förenklat etableringsflöde för Azure AD som drivs av medgivande.  Det finns två klienter (A och B), där klient organisation A äger programmet och klient B instansierar programmet via ett huvud namn för tjänsten.  

![Förenklat etableringsflöde som drivs av medgivande](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B
1. Azure AD använder programobjektet i klient organisation A som en skiss för att skapa ett huvud namn för tjänsten i klient B
1. Användaren får begärd token

Du kan upprepa den här processen så många gånger du vill för andra klientorganisationer (C, D och så vidare). Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra klientorganisationer där appen får medgivande behåller kontrollen över vad programmet tillåts göra genom motsvarande tjänsthuvudobjekt i varje klientorganisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="claims-in-azure-ad-security-tokens"></a>Anspråk i säkerhetstoken för Azure AD

Säkerhetstoken (åtkomst- och ID-token) som utfärdas av Azure AD innehåller anspråk, eller försäkringar för information om subjektet som har autentiserats. Program kan använda anspråk för olika uppgifter, till exempel:

* Validera token
* Identifiera subjektets katalogklientorganisation
* Visa användarinformation
* Fastställa subjektets auktorisering

Anspråk som finns i alla angivna säkerhetstoken beror på typen av token, på typen av autentiseringsuppgift som används för att autentisera användaren och på programkonfigurationen.

En kort beskrivning av varje typ av anspråk som genereras av Azure AD finns i tabellen nedan. Mer detaljerad information [finns i åtkomsttoken och](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) [ID-token](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) som utfärdats av Azure AD.

| Begär | Beskrivning |
| --- | --- |
| Program-ID | Identifierar programmet som använder token. |
| Målgrupp | Identifierar mottagarresursen som token är avsedd för. |
| Application Authentication Context Class Reference | Anger hur klienten har autentiserats (offentlig eller konfidentiell klient). |
| Autentiseringstillfälle | Registrerar datum och tid när autentiseringen inträffade. |
| Autentiseringsmetod | Anger hur subjektet för token har autentiserats (lösenord, certifikat osv.). |
| Förnamn | Innehåller förnamnet på användaren som det anges i Azure AD. |
| Grupper | Innehåller objekt-ID:n för Azure AD-grupper som användaren är medlem i. |
| Identitetsprovider | Registrerar den identitetsprovider som har autentiserat subjektet för token. |
| Utfärdad | Registrerar tiden då token utfärdats, används ofta för tokens uppdateringsbarhet. |
| Utfärdare | Identifierar STS som genererat token samt Azure AD-klientorganisationen. |
| Efternamn | Innehåller efternamnet på användaren som det anges i Azure AD. |
| Name | Innehåller ett läsbart värde som identifierar subjektet för token. |
| Objekt-ID | Innehåller en oföränderlig, unik identifierare för subjektet i Azure AD. |
| Roller | Innehåller egna namn på Azure AD-programroller som användaren har beviljats. |
| Omfång | Anger de behörigheter som beviljats för klientprogrammet. |
| Ämne | Anger huvudnamnet som token kontrollerar information om. |
| Klient-ID:t | Innehåller ett oföränderlig, unik identifierare för katalogklientorganisationen som har utfärdat token. |
| Tokenlivstid | Definierar tidsintervallet då token är giltig. |
| UPN (User Principal Name) | Innehåller UPN för subjektet. |
| Version | Innehåller versionsnumret för token. |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [program typer och scenarier som stöds i Microsoft Identity Platform](app-types.md)
