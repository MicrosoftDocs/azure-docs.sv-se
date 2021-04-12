---
title: Vägledning för utvecklare för Azure Active Directory villkorlig åtkomst
titleSuffix: Microsoft identity platform
description: Vägledning för utvecklare och scenarier för villkorlig åtkomst och Microsoft Identity Platform i Azure AD.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: ed8007c81479c73e4503d74af4c4043e503baf2b
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120154"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Vägledning för utvecklare för Azure Active Directory villkorlig åtkomst

Funktionen för villkorlig åtkomst i Azure Active Directory (Azure AD) erbjuder ett av flera sätt som du kan använda för att skydda din app och skydda en tjänst. Med villkorlig åtkomst kan utvecklare och företags kunder skydda tjänster på flera olika sätt:

* [Multifaktorautentisering](../authentication/concept-mfa-howitworks.md)
* Tillåter endast att Intune-registrerade enheter får åtkomst till vissa tjänster
* Begränsa användar platser och IP-intervall

Mer information om alla funktioner för villkorlig åtkomst finns i artikeln [Vad är villkorlig åtkomst](../conditional-access/overview.md).

För utvecklare som skapar appar för Azure AD visar den här artikeln hur du kan använda villkorlig åtkomst och du får också lära dig om effekten av åtkomst till resurser som du inte har kontroll över som kan ha villkorliga åtkomst principer tillämpade. Artikeln utforskar också konsekvenserna av villkorlig åtkomst i Flow-, Web Apps-och Access-Microsoft Graph och anropar API: er.

Kunskaper om appar för [en](quickstart-register-app.md) och [flera innehavare](howto-convert-app-to-be-multi-tenant.md) och [vanliga autentiserings mönster](./authentication-vs-authorization.md) antas.

