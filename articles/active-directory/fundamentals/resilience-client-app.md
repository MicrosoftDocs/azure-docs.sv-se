---
title: Öka återhämtningen av autentisering och auktorisering i klient program som du utvecklar
titleSuffix: Microsoft identity platform
description: Vägledning för ökad återhämtning av autentisering och auktorisering i klient program med hjälp av Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: fc15176318dcfae99434f50a0b4370f371cec05a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938247"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Öka återhämtningen av autentisering och auktorisering i klient program som du utvecklar

Det här avsnittet innehåller vägledning om hur du skapar återhämtning till klient program som använder Microsoft Identity Platform och Azure Active Directory för att logga in användare och utföra åtgärder åt dessa användare.

## <a name="use-the-microsoft-authentication-library-msal"></a>Använda Microsoft Authentication Library (MSAL)

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) är en viktig del av [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop). Det fören klar och hanterar hämtning, hantering, cachelagring och uppdatering av tokens och använder bästa praxis för återhämtning. MSAL är utformat för att möjliggöra en säker lösning utan att utvecklare behöver bekymra dig om implementerings informationen.

MSAL cachelagrar tokens och använder ett mönster för tyst token-hämtning. Den serialiserar också automatiskt token-cachen på plattformar som internt tillhandahåller säker lagring som Windows UWP, iOS och Android. Utvecklare kan anpassa serialiseringen när de använder [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.net](https://docs.microsoft.com/azure/active-directory/develop/msal-net-token-cache-serialization), [MSAL för Java](https://docs.microsoft.com/azure/active-directory/develop/msal-java-token-cache-serialization)och [MSAL för python](https://docs.microsoft.com/azure/active-directory/develop/msal-python-token-cache-serialization).

![Bild av enhet med och program som använder MSAL för att anropa Microsoft Identity](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

När du använder MSAL stöds cachelagring av token, uppdatering och tyst hämtning automatiskt. Du kan använda enkla mönster för att hämta de tokens som krävs för modern autentisering. Vi har stöd för många språk och du hittar ett exempel som stämmer överens med ditt språk och din situation på vår [exempel](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) sida.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Java](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL kan i vissa fall proaktivt uppdatera tokens. När Microsoft Identity utfärdar en token för lång livs längd kan den skicka information till klienten för att den optimala tiden ska uppdatera token ("uppdatera \_ i"). MSAL kommer proaktivt uppdatera token baserat på den här informationen. Appen fortsätter att köras medan den gamla token är giltig men har en längre tids period då en annan lyckad token-hämtning skulle göras.

### <a name="stay-up-to-date"></a>Håll dig uppdaterad

Utvecklare bör ha en process för att uppdatera till den senaste versionen av MSAL. Autentisering är en del av appens säkerhet och din app måste vara aktuell med de säkerhets förbättringar som finns i nya MSAL-versioner. Detta är vanligt vis bra för bibliotek under kontinuerlig utveckling och vi ser till att du har den senaste uppdaterade koden vad gäller app-återhämtning. Eftersom Microsoft Identity fortsätter att utveckla på olika sätt för program att bli mer elastiska är appar som använder de senaste MSAL de mest beredda att bygga på dessa innovationer.

[Läs den senaste MSAL.js versionen och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Kontrol lera den senaste versionen av MSAL .NET och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Kontrol lera den senaste versionen av MSAL python och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Kontrol lera den senaste versionen av MSAL Java och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Kontrol lera den senaste versionen av MSAL iOS och macOS och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Kontrol lera den senaste versionen av MSAL Android och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Kontrol lera den senaste MSALa vinkel versionen och viktig information](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Kontrol lera den senaste versionen av Microsoft. Identity. Web och viktig information](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Använda elastiska mönster för token-hantering

Om du inte använder MSAL kan du använda dessa elastiska mönster för hantering av token. Dessa bästa metoder implementeras automatiskt av MSAL-biblioteket. 

I allmänhet anropar ett program som använder modern autentisering en slut punkt för att hämta tokens som autentiserar användaren eller ger programmet tillåtelse att anropa skyddade API: er. MSAL är avsett att hantera information om autentisering och implementerar flera mönster för att förbättra återhämtningen av den här processen. Använd rikt linjerna i det här avsnittet för att implementera bästa praxis om du väljer att använda ett annat bibliotek än MSAL. Om du använder MSAL får du alla dessa metod tips kostnads fritt, eftersom MSAL implementerar dem automatiskt.

### <a name="cache-tokens"></a>Cache-token

Appar ska korrekt cachelagra token som tas emot från Microsoft Identity. När din app tar emot token innehåller det HTTP-svar som innehåller tokens även en "expires_in"-egenskap som talar om för programmet hur lång tid det tar att cachelagra och återanvända token. I det viktigt att programmen använder egenskapen "expires_in" för att fastställa livs längd för token. Programmet får aldrig försöka avkoda en API-åtkomsttoken.

![Ett program som gör ett anrop till Microsoft Identity, men anropet går via en token-cache på enheten som kör programmet](media/resilience-client-app/token-cache.png)

Att använda den cachelagrade token förhindrar onödig trafik mellan appen och Microsoft-identiteten och gör att appen är mindre mottaglig för att hämta problem med att hämta token genom att minska antalet token för hämtning av token. Cachelagrade token förbättrar också programmets prestanda eftersom appen behöver blockeras på att förvärva token mindre. Din användare kan vara inloggad på ditt program för längden på den tokens livs längd.

### <a name="serialize-and-persist-tokens"></a>Serialisera och behåll token

Appar bör på ett säkert sätt serialisera deras token-cache för att bevara tokens mellan instanser av appen. Token kan återanvändas så länge de ligger inom en giltig livs längd. [Uppdatering av tokens](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)och, allt fler åtkomsttoken [, utfärdas](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)i flera timmar. Den här giltiga tiden kan omfatta en användare som startar ditt program många gånger. När appen startar bör den kontrol lera om det finns en giltig åtkomst-eller uppdateringstoken som kan användas. Detta ökar appens återhämtnings förmåga och prestanda eftersom det undviker onödiga anrop till Microsoft-identitet.

![Ett program som gör ett anrop till Microsoft Identity, men anropet går via ett token-cacheminne och ett token Store på enheten som kör programmet](media/resilience-client-app/token-store.png)

Lagring med beständig token bör vara åtkomst kontrollerad och krypterad till ägande användare eller process identitet. På plattformar som Mobile, Windows och Mac bör utvecklaren dra nytta av inbyggda funktioner för att lagra autentiseringsuppgifter.

### <a name="acquire-tokens-silently"></a>Hämta token tyst

Processen för att autentisera en användare eller hämta auktorisering för att anropa ett API kan kräva flera steg i Microsoft-identitet. Till exempel när användaren loggar in för första gången kan de behöva ange autentiseringsuppgifter och utföra Multi-Factor Authentication via ett textmeddelande. Varje steg lägger till ett beroende på den resurs som tillhandahåller tjänsten. Den bästa upplevelsen för användaren och den som har minst beroenden är att försöka hämta en token tyst för att undvika dessa extra steg innan du begär användar interaktion.

![Diagram som visar de olika tjänsterna i Microsoft Identity som kan behöva köras för att slutföra processen med autentisering eller auktorisering av en användare](media/resilience-client-app/external-dependencies.png)

Att förvärva en token börjar tyst med att använda en giltig token från appens token cache. Om det inte finns någon giltig giltig token, bör appen försöka hämta en token med en uppdateringstoken, om den är tillgänglig, och token-slutpunkten. Om inget av dessa alternativ är tillgängligt ska appen Hämta en token med parametern "prompt = none". Detta använder slut punkten för auktorisering, men visar inte några användar gränssnitt för användaren. Om Microsoft-identiteten kan tillhandahålla en token till appen utan att interagera med användaren, kommer den att vara. Om ingen av dessa metoder resulterar i en token måste en användare autentisera interaktivt igen.

> [!NOTE]
> I allmänhet bör appar undvika att använda prompter som "login" och "medgivande" eftersom de tvingar användar interaktion även när ingen interaktion krävs.

## <a name="handle-service-responses-properly"></a>Hantera tjänstens svar korrekt

Även om program ska hantera alla fel svar finns det vissa svar som kan påverka återhämtningen. Om ditt program får en HTTP 429-svarskod, för många begär Anden, begränsar Microsoft-identiteten dina begär Anden. Om din app fortsätter att göra för många begär Anden fortsätter den att begränsas förhindra att din app tar emot tokens. Ditt program bör inte försöka hämta en token igen förrän efter tiden, i sekunder, i fältet Retry-After svar har slutförts. Att ta emot ett 429-svar är ofta en indikation på att programmet inte cachelagrar och återanvänder tokens på rätt sätt. Utvecklare bör se hur token cachelagras och återanvändas i programmet.

När ett program får en HTTP 5xx-svarskod får appen inte ange en snabb återförsöks slinga. När programmet är närvarande bör programmet respektera samma Retry-After hantering som för ett 429-svar. Om inget Retry-After-huvud tillhandahålls av svaret rekommenderar vi att du implementerar ett exponentiellt återförsök med det första försöket minst 5 sekunder efter svaret.

När en tids gräns för begäran inte bör försöka igen omedelbart. Implementera ett exponentiellt återförsök med det första försöket minst 5 sekunder efter svaret.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Utvärdera alternativ för att hämta information om auktorisering

Många program och API: er behöver detaljerad information om användaren som fattar auktoriseringsbeslut. Det finns några sätt för ett program att hämta den här informationen. Varje metod har sina fördelar och nack delar. Utvecklare bör väga dessa för att avgöra vilken strategi som är bäst för återhämtning i appen.

### <a name="tokens"></a>Token

Identiteter (ID) och åtkomsttoken innehåller standard anspråk som ger information om ämnet. Dessa dokumenteras i [Microsoft Identity Platform ID-token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) och [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)-åtkomsttoken. Om den information som appen behöver redan finns i token, är den mest effektiva tekniken för att hämta dessa data att använda token-anspråk som sparar den överhörande delen av ett ytterligare nätverks anrop för att hämta information separat. Färre nätverks anrop innebär högre total återhämtning för programmet.

> [!NOTE]
> Vissa program anropar UserInfo-slutpunkten för att hämta anspråk för den användare som autentiserats. Den information som är tillgänglig i ID-token som din app kan ta emot är en supermängd av den information som kan hämtas från UserInfo-slutpunkten. Din app ska använda ID-token för att hämta information om användaren i stället för att anropa UserInfo-slutpunkten.

En app-utvecklare kan utöka standard-token-anspråk med [valfria anspråk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims). Ett vanligt valfritt anspråk är [grupper](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-groups-optional-claims). Det finns flera sätt att lägga till grupp anspråk på. Alternativet "program grupp" omfattar endast grupper som tilldelats programmet. Alternativen "alla" eller "säkerhets grupper" innehåller grupper från alla appar i samma klient organisation, som kan lägga till flera grupper i token. Det är viktigt att du utvärderar effekt i ditt fall, eftersom det potentiellt kan negera effektiviteten som uppnås genom att begära grupper i token genom att orsaka token överdriven storlek och även kräva ytterligare anrop för att hämta den fullständiga listan över grupper.

I stället för att använda grupper i din token kan du i stället använda och inkludera app-roller. Utvecklare kan definiera [app-roller](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för sina appar och API: er som kunden kan hantera från sin katalog med hjälp av portalen eller API: er. IT-proffs kan sedan tilldela roller till olika användare och grupper för att kontrol lera vem som har åtkomst till det innehåll och de funktioner som finns. När en token utfärdas för programmet eller API: et, kommer de roller som tilldelas användaren att vara tillgängliga i rollerna i-token. Att hämta den här informationen direkt i en token kan spara ytterligare API-anrop.

Slutligen kan IT-administratörer även lägga till anspråk baserat på speciell information i en klient organisation. Ett företag kan till exempel ha ett tillägg för att ha ett företagsspecifika användar-ID.

I samtliga fall kan tillägg av information från katalogen direkt till en token vara effektiv och öka återhämtnings förmågan genom att minska antalet beroenden som appen har. Å andra sidan kan den inte lösa eventuella återhämtnings problem från att kunna hämta en token. Du bör bara lägga till valfria anspråk för programmets huvud scenarier. Om appen bara behöver information för administratörs funktionen, är det bäst att programmet bara hämtar informationen vid behov.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph tillhandahåller en enhetlig API-slutpunkt för att komma åt Microsoft 365 data som beskriver mönster för produktivitet, identitet och säkerhet i en organisation. Program som använder Microsoft Graph kan eventuellt använda all information i Microsoft 365 för auktoriseringsbeslut.

Appar kräver bara en enda token för att få åtkomst till alla Microsoft 365. Detta är mer flexibelt än att använda äldre API: er som är speciella för Microsoft 365-komponenter som Microsoft Exchange eller Microsoft SharePoint där flera token krävs.

När du använder Microsoft Graph API: er föreslår vi att du använder en [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview). Microsoft Graph SDK: er är utformade för att förenkla skapandet av högkvalitativa, effektiva och elastiska program som har åtkomst till Microsoft Graph.

För auktoriseringsbeslut bör utvecklare överväga när de anspråk som är tillgängliga i en token ska användas som ett alternativ till vissa Microsoft Graph samtal. Som nämnts ovan kunde utvecklare begära grupper, app-roller och valfria anspråk i sina tokens. När det gäller återhämtning kräver Microsoft Graph för auktorisering ytterligare nätverks anrop som förlitar sig på Microsoft-identitet (för att få åtkomst till Microsoft Graph) samt Microsoft Graph sig själva. Om ditt program redan förlitar sig på Microsoft Graph som dess data lager, är det dock inte ytterligare risk att ta med i grafen för auktorisering.

## <a name="use-broker-authentication-on-mobile-devices"></a>Använda Broker-autentisering på mobila enheter

På mobila enheter kan du använda en autentiseringstjänst som Microsoft Authenticator för att förbättra återhämtningen. Service Broker lägger till förmåner över vad som finns tillgängligt med andra alternativ, till exempel systemets webbläsare eller en inbäddad webbvy. Authentication Broker kan använda en [primär uppdateringstoken](https://docs.microsoft.com/azure/active-directory/devices/concept-primary-refresh-token) (PRT) som innehåller anspråk om användaren och enheten och kan användas för att hämta autentiseringstoken för att få åtkomst till andra program från enheten. När en PRT används för att begära åtkomst till ett program, är dess enhets-och MFA-anspråk betrodda av Azure AD. Detta ökar återhämtningen genom att undvika ytterligare steg för att autentisera enheten igen. Användarna kommer inte att kunna kontaktas med flera MFA-prompter på samma enhet, vilket ökar återhämtningen genom att minska beroenden för externa tjänster och förbättra användar upplevelsen.

![Ett program som gör ett anrop till Microsoft Identity, men anropet går via ett token-cacheminne samt ett token Store och en autentiseringsprovider på enheten som kör programmet](media/resilience-client-app/authentication-broker.png)

Broker-autentisering stöds automatiskt av MSAL. Du hittar mer information om hur du använder Brokered Authentication på följande sidor:

- [Konfigurera SSO på macOS och iOS](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-macos-ios#sso-through-authentication-broker-on-ios)
- [Aktivera enkel inloggning mellan appar på Android med MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-android-single-sign-on)

## <a name="adopt-continuous-access-evaluation"></a>Inför utvärdering av kontinuerlig åtkomst

[Kontinuerlig åtkomst utvärdering (CAE)](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) är en ny utveckling som kan öka program säkerhet och återhämtning med långvariga token. CAE är en ny bransch standard som utvecklas i arbets gruppen delade signaler och händelser i OpenID Foundation. Med CAE kan en åtkomsttoken återkallas baserat på [kritiska händelser](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#critical-event-evaluation) och [princip utvärdering](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation#conditional-access-policy-evaluation-preview), i stället för att förlita dig på en kort livs längd för token. För vissa resurs-API: er, eftersom risk och principer utvärderas i real tid, kan CAE avsevärt öka token-livstiden upp till 28 timmar. I takt med att resurs-API: er och program använder CAE kan Microsoft Identity utfärda åtkomsttoken som är revocable och som är giltiga under långa tids perioder. Dessa lång livs längds-token kommer att proaktivt uppdateras av MSAL.

När CAE är i tidiga faser är det möjligt att [utveckla klient program idag som kommer att ha nytta av CAE](../develop/app-resilience-continuous-access-evaluation.md) när de resurser (API: er) som programmet använder implementera CAE. I takt med att fler resurser antar CAE kommer ditt program att kunna hämta CAE-aktiverade tokens för dessa resurser. Microsoft Graph API, och [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview): er, kommer att se CAE-funktionen tidigt 2021. Om du vill delta i den offentliga för hands versionen av Microsoft Graph med CAE kan du meddela oss att du är intresse rad av detta: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Om du utvecklar resurs-API: er, rekommenderar vi att du deltar i de [delade signalerna och händelserna WG](https://openid.net/wg/sse/). Vi arbetar med den här gruppen för att möjliggöra delning av säkerhets händelser mellan Microsofts identitets-och resurs leverantörer.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du utvärderings aktiverade API: er för kontinuerlig åtkomst i dina program](../develop/app-resilience-continuous-access-evaluation.md)
- [Bygg återhämtning till daemon-program](resilience-daemon-app.md)
- [Bygg återhämtning i din infrastruktur för identitets-och åtkomst hantering](resilience-in-infrastructure.md)
- [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)
