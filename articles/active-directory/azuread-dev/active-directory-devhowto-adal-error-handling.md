---
title: ADAL klient program fel hantering metod tips | Azure
description: Ger fel hanterings vägledning och bästa praxis för ADAL-klientprogram.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383742"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Fel hantering av metod tips för ADAL-klienter (Azure Active Directory Authentication Library)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Den här artikeln ger vägledning om vilken typ av fel som utvecklare kan stöta på när de använder ADAL för att autentisera användare. När du använder ADAL finns det flera fall där en utvecklare kan behöva stega i och hantera fel. Rätt fel hantering garanterar en bra upplevelse för slutanvändaren och begränsar antalet gånger som slutanvändaren måste logga in.

I den här artikeln går vi igenom de specifika fallen för varje plattform som stöds av ADAL, och hur programmet kan hantera varje fall som det ska. Fel vägledningen delas upp i två bredare kategorier, baserat på mönstren för token som tillhandahålls av ADAL-API: er:

- **AcquireTokenSilent**: klienten försöker få en token tyst (inget gränssnitt) och kan Miss lyckas om ADAL Miss lyckas. 
- **AcquireToken**: klienten kan försöka med tyst förvärv, men kan även utföra interaktiva begär Anden som kräver inloggning.

> [!TIP]
> Det är en bra idé att logga alla fel och undantag när du använder ADAL och Azure AD. Loggar är inte bara användbara för att förstå programmets övergripande hälso tillstånd, men är också viktiga vid fel sökning av bredare problem. Även om ditt program kan återställas från vissa fel, kan det vara en ledtråd vid bredare design problem som kräver kod ändringar för att lösa problemet. 
> 
> När du implementerar de fel villkor som beskrivs i det här dokumentet ska du logga felkoden och en beskrivning av de orsaker som beskrivs ovan. Se [fel-och loggnings referensen](#error-and-logging-reference) för exempel på loggnings kod. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent försöker hämta en token med den garanti att slutanvändaren inte ser något användar gränssnitt (UI). Det finns flera fall där tyst förvärv kan Miss lyckas och måste hanteras via interaktiva begär Anden eller av en standard hanterare. Vi går igenom vad som gäller när och hur man använder varje fall i de avsnitt som följer.

Det finns en uppsättning fel som genereras av operativ systemet, vilket kan kräva fel hantering som är specifik för programmet. Mer information finns i avsnittet "operativ system" fel i [fel-och loggnings referens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- [Interna klient](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) program (iOS, Android, .net Desktop eller Xamarin)
- [Webb klient](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) program som anropar en [resurs](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.net)

### <a name="error-cases-and-actionable-steps"></a>Fel ärenden och åtgärds bara steg

Grundläggande finns två fall av AcquireTokenSilent-fel:

| Ärende | Beskrivning |
|------|-------------|
| **Fall 1**: felet kan matchas med en interaktiv inloggning | För fel som orsakas av brist på giltiga token krävs en interaktiv begäran. Mer specifikt kräver cache-sökning och en ogiltig/utgången uppdateringstoken kräver ett AcquireToken-anrop för att lösa problemet.<br><br>I dessa fall måste slutanvändaren uppmanas att logga in. Programmet kan välja att utföra en interaktiv begäran omedelbart efter slut användar interaktion (t. ex. genom att trycka på en knapp) eller senare. Valet beror på programmets önskade beteende.<br><br>Se koden i följande avsnitt för detta specialfall och de fel som diagnostiserar den.|
| **Fall 2**: det går inte att matcha fel med en interaktiv inloggning | För nätverks-och tillfälliga/tillfälliga fel, eller andra fel, löser inte problemet med en interaktiv AcquireToken-begäran. Onödiga interaktiva inloggnings meddelanden kan också vara frustrerande för slutanvändare. ADAL försöker automatiskt med ett enda försök för de flesta fel på AcquireTokenSilent-fel.<br><br>Klient programmet kan också försöka igen vid ett senare tillfälle, men när och hur är beroende av programmets beteende och önskad slut användar upplevelse. Programmet kan till exempel göra ett AcquireTokenSilent-försök igen efter ett par minuter eller som svar på vissa slut användar åtgärder. Ett omedelbart återförsök leder till att programmet begränsas och inte bör provas.<br><br>Ett senare försök med samma fel innebär inte att klienten bör göra en interaktiv begäran med AcquireToken, eftersom det inte löser felet.<br><br>Se koden i följande avsnitt för detta specialfall och de fel som diagnostiserar den. |

### <a name="net"></a>.NET

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [inaktuell] acquireTokenSilent (...)
- [inaktuell] acquireTokenByRefreshToken (...) 

Koden ska implementeras på följande sätt:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [inaktuell] acquireTokenSilent (...)

Koden ska implementeras på följande sätt:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- acquireTokenSilentWithResource(...)

Koden ska implementeras på följande sätt:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken är standard metoden ADAL som används för att hämta tokens. I de fall där användar identitet krävs försöker AcquireToken att få en token tyst först och visar sedan användar gränssnittet vid behov (om inte PromptBehavior. Never skickas). I de fall där program identitet krävs försöker AcquireToken hämta en token, men visar inte användar gränssnittet eftersom det inte finns någon slutanvändare. 

Vid hantering av AcquireToken-fel beror fel hanteringen på den plattform och det scenario som programmet försöker uppnå. 

Operativ systemet kan också generera en uppsättning fel som kräver fel hantering beroende av det specifika programmet. Mer information finns i "operativ system fel" i [fel-och loggnings referens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- Interna klient program (iOS, Android, .NET Desktop eller Xamarin)
- Webb program som anropar ett resurs-API (.NET)
- Program med en enda sida (Java Script)
- Tjänst-till-tjänst-program (.NET, Java)
  - Alla scenarier, inklusive på uppdrag av
  - På uppdrag av vissa scenarier

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Fel ärenden och åtgärds bara steg: interna klient program

Om du skapar ett internt klient program finns det några fel hanterings fall som är relaterade till nätverks problem, tillfälliga fel och andra plattformsspecifika fel. I de flesta fall ska ett program inte utföra omedelbara återförsök, utan att vänta på en slut användar interaktion som efterfrågar en inloggning. 

Det finns några särskilda fall där ett enda försök kan lösa problemet. Till exempel när en användare behöver aktivera data på en enhet eller slutfört hämtningen av Azure AD Broker efter det första försöket. 

I händelse av fel kan ett program Visa ett användar gränssnitt som gör att slutanvändaren kan utföra en viss interaktion som efterfrågar ett nytt försök. Om enheten till exempel inte kunde ansluta till fel meddelandet "försök att logga in igen" och ange ett AcquireToken-försök i stället för att omedelbart försöka igen med felet. 

Fel hantering i interna program kan definieras i två fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Fel som inte går att försöka igen (de flesta fall) | 1. Försök inte att köra omedelbara försök igen. Presentera användar gränssnittet för slutanvändaren baserat på det specifika fel som anropar ett återförsök (till exempel "försök att logga in igen" eller "Ladda ned Azure AD Broker-program"). |
| **Fall 2**:<br>Återförsöks fel | 1. utför ett enda försök eftersom slutanvändaren kan ha angett ett tillstånd som resulterar i ett lyckat resultat.<br><br>2. om det inte går att försöka igen visar du slutanvändarens användar gränssnitt baserat på det särskilda felet som anropar ett återförsök ("försök att logga in igen", "Ladda ned Azure AD Broker-app" osv.). |

> [!IMPORTANT]
> Om ett användar konto skickas till ADAL i ett tyst anrop och Miss lyckas, kan användaren logga in med ett annat konto genom att utföra den efterföljande interaktiva begäran. Efter en lyckad AcquireToken med ett användar konto måste programmet verifiera att den inloggade användaren matchar programmets lokala användar objekt. Ett matchnings fel genererar inget undantag (förutom i mål C), men bör beaktas i de fall där en användare är känd lokalt före autentiseringsbegäranden (t. ex. ett misslyckat tyst anrop).
>

#### <a name="net"></a>.NET

Följande vägledning ger exempel på fel hantering tillsammans med alla icke-tysta AcquireToken (...) ADAL-metoder, *förutom*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync (..., UserAssertion,...)   

Koden ska implementeras på följande sätt:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET har extra betydelse eftersom det stöder PromptBehavior. Never, som har beteende som AcquireTokenSilent.
>

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- acquireToken (..., PromptBehavior. Never)

Koden ska implementeras på följande sätt:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Följande vägledning ger exempel på fel hantering tillsammans med alla icke-tysta AcquireToken (...) ADAL-metoder. 

Koden ska implementeras på följande sätt:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Följande vägledning ger exempel på fel hantering tillsammans med alla icke-tysta AcquireToken (...) ADAL-metoder. 

Koden ska implementeras på följande sätt:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Fel ärenden och åtgärds bara steg: webb program som anropar ett resurs-API (.NET)

Om du skapar en .NET-webbapp som anropar en token med en auktoriseringskod för en resurs, är den enda kod som krävs en standard hanterare för det allmänna fallet. 

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- AcquireTokenByAuthorizationCodeAsync(...)

Koden ska implementeras på följande sätt:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Fel och åtgärds bara steg: program med en sida (adal.js)

Om du skapar ett program med en enda sida med hjälp av adal.js med AcquireToken, liknar fel hanterings koden samma som för ett typiskt tyst anrop. Särskilt i adal.js visar AcquireToken aldrig ett användar gränssnitt. 

En misslyckad AcquireToken har följande fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Går att matcha med en interaktiv begäran | 1. Om inloggningen () Miss lyckas ska du inte utföra omedelbara försök. Försök igen efter att användaren har angett ett nytt försök.|
| **Fall 2**:<br>Går inte att matcha med en interaktiv begäran. Felet kan ha ett nytt försök. | 1. utför ett enda försök eftersom slutanvändaren är större och har angett ett tillstånd som leder till att det lyckas.<br><br>2. om det inte går att försöka igen visas slutanvändaren med en åtgärd baserat på det särskilda fel meddelandet som kan anropa ett nytt försök ("försök att logga in igen"). |
| **Fall 3**:<br>Går inte att matcha med en interaktiv begäran. Felet kan inte göras igen. | 1. Försök inte att köra omedelbara försök igen. Presentera slutanvändaren med en åtgärd baserat på det särskilda fel som kan anropa ett nytt försök ("försök att logga in igen"). |

Koden ska implementeras på följande sätt:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Fel ärenden och åtgärds bara steg: tjänst-till-tjänst-program (endast .NET)

Om du skapar ett tjänst-till-tjänst-program som använder AcquireToken finns det några viktiga fel som din kod måste hantera. Det enda du behöver göra är att returnera felet tillbaka till den anropande appen (för många fall) eller tillämpa en strategi för återförsök. 

#### <a name="all-scenarios"></a>Alla scenarier

För *alla* service-till-service-programscenarier, inklusive på uppdrag av:

- Försök inte med ett omedelbart försök. ADAL försöker med ett enda återförsök för vissa misslyckade begär Anden. 
- Fortsätt bara att försöka igen när en användare eller app-åtgärd begär ett nytt försök. Till exempel bör ett daemon-program som fungerar med ett visst intervall vänta till nästa intervall att försöka igen.

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Koden ska implementeras på följande sätt:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>På uppdrag av scenarier

För program som är *i* tjänst-till-tjänst-scenarier.

Följande vägledning ger exempel på fel hantering tillsammans med ADAL-metoder: 

- AcquireTokenAsync(..., UserAssertion, ...)

Koden ska implementeras på följande sätt:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Vi har skapat ett [komplett exempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) som visar det här scenariot.

## <a name="error-and-logging-reference"></a>Fel-och loggnings referens

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Logga personlig identifierbar information & organisations identifierbar information 
Som standard samlar ADAL inte in eller loggar all personlig identifierbar information eller information som är identifierbar för organisationen. Med biblioteket kan appar utvecklare aktivera detta genom en setter i klassen för loggning. Genom att logga personlig identifierbar information eller organisations identifierbar information, tar appen ansvar för säker hantering av mycket känsliga data och uppfyller alla myndighets krav.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL biblioteks fel

För att utforska vissa ADAL-fel är käll koden i [databasen Azure-ActiveDirectory-Library-for-dotNet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) den bästa fel referensen.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för fel loggnings kod

ADAL .NET-loggnings ändringar beroende på vilken plattform du arbetar på. Se [loggnings-wikin](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) för kod för att aktivera loggning.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL biblioteks fel

För att utforska vissa ADAL-fel är käll koden i [databasen Azure-ActiveDirectory-bibliotek-för-Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) den bästa fel referensen.

#### <a name="operating-system-errors"></a>Fel i operativ system

Android OS-fel exponeras genom AuthenticationException i ADAL, är identifierade som "SERVER_INVALID_REQUEST" och kan användas mer detaljerad genom fel beskrivningarna. 

En fullständig lista över vanliga fel och vilka steg du bör vidta när din app eller slutanvändare stöter på dem finns i [ADAL Android-wikin](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Vägledning för fel loggnings kod

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL biblioteks fel

För att utforska vissa ADAL-fel är käll koden i [databasen Azure-ActiveDirectory-Library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) den bästa fel referensen.

#### <a name="operating-system-errors"></a>Fel i operativ system

iOS-fel kan uppstå under inloggningen när användarna använder webbvyer och typen av autentisering. Detta kan orsakas av villkor som TLS-fel, tids gränser eller nätverks fel:

- För delning av rättigheter är inloggningar inte beständiga och cachen verkar vara tom. Du kan lösa problemet genom att lägga till följande kodrad i nyckel ringen: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- För den NsUrlDomain uppsättningen av fel ändras åtgärden beroende på appens logik. Se [NSURLErrorDomain Reference documentation](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) för vissa instanser som kan hanteras.
- Se [ADAL OBJ-C vanliga problem](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) för att visa en lista över vanliga fel som hanteras av ADAL-teamet för mål-c.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för fel loggnings kod

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Vägledning för fel loggnings kod – Java Script 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Relaterat innehåll

* [Azure AD-autentiseringspaket][AAD-Auth-Libraries]
* [Azure AD-autentiserings scenarier][AAD-Auth-Scenarios]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]

Använd avsnittet kommentarer som följer för att ge feedback och hjälpa oss att förfina och forma vårt innehåll.

[![Visar knappen "Logga in med Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

