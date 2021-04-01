---
title: Varför uppdatera till Microsoft Identity Platform (v 2.0) | Azure
description: Känn till skillnaderna mellan Microsoft Identity Platform (v 2.0)-slut punkten och Azure Active Directory (Azure AD) v 1.0-slutpunkten och lär dig fördelarna med att uppdatera till v 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8f6170de65ae5e1ca8ecb5f7cc8a78f4f194ac41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92055298"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Varför uppdatera till Microsoft-identitetsplattformen (v2.0)?

När du utvecklar ett nytt program är det viktigt att känna till skillnaderna mellan Microsoft Identity Platform (v 2.0) och Azure Active Directory (v 1.0) slut punkter. Den här artikeln beskriver de viktigaste skillnaderna mellan slut punkterna och några befintliga begränsningar för Microsoft Identity Platform.

## <a name="who-can-sign-in"></a>Vem kan logga in

![Vem kan logga in med v 1.0-och v 2.0-slut punkter](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* V 1.0-slutpunkten tillåter endast arbets-och skol konton att logga in i ditt program (Azure AD)
* Med Microsoft Identity Platform-slutpunkten kan du logga in med arbets-och skol konton från Azure AD och personliga Microsoft-konton (MSA), till exempel hotmail.com, outlook.com och msn.com.
* Båda slut punkterna accepterar också inloggnings program för *[gäst användare](../external-identities/what-is-b2b.md)* i en Azure AD-katalog för program som kon figurer ATS som *[en enskild klient](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* eller för program *med flera klienter* som kon figurer ATS för att peka på den klient-/regionsspecifika slut punkten ( `https://login.microsoftonline.com/{TenantId_or_Name}` ).

Med slut punkten för Microsoft Identity Platform kan du skriva appar som accepterar inloggningar från personliga Microsoft-konton och arbets-och skol konton. Det ger dig möjlighet att skriva appens fullständigt konto-oberoende. Om din app till exempel anropar [Microsoft Graph](https://graph.microsoft.io), kommer vissa ytterligare funktioner och data att vara tillgängliga för arbets konton, till exempel SharePoint-webbplatser eller katalog data. Men för många åtgärder, till exempel [läsning av en användares e-post](/graph/api/user-list-messages), kan samma kod komma åt e-postmeddelandet för både personliga konton och arbets-och skol konton.

För Microsoft Identity Platform-slutpunkten kan du använda Microsoft Authentication Library (MSAL) för att få till gång till konsument-, utbildnings-och företags världar. Azure AD v 1.0-slutpunkten accepterar bara inloggningar från arbets-och skol konton.

## <a name="incremental-and-dynamic-consent"></a>Det stegvisa och dynamiska godkännandet

Appar som använder Azure AD v 1.0-slutpunkten krävs för att ange deras obligatoriska OAuth 2,0-behörigheter i förväg, till exempel:

![Exempel som visar användar gränssnittet för behörighets registrering](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

Behörigheterna som anges direkt på program registreringen är **statiska**. Även om statiska behörigheter för appen som definierats i Azure Portal låter koden vara Nice och enkel, visar den några möjliga problem för utvecklare:

* Appen måste begära alla behörigheter som den skulle behöva vid användarens första inloggning. Detta kan leda till en lång lista med behörigheter som förhindrar att slutanvändarna godkänner appens åtkomst vid första inloggningen.

* Appen måste känna till alla resurser som den skulle ha haft till gång till i förväg. Det var svårt att skapa appar som kunde komma åt ett godtyckligt antal resurser.

Med Microsoft Identity Platform-slutpunkten kan du ignorera de statiska behörigheter som definierats i appens registrerings information i Azure Portal och begär behörigheter stegvis i stället, vilket innebär att du ombeds att ange en minimal uppsättning behörigheter som är i drift fram och växer mer över tid eftersom kunden använder ytterligare AppData. Om du vill göra det kan du ange de omfattningar som appen behöver när som helst genom att inkludera de nya omfången i `scope` parametern när du begär en åtkomsttoken – utan att behöva definiera dem i program registrerings informationen. Om användaren ännu inte har samtyckt till nya omfattningar som lagts till i begäran, uppmanas de bara att godkänna de nya behörigheterna. Läs mer i [behörigheter, medgivande och omfång](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Genom att tillåta en app att begära behörigheter dynamiskt via `scope` parametern ger utvecklare fullständig kontroll över din användar upplevelse. Du kan också läsa in din medgivande upplevelse och fråga efter alla behörigheter i en första auktoriseringsbegäran-begäran. Om din app kräver ett stort antal behörigheter kan du samla in dessa behörigheter från användaren stegvis när de försöker använda vissa funktioner i appen över tid.

Administratörs medgivande som gjorts för en organisations räkning kräver fortfarande de statiska behörigheter som registrerats för appen, så du bör ange dessa behörigheter för appar i appens registrerings portal om du behöver en administratör för att ge tillåtelse åt hela organisationen. Detta minskar de cykler som krävs av organisationens administratör för att konfigurera programmet.

## <a name="scopes-not-resources"></a>Omfattningar, inte resurser

För appar som använder v 1.0-slutpunkten kan en app beter sig som en **resurs** eller en mottagare av tokens. En resurs kan definiera ett antal **omfattningar** eller **oAuth2Permissions** som den förstår, vilket gör det möjligt för klient program att begära token från den resursen för en viss uppsättning omfång. Överväg Microsoft Graph-API: et som ett exempel på en resurs:

* Resurs-ID eller `AppID URI` : `https://graph.microsoft.com/`
* Omfattningar, eller `oAuth2Permissions` : `Directory.Read` , `Directory.Write` , och så vidare.

Detta gäller för Microsoft Identity Platform-slutpunkten. En app kan fortfarande fungera som en resurs, definiera omfång och identifieras av en URI. Klient program kan fortfarande begära åtkomst till dessa scope. Men det kan vara så att en klient begär dessa behörigheter.

För den v 1.0-slutpunkten kan en OAuth 2,0-auktorisera begäran till Azure AD ha tittat på:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Här indikerade **resurs** parametern vilken resurs som klient appen begär auktorisering för. Azure AD beräknade de behörigheter som krävs av appen baserat på statisk konfiguration i Azure Portal och utfärdade tokens.

För program som använder Microsoft Identity Platform-slutpunkten ser samma OAuth 2,0-auktoriserade begäran ut så här:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Här anger **omfattnings** parametern vilken resurs och vilka behörigheter appen begär auktorisering. Den önskade resursen finns fortfarande i begäran – den ingår i värdena för omfattnings parametern. Genom att använda omfattnings parametern på det här sättet kan Microsoft Identity Platform-slutpunkten bli mer kompatibel med OAuth 2,0-specifikationen och anpassa sig närmare med vanliga bransch praxis. Det gör det också möjligt för appar att göra ett [stegvist godkännande](#incremental-and-dynamic-consent) – endast begära behörighet när programmet kräver dem i stället för att placeras överst.

## <a name="well-known-scopes"></a>Välkända omfattningar

### <a name="offline-access"></a>Åtkomst offline

Appar som använder Microsoft Identity Platform-slutpunkten kan kräva att en ny välkänd behörighet för appar- `offline_access` omfånget används. Alla appar måste begära den här behörigheten om de behöver åtkomst till resurser för en användares räkning under en längre tids period, även om användaren inte kan använda appen aktivt. `offline_access`Omfånget visas för användaren i medgivande dialog rutor som **åtkomst till dina data när som helst**, vilka användaren måste godkänna. Om du begär `offline_access` behörigheten kommer din webbapp att kunna ta emot OAuth 2,0-refresh_tokens från slut punkten för Microsoft Identity Platform. Uppdaterade token är långa och kan bytas ut för nya OAuth 2,0-åtkomsttoken för utökade åtkomst perioder.

Om din app inte begär `offline_access` omfånget får den inte ta emot uppdateringstoken. Det innebär att när du löser in en auktoriseringskod i OAuth 2,0-Authorization Code Flow får du bara tillbaka en åtkomsttoken från `/token` slut punkten. Denna åtkomsttoken är fortfarande giltig under en kort tids period (vanligt vis en timme), men kommer att upphöra att gälla. Vid den tidpunkten måste din app omdirigera användaren tillbaka till `/authorize` slut punkten för att hämta en ny auktoriseringskod. Under den här omdirigeringen kanske användaren inte behöver ange sina autentiseringsuppgifter igen eller medgivande till behörigheter, beroende på typ av app.

Om du vill veta mer om OAuth 2,0, `refresh_tokens` och `access_tokens` kolla in [Microsoft Identity Platform Protocol-referens](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profil och e-post

Tidigare var det mest grundläggande OpenID Connect-inloggnings flödet med Microsoft Identity Platform en mycket information om användaren i den resulterande *id_token*. Anspråk i en id_token kan innehålla användarens namn, önskat användar namn, e-postadress, objekt-ID med mera.

Den information som `openid` omfånget ger åtkomst till appen är nu begränsad. `openid`Omfånget tillåter bara att din app loggar in användaren och tar emot en app-Specific Identifier för användaren. Om du vill hämta personliga data om användaren i din app måste appen begära ytterligare behörigheter från användaren. Två nya omfattningar och ger `email` `profile` dig möjlighet att begära ytterligare behörigheter.

* Med hjälp av `email` omfånget får appen åtkomst till användarens primära e-postadress via `email` anspråket i id_token, förutsatt att användaren har en adresserad e-postadress.
* `profile`Omfånget ger appen åtkomst till all annan grundläggande information om användaren, till exempel namn, önskat användar namn, objekt-ID och så vidare i id_token.

Med de här omfången kan du koda din app på ett minimalt sätt så att du bara kan fråga användaren om den information som appen behöver för att utföra sitt jobb. Mer information om de här omfattningarna finns i [referens för Microsoft Identity Platform](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Token-anspråk

Slut punkten för Microsoft Identity Platform utfärdar en mindre uppsättning anspråk i sina tokens som standard för att bevara nytto laster små. Om du har appar och tjänster som har ett beroende på ett visst anspråk i en v 1.0-token som inte längre anges som standard i en Microsoft Identity Platform-token, bör du överväga att använda funktionen [valfria anspråk](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att ta med detta anspråk.

> [!IMPORTANT]
> v 1.0-och v 2.0-token kan utfärdas av både v 1.0-och v 2.0-slutpunkter! id_tokens *alltid* matcha den slut punkt som de begär från, och åtkomsttoken matchar *alltid* det format som förväntas av webb-API: et som klienten anropar med denna token.  Så om din app använder v 2.0-slutpunkten för att hämta en token för att anropa Microsoft Graph, vilket förväntar sig ett token för v 1.0-format, får appen en token i formatet v 1.0.

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar som du bör känna till när du använder Microsoft Identity Platform.

När du skapar program som integreras med Microsoft Identity Platform måste du bestämma om slut punkten och autentiseringsprotokollen för Microsoft Identity Platform uppfyller dina behov. Slut punkten och plattformen för v 1.0 stöds fortfarande fullt ut, och i vissa hänseende är fler funktioner mer avancerade än Microsoft Identity Platform. Microsoft Identity Platform [introducerar emellertid betydande fördelar](azure-ad-endpoint-comparison.md) för utvecklare.

Här är en förenklad rekommendation för utvecklare nu:

* Om du vill ha eller behöver stöd för personliga Microsoft-konton i ditt program, eller om du skriver ett nytt program, använder du Microsoft Identity Platform. Men innan du gör det, se till att du förstår de begränsningar som beskrivs i den här artikeln.
* Om du migrerar eller uppdaterar ett program som förlitar sig på SAML kan du inte använda Microsoft Identity Platform. Se i stället till [Azure AD v 1.0-guiden](v1-overview.md).

Slut punkten för Microsoft Identity Platform kommer att utvecklas för att eliminera de begränsningar som anges här, så att du bara behöver använda Microsoft Identity Platform-slutpunkten. Under tiden kan du använda den här artikeln för att avgöra om slut punkten för Microsoft Identity Platform är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla den aktuella statusen för Microsoft Identity Platform-slutpunkten. Kom tillbaka för att utvärdera dina krav mot Microsoft Identity Platform-funktioner.

### <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar

För varje app som du vill integrera med Microsoft Identity Platform-slutpunkten kan du skapa en app-registrering i den nya [ **Appregistreringars** miljön](https://aka.ms/appregistrations) i Azure Portal. Befintliga Microsoft-konto appar är inte kompatibla med portalen, men alla Azure AD-appar är, oavsett var eller när de registrerades.

Appregistreringar som har stöd för arbets-och skol konton och personliga konton har följande varningar:

* Endast två program hemligheter tillåts per program-ID.
* Ett program som inte har registrerats i en klient organisation kan bara hanteras av det konto som registrerade det. Den kan inte delas med andra utvecklare. Detta är fallet för de flesta appar som registrerades med en personlig Microsoft-konto i registrerings portalen för appen. Om du vill dela din app-registrering med flera utvecklare registrerar du programmet i en klient med hjälp av den nya **Appregistreringar** avsnittet av Azure Portal.
* Det finns flera begränsningar för formatet för den omdirigerings-URL som tillåts. Mer information om omdirigerings-URL finns i nästa avsnitt.

### <a name="restrictions-on-redirect-urls"></a>Begränsningar för omdirigerings-URL: er

Den senaste informationen om begränsningar för omdirigerings-URL: er för appar som har registrerats för Microsoft Identity Platform finns i [OMDIRIGERA URI/svars-URL begränsningar och begränsningar](../develop/reply-url.md) i dokumentationen för Microsoft Identity Platform.

Information om hur du registrerar en app för användning med Microsoft Identity Platform finns i [Registrera en app med hjälp av den nya Appregistreringar-upplevelsen](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK: er

Biblioteks stöd för Microsoft Identity Platform-slutpunkten är för närvarande begränsad. Om du vill använda Microsoft Identity Platform-slutpunkten i ett produktions program har du följande alternativ:

* Om du skapar ett webb program kan du på ett säkert sätt använda den allmänt tillgängliga mellanliggande server sidan för att utföra inloggning och verifiering av token. Detta inkluderar OWIN OpenID Connect-programprogram för ASP.NET och plugin-programmet för Node.js Passport. Kod exempel som använder Microsoft mellanprogram finns i avsnittet [komma igång med Microsoft Identity Platform](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) .
* Om du skapar ett skriv bord eller ett mobilt program kan du använda något av Microsoft-MSAL (Authentication Libraries). Dessa bibliotek är allmänt tillgängliga eller i en för hands version som stöds för produktion, så det är säkert att använda dem i produktions program. Du kan läsa mer om villkoren i för hands versionen och de tillgängliga biblioteken i [autentiserings biblioteks referensen](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* För plattformar som inte omfattas av Microsoft-bibliotek kan du integrera med Microsoft Identity Platform-slutpunkten genom att skicka och ta emot protokoll meddelanden direkt i program koden. OpenID Connect-och OAuth-protokollen [är uttryckligen dokumenterade](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för att hjälpa dig att göra en sådan integrering.
* Slutligen kan du använda OpenID Connect och OAuth-bibliotek med öppen källkod för att integrera med Microsoft Identity Platform-slutpunkten. Microsoft Identity Platform-slutpunkten bör vara kompatibel med många protokoll bibliotek med öppen källkod utan ändringar. Tillgängligheten för de här typerna av bibliotek varierar beroende på språk och plattform. [OpenID Connect](https://openid.net/connect/) och [OAuth 2,0](https://oauth.net/2/) -webbplatser har en lista över populära implementeringar. Mer information finns i [Microsoft Identity Platform and Authentication libraries](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)och i listan över klient bibliotek med öppen källkod och exempel som har testats med Microsoft Identity Platform-slutpunkten.
* För referens `.well-known` är slut punkten för den vanliga slut punkten för Microsoft Identity Platform `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Ersätt `common` med klient-ID: t för att hämta data som är speciella för din klient.

### <a name="protocol-changes"></a>Protokoll ändringar

Slut punkten för Microsoft Identity Platform stöder inte SAML eller WS-Federation; den stöder bara OpenID Connect och OAuth 2,0.  De viktiga ändringarna i OAuth 2,0-protokollen från v 1.0-slut punkten är:

* `email`Anspråket returneras om ett valfritt anspråk har kon figurer ATS **eller** om scope = email angavs i begäran.
* `scope`Parametern stöds nu i stället för `resource` parametern.
* Många svar har ändrats för att göra dem mer kompatibla med OAuth 2,0-specifikationen, till exempel korrekt returneras `expires_in` som en int-sträng i stället för en sträng.

För att bättre förstå omfattningen av de protokoll funktioner som stöds i Microsoft Identity Platform-slutpunkten, se [OpenID Connect och OAuth 2,0-protokoll referens](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-usage"></a>SAML-användning

Om du har använt Active Directory-autentiseringsbibliotek (ADAL) i Windows-program kan du ha nytta av Windows-integrerad autentisering, som använder den beviljade SAML-kontrollen (Security Assertion Markup Language). Med det här bidraget kan användare av federerade Azure AD-klienter obevakat autentisera med sina lokala Active Directory-instanser utan att ange autentiseringsuppgifter. Även om [SAML fortfarande är ett protokoll som stöds](../develop/active-directory-saml-protocol-reference.md) för användning med företags användare, är v 2.0-slutpunkten endast för användning med OAuth 2,0-program.

## <a name="next-steps"></a>Nästa steg

Läs mer i [dokumentationen för Microsoft Identity Platform](../develop/index.yml).
