---
title: Fel och undantag (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, villkorlig åtkomst och anspråk på anspråk i MSAL Android-program.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761388"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Hantera undantag och fel i MSAL för Android

Undantag i Microsoft Authentication Library (MSAL) är avsedda att hjälpa utvecklare att felsöka sina program. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för autentisering och auktorisering](reference-aadsts-error-codes.md).

Under inloggningen kan du stöta på fel om medgivanden, villkorlig åtkomst (MFA, enhets hantering, platsbaserade begränsningar), utfärdande och inlösen av token och användar egenskaper.


|Fel klass | Orsak/fel sträng| Så här hanterar du |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Uppdateringstoken som används för att lösa in åtkomsttoken är ogiltig, har upphört att gälla eller återkallats. Detta undantag kan bero på en lösen ords ändring. </li><li>`NO_TOKENS_FOUND`: Åtkomsttoken finns inte och det går inte att hitta någon uppdateringstoken för att lösa in åtkomsttoken.</li> <li>Steg-upp krävs<ul><li>Multifaktorautentisering</li><li>Anspråk som saknas</li></ul></li><li>Blockeras av villkorlig åtkomst (till exempel krävs en installation av [Authentication Broker](./msal-android-single-sign-on.md) )</li><li>`NO_ACCOUNT_FOUND`: Inget konto är tillgängligt i cacheminnet för tyst autentisering.</li></ul> |Anropa `acquireToken()` för att uppmana användaren att ange sitt användar namn och lösen ord och eventuellt godkänna och utföra Multi Factor Authentication.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: Användaren eller servern har inte accepterat alla omfattningar. Servern kan neka en omfattning om det begärda omfånget inte stöds, inte känns igen eller inte stöds för ett visst konto. </li></ul>| Utvecklaren bör bestämma om du vill fortsätta autentiseringen med de beviljade omfattningarna eller avsluta autentiseringsprocessen. Alternativ för att skicka begäran om Hämta token endast för beviljade omfattningar och tillhandahålla tips för vilka behörigheter som har beviljats genom att skicka `silentParametersForGrantedScopes` och anropa `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: Den här begäran saknar en obligatorisk parameter, innehåller en ogiltig parameter, innehåller en parameter mer än en gång eller är annars felaktig. </li><li>`SERVICE_NOT_AVAILABLE`: Representerar 500/503/506-felkoder på grund av att tjänsten inte är igång. </li><li>`UNAUTHORIZED_REQUEST`: Klienten har inte behörighet att begära en auktoriseringskod.</li><li>`ACCESS_DENIED`: Resurs ägaren eller auktoriseringsservern nekade begäran.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` verifieringen misslyckades</li><li>`UNKNOWN_ERROR`: Begäran till Server misslyckades, men inget fel `error_description` har returnerats från tjänsten.</li><ul>| Den här undantags klassen representerar fel vid kommunikation med tjänsten, kan vara från behörighets-eller token-slutpunkterna. MSAL läser fel och error_description från Server svaret. De här felen löses vanligt vis genom att du korrigerar appars konfigurationer i kod eller i registrerings portalen för appen. Sällan ett avbrott i tjänsten kan utlösa den här varningen, som bara kan begränsas genom att vänta på att tjänsten ska återställas.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Det finns flera cacheposter och SDK: n kan inte identifiera rätt åtkomst eller uppdateringstoken från cachen. Detta undantag tyder vanligt vis på ett fel i SDK: n för att lagra token eller att utfärdaren inte finns i den tysta begäran och flera matchande token hittas. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Det finns inget aktivt nätverk på enheten. </li><li>`JSON_PARSE_FAILURE`: SDK kunde inte parsa JSON-formatet.</li><li>`IO_ERROR`: `IOException` hände, kan vara ett enhets-eller nätverks fel. </li><li>`MALFORMED_URL`: URL: en är felaktig. Troligt vis orsakades vid konstruktion av auth-begäran,-utfärdare eller omdirigerings-URI. </li><li>`UNSUPPORTED_ENCODING`: Kodningen stöds inte av enheten. </li><li>`NO_SUCH_ALGORITHM`: Algoritmen som används för att generera [PKCE](https://tools.ietf.org/html/rfc7636) -utmaningen stöds inte. </li><li>`INVALID_JWT`: `JWT` returnerad av servern är ogiltig eller tom eller felaktig. </li><li>`STATE_MISMATCH`: Tillstånd från svars svar matchade inte tillståndet i auktoriseringsbegäran. För auktoriserings begär Anden kontrollerar SDK det tillstånd som returnerades från omdirigering och den som skickas i begäran. </li><li>`UNSUPPORTED_URL`: URL: en stöds inte, det går inte att utföra verifiering av ADFS-auktoritet. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: Auktoriteten stöds inte för verifiering av utfärdare. SDK stöder B2C-utfärdare, men har inte stöd för verifiering av B2C-auktoritet. Endast välkänd värd kommer att stödjas. </li><li>`CHROME_NOT_INSTALLED`: Chrome är inte installerat på enheten. SDK använder en anpassad flik för begäran om auktorisering om det är tillgängligt och kommer att gå tillbaka till Chrome-webbläsaren. </li><li>`USER_MISMATCH`: Den användare som anges i begäran om inhämta token matchar inte användaren som returnerades från servern.</li></ul>|Den här undantags klassen representerar allmänna fel som är lokala i biblioteket. Dessa undantag kan hanteras genom att korrigera begäran.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: Användaren initierade ett interaktivt flöde och innan du tar emot token tillbaka som avbrutit begäran. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Utfärdare måste anges för `acquireTokenSilent` .</li></ul>|De här felen kan begränsas av utvecklaren som korrigerar argument och säkerställer aktivitet för interaktiv autentisering, motringning för slut för ande, omfattningar och ett konto med ett giltigt ID har angetts.|


## <a name="catching-errors"></a>Fånga fel

Följande kodfragment visar ett exempel på att fånga fel i händelse av tysta `acquireToken` anrop.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [loggning i MSAL för Android](msal-logging-android.md).