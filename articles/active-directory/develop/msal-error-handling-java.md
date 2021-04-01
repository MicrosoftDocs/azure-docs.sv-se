---
title: Hantera fel och undantag i MSAL4J
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, anspråk på villkorlig åtkomst och återförsök i MSAL4J-program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760682"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Hantera fel och undantag i MSAL för Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Fel hantering i MSAL för Java

I MSAL för Java finns det tre typer av undantag: `MsalClientException` , `MsalServiceException` och `MsalInteractionRequiredException` . allt ärver från `MsalException` .

- `MsalClientException` genereras när ett fel inträffar som är lokalt i biblioteket eller på enheten.
- `MsalServiceException` utlöses när STS (Secure token service) returnerar ett fel svar eller ett annat nätverks fel inträffar.
- `MsalInteractionRequiredException` genereras när UI-interaktion krävs för att autentiseringen ska lyckas.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` exponerar HTTP-huvuden som returneras i begär anden till STS. Få åtkomst till dem via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

En av de vanliga status koderna som returnerades från MSAL för Java vid anrop `AcquireTokenSilently()` är `InvalidGrantError` . Det innebär att ytterligare användar åtgärder krävs innan en autentiseringstoken kan utfärdas. Programmet bör anropa autentiseringspaket igen, men i interaktivt läge genom att skicka `AuthorizationCodeParameters` eller `DeviceCodeParameters` för offentliga klient program.

I de flesta fall då `AcquireTokenSilently` Miss lyckas, beror det på att token cache inte har en token som matchar din begäran. Åtkomsttoken upphör att gälla om en timme och kommer att `AcquireTokenSilently` försöka hämta en ny utifrån en uppdateringstoken. I OAuth2 villkor är det här uppdaterings-token. Det här flödet kan också inte utföras av olika anledningar, t. ex. När en klient administratör konfigurerar mer strikta inloggnings principer.

Vissa villkor som resulterar i detta fel är lätta för användarna att lösa. De kan till exempel behöva godkänna användnings villkoren eller så kan begäran inte uppfyllas med den aktuella konfigurationen eftersom datorn måste ansluta till ett enskilt företags nätverk.

MSAL visar ett `reason` fält som du kan använda för att ge en bättre användar upplevelse. Fältet kan till exempel `reason` leda till att användaren anger att deras lösen ord har upphört att gälla eller att de måste ge tillåtelse att använda vissa resurser. De värden som stöds är en del av  `InteractionRequiredExceptionReason` uppräkningen:

| Anledning | Innebörd | Rekommenderad hantering |
|---------|-----------|-----------------------------|
| `BasicAction` | Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa `acquireToken` med interaktiva parametrar. |
| `AdditionalAction` | Villkoret kan lösas med hjälp av ytterligare återställnings interaktion med systemet utanför det interaktiva autentiserings flödet. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar vilken åtgärd som ska vidtas. Den anropande appen kan välja att dölja flöden som kräver ytterligare åtgärder om användaren inte är tvungen att slutföra åtgärden. |
| `MessageOnly` | Villkoret kan inte lösas just nu. Starta interaktiva autentiserings flöde för att visa ett meddelande som förklarar villkoret. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar villkoret. `acquireToken` Returnerar `UserCanceled` felet när användaren har läst meddelandet och stängt fönstret. Appen kan välja att dölja flöden som resulterar i ett meddelande om användaren inte har nytta av meddelandet. |
| `ConsentRequired`| Användar medgivande saknas eller har återkallats. |Anropa `acquireToken` med interaktiva parametrar så att användaren kan ge sitt medgivande. |
| `UserPasswordExpired` | Användarens lösen ord har upphört att gälla. | Anropa `acquireToken` med interaktiv parameter så att användaren kan återställa sina lösen ord. |
| `None` |  Mer information finns. Villkoret kan lösas av användar interaktion under det interaktiva autentiserings flödet. | Anropa `acquireToken` med interaktiva parametrar. |

### <a name="code-example"></a>Kod exempel

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Nästa steg

Överväg att aktivera [loggning i MSAL för Java](msal-logging-java.md) för att hjälpa dig att diagnostisera och felsöka problem.
