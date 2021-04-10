---
title: Hantera fel och undantag i MSAL.js
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, utmanings krav för villkorlig åtkomst och nya försök i MSAL.js-program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761337"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Hantera fel och undantag i MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Fel hantering i MSAL.js

MSAL.js innehåller fel objekt som kan vara abstrakta och klassificera de olika typerna av vanliga fel. Det innehåller också ett gränssnitt för att få åtkomst till detaljerad information om felen, till exempel fel meddelanden, för att hantera dem på rätt sätt.

### <a name="error-object"></a>Fel objekt

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Genom att utöka fel klassen har du åtkomst till följande egenskaper:
- `AuthError.message`: Samma som `errorMessage` .
- `AuthError.stack`: Stack spårning för utlösta fel.

### <a name="error-types"></a>Fel typer

Följande fel typer är tillgängliga:

- `AuthError`: Bask lass klassen för MSAL.jss biblioteket, används också för oväntade fel.

- `ClientAuthError`: Fel klass, som anger ett problem med klientautentisering. De flesta fel som kommer från biblioteket kommer att bli ClientAuthErrors. Dessa fel beror på saker som att anropa en inloggnings metod när inloggningen redan pågår, att användaren avbryter inloggningen och så vidare.

- `ClientConfigurationError`: Fel klass, utökningar `ClientAuthError` som genererats innan begär Anden görs när de tilldelade parametrarna för användar konfiguration är felaktiga eller saknas.

- `ServerError`: Klassen Error representerar de fel strängar som skickats av autentiseringsservern. Detta kan vara fel som ogiltiga format eller parametrar för begäran, eller andra fel som hindrar servern från att autentisera eller auktorisera användaren.

- `InteractionRequiredAuthError`: Fel klass, utökar `ServerError` för att representera Server fel som kräver ett interaktivt anrop. Det här felet uppstår av `acquireTokenSilent` om användaren måste interagera med servern för att ange autentiseringsuppgifter eller tillstånd för autentisering/auktorisering. Felkoderna är `"interaction_required"` , `"login_required"` och `"consent_required"` .

För fel hantering i autentiserings flöden med omdirigerings metoder ( `loginRedirect` , `acquireTokenRedirect` ) måste du registrera återanropet, som anropas med lyckat eller misslyckat efter omdirigeringen med hjälp av `handleRedirectCallback()` metoden enligt följande:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metoderna för att se pop-up ( `loginPopup` , `acquireTokenPopup` ) returnera löfte så att du kan använda löftes mönstret (. och. Catch) för att hantera dem som de visas:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Fel som kräver interaktion

Ett fel returneras när du försöker använda en icke-interaktiv metod för att förvärva en token, till exempel `acquireTokenSilent` , men MSAL inte kunde göra det tyst.

Möjliga orsaker är:

- Du måste logga in
- Du måste godkänna
- Du måste gå igenom en Multi-Factor Authentication-upplevelse.

Reparationen är att anropa en interaktiv metod som `acquireTokenPopup` eller `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

När du hämtar token tyst (med `acquireTokenSilent` ) med hjälp av MSAL.js kan ditt program få fel när en [utmanings utmaning för anspråk](../azuread-dev/conditional-access-dev-guide.md) , till exempel MFA-princip krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att göra ett interaktivt anrop för att hämta token i MSAL.js som `acquireTokenPopup` eller `acquireTokenRedirect` som i följande exempel:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Att interaktivt förvärva token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

När du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. I det här fallet kan du skicka de anspråk som returneras i fel till `claimsRequest` fältet i `AuthenticationParameters.ts` klassen för att uppfylla den aktuella principen. 

Mer information finns i [begära ytterligare anspråk](active-directory-optional-claims.md) .


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Nästa steg

Överväg att aktivera [loggning i MSAL.js](msal-logging-js.md) för att hjälpa dig att diagnostisera och felsöka problem.