> [!NOTE]
> Om du använder den här funktionen krävs en licens för Azure AD Premium P1. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).
> Kunder med [Microsoft 365 Business licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst.

## <a name="how-does-conditional-access-impact-an-app"></a>Hur påverkar villkorlig åtkomst en app?

### <a name="app-types-impacted"></a>Program typer som påverkas

I de flesta vanliga fall ändrar inte villkorlig åtkomst appens beteende eller kräver ändringar från utvecklaren. I vissa fall när en app indirekt eller tyst begär en token för en tjänst, kräver en app kod ändringar för att hantera utmaningarna för villkorlig åtkomst. Det kan vara så enkelt som att utföra en interaktiv inloggnings förfrågan.

Mer specifikt kräver följande scenarier kod för att hantera utmaningarna för villkorlig åtkomst:

* Appar som utför ett flöde på samma vägnar
* Appar som har åtkomst till flera tjänster/resurser
* Appar med en sida som använder MSAL.js
* Web Apps att anropa en resurs

Principer för villkorlig åtkomst kan tillämpas på appen, men kan också tillämpas på ett webb-API som appen får åtkomst till. Mer information om hur du konfigurerar en princip för villkorlig åtkomst finns i [snabb start: KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md).

Beroende på scenariot kan en företags kund tillämpa och ta bort principer för villkorlig åtkomst när som helst. Implementera utmanings hantering för att din app ska fortsätta att fungera när en ny princip tillämpas. I följande exempel visas utmanings hantering.

### <a name="conditional-access-examples"></a>Exempel på villkorlig åtkomst

Vissa scenarier kräver kod ändringar för att hantera villkorlig åtkomst medan andra fungerar som de är. Här följer några scenarier som använder villkorlig åtkomst för att utföra Multi-Factor Authentication som ger viss insikt i skillnaden.

* Du skapar en iOS-app med en enda klient och tillämpar en princip för villkorlig åtkomst. Appen loggar in en användare och begär inte åtkomst till ett API. När användaren loggar in anropas principen automatiskt och användaren måste utföra Multi-Factor Authentication (MFA).
* Du skapar en inbyggd app som använder en mellan nivå tjänst för att få åtkomst till ett underordnat API. En företags kund på företaget som använder den här appen tillämpar en princip på det underordnade API: et. När en användare loggar in begär den interna appen åtkomst till mellan nivån och skickar token. Mellan nivån utför flöde på egen räkning för att begära åtkomst till det underordnade API: et. I det här läget presenteras en anspråks utmaning för mellan nivån. Mellan nivån skickar utmaningen tillbaka till den inbyggda appen, som måste följa principen för villkorlig åtkomst.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph har särskilda överväganden när du skapar appar i miljöer med villkorlig åtkomst. I allmänhet har Mechanics för villkorlig åtkomst samma, men principerna som användarna ser baseras på underliggande data som din app begär från grafen.

Mer specifikt representerar alla Microsoft Graph omfattningarna en viss data uppsättning som individuellt kan tillämpa principer. Eftersom principer för villkorlig åtkomst tilldelas till de särskilda data uppsättningarna, tillämpar Azure AD principer för villkorlig åtkomst baserat på data bakom grafen – i stället för själva grafen.

Om en app till exempel begär följande Microsoft Graph omfattningar,

```
scopes="ChannelMessages.Read.All Mail.Read"
```

En app kan förvänta sina användare att uppfylla alla principer som angetts för team och Exchange. Vissa omfång kan mappas till flera data uppsättningar om den beviljar åtkomst.

### <a name="complying-with-a-conditional-access-policy"></a>Följa en princip för villkorlig åtkomst

För flera olika topologier för appar utvärderas en princip för villkorlig åtkomst när sessionen upprättas. När en princip för villkorlig åtkomst fungerar på kornig het för appar och tjänster, beror den punkt där den anropas i stor utsträckning på det scenario du försöker utföra.

När din app försöker få åtkomst till en tjänst med en princip för villkorlig åtkomst kan det uppstå en utmaning för villkorlig åtkomst. Denna utmaning är kodad i den `claims` parameter som ingår i ett svar från Azure AD. Här är ett exempel på den här anrops parametern:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Utvecklare kan ta denna utmaning och lägga till den på en ny begäran till Azure AD. Om du skickar det här läget ombeds slutanvändaren utföra alla åtgärder som krävs för att följa principen för villkorlig åtkomst. I följande scenarier beskrivs information om felet och hur du extraherar parametern.

## <a name="scenarios"></a>Scenarier

### <a name="prerequisites"></a>Förutsättningar

Villkorlig åtkomst i Azure AD är en funktion som ingår i [Azure AD Premium](../fundamentals/active-directory-whatis.md). Kunder med [Microsoft 365 Business licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst.

### <a name="considerations-for-specific-scenarios"></a>Överväganden för vissa scenarier

Följande information gäller endast i följande scenarier för villkorlig åtkomst:

* Appar som utför ett flöde på samma vägnar
* Appar som har åtkomst till flera tjänster/resurser
* Appar med en sida som använder MSAL.js

I följande avsnitt beskrivs vanliga scenarier som är mer komplexa. Kärn drifts principen är villkorliga åtkomst principer utvärderas när token begärs för tjänsten som har en tillämpad princip för villkorlig åtkomst.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: app som presterar på uppdrag av Flow

I det här scenariot går vi igenom det fall där en inbyggd app anropar en webb tjänst/API. I sin tur utför tjänsten "on-of-of"-flödet för att anropa en underordnad tjänst. I vårt fall har vi tillämpat vår princip för villkorlig åtkomst till den underordnade tjänsten (webb-API 2) och använder en inbyggd app i stället för en server/daemon-app.

![App som utför Flow-diagrammet på uppdrag](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Den inledande token-begäran för webb-API 1 efterfrågar inte slutanvändaren för Multi-Factor Authentication eftersom webb-API 1 inte alltid når det underordnade API: t. När Web API 1 försöker begära en token för användaren för webb-API 2, Miss lyckas begäran eftersom användaren inte har loggat in med Multi-Factor Authentication.

Azure AD returnerar ett HTTP-svar med några intressanta data:

> [!NOTE]
> I den här instansen är det en beskrivning av Multi-Factor Authentication-fel, men det finns ett stort antal `interaction_required` möjliga som rör villkorlig åtkomst.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

I webb-API 1 fångar vi felet `error=interaction_required` och skickar tillbaka `claims` utmaningen till Skriv bords appen. I det här läget kan Desktop-appen skapa ett nytt `acquireToken()` samtal och lägga till `claims` utmaningen som en extra frågesträngparametern. Den här nya begäran kräver att användaren utför Multi-Factor Authentication och skickar sedan den nya token tillbaka till webb-API 1 och slutför flödet på uppdrag av.

För att testa det här scenariot, se vårt [exempel på .NET-kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-). Den visar hur du skickar tillbaka anspråks utmaningarna från webb-API 1 till den interna appen och skapar en ny begäran i klient programmet.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: appen använder flera tjänster

I det här scenariot går vi igenom det fall då en webbapp får åtkomst till två tjänster som har en tilldelad princip för villkorlig åtkomst. Beroende på din program logik kan det finnas en sökväg där appen inte behöver åtkomst till båda webb tjänsterna. I det här scenariot spelar den ordning i vilken du begär en token en viktig roll i slut användar upplevelsen.

Vi antar att vi har webb tjänsten A och B och att webb tjänsten B har samma princip för villkorlig åtkomst som tillämpas. Även om den inledande interaktiva auth-begäran kräver godkännande för båda tjänsterna, krävs inte principen för villkorlig åtkomst i samtliga fall. Om appen begär en token för Web Service B, anropas principen och efterföljande förfrågningar för webb tjänsten A följer också.

![App-åtkomst till flödes diagram för flera tjänster](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternativt, om appen ursprungligen begär en token för webb tjänst A, så anropar inte slutanvändaren principen för villkorlig åtkomst. Detta gör att appens utvecklare kan styra slutanvändarens upplevelse och inte tvinga den villkorliga åtkomst principen att anropas i samtliga fall. Väskan är om appen senare begär en token för webb tjänst B. I det här läget måste slutanvändaren följa principen för villkorlig åtkomst. När appen försöker till kan `acquireToken` den generera följande fel (illustreras i följande diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App som använder flera tjänster som begär en ny token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Om appen använder MSAL-biblioteket, görs ett försök att hämta token alltid interaktivt. När den här interaktiva begäran sker har slutanvändaren möjlighet att följa den villkorliga åtkomsten. Detta är sant om begäran inte är en `AcquireTokenSilentAsync` eller `PromptBehavior.Never` i vilket fall appen behöver utföra en interaktiv ```AcquireToken``` begäran för att ge slutanvändaren möjlighet att följa principen.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Scenario: en app med en sida (SPA) med MSAL.js

I det här scenariot går vi igenom fallet när vi har en enda sida-app (SPA) som använder MSAL.js för att anropa ett skyddat webb-API för villkorlig åtkomst. Det här är en enkel arkitektur men har vissa olika delarna som måste beaktas när du utvecklar kring villkorlig åtkomst.

I MSAL.js finns det några funktioner som hämtar token: `loginPopup()` , `acquireTokenSilent(...)` , `acquireTokenPopup(…)` och `acquireTokenRedirect(…)` .

* `loginPopup()` hämtar en ID-token via en interaktiv inloggnings förfrågan men får inte åtkomst-token för någon tjänst (inklusive ett skyddat webb-API för villkorlig åtkomst).
* `acquireTokenSilent(…)` kan sedan användas för att tyst erhålla en åtkomsttoken, vilket innebär att den inte visar gränssnitt i någon omständighet.
* `acquireTokenPopup(…)` och `acquireTokenRedirect(…)` båda används för att interaktivt begära en token för en resurs, vilket innebär att de alltid visar inloggnings gränssnittet.

När en app behöver en åtkomsttoken för att anropa ett webb-API, försöker den med `acquireTokenSilent(…)` . Om token-sessionen har upphört att gälla eller om vi måste följa en princip för villkorlig åtkomst, Miss lyckas *acquireToken* -funktionen och appen använder `acquireTokenPopup()` eller `acquireTokenRedirect()` .

![En app med en sida med MSAL Flow-diagram](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Låt oss gå igenom ett exempel med vårt scenario för villkorlig åtkomst. Slutanvändaren precis landats på platsen och har ingen session. Vi utför ett `loginPopup()` anrop, hämtar en ID-token utan Multi-Factor Authentication. Sedan träffar användaren en knapp som kräver att appen begär data från ett webb-API. Appen försöker utföra ett anrop, `acquireTokenSilent()` men det Miss lyckas eftersom användaren inte har utfört Multi-Factor Authentication än och måste följa principen för villkorlig åtkomst.

Azure AD skickar tillbaka följande HTTP-svar:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Vår app behöver fånga in `error=interaction_required` . Programmet kan sedan använda antingen `acquireTokenPopup()` eller `acquireTokenRedirect()` på samma resurs. Användaren tvingas göra en Multi-Factor Authentication. När användaren har slutfört Multi-Factor Authentication utfärdas appen en ny åtkomsttoken för den begärda resursen.

Om du vill testa det här scenariot, se vår [JS-kod exempel för JS på egen räkning](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md). I det här kod exemplet används en princip för villkorlig åtkomst och webb-API som du registrerade tidigare med en JS-SPA för att demonstrera det här scenariot. Det visar hur du kan hantera anspråks utmaningen och få en åtkomsttoken som kan användas för ditt webb-API. Du kan också checka in exemplet på allmänt [Angular.jss kod](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) för vägledning om ett vinkel Spa

## <a name="see-also"></a>Se även

* Mer information om funktionerna finns [i villkorlig åtkomst i Azure Active Directory](../conditional-access/overview.md).
* Fler kod exempel för Azure AD finns i [exempel](sample-v2-code.md).
* Mer information om MSAL SDK: er och åtkomst till referens dokumentationen finns i [Översikt över Microsoft Authentication Library](msal-overview.md).
* Mer information om scenarier med flera innehavare finns i [så här loggar du in användare med hjälp av mönster för flera innehavare](howto-convert-app-to-be-multi-tenant.md).
* Lär dig mer om [villkorlig åtkomst och att skydda åtkomsten till IoT-appar](/azure/architecture/example-scenario/iot-aad/iot-aad).
