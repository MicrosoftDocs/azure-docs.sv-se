---
title: Hantera fel och undantag i MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, utmanings krav för villkorlig åtkomst och nya försök i MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584052"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Hantera fel och undantag i MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Fel hantering i MSAL.NET

När du bearbetar .NET-undantag kan du använda undantags typen och `ErrorCode` medlemmen för att skilja mellan undantag. `ErrorCode` värdena är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Du kan också ha en titt på fälten [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)och [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) genereras kan du testa [autentiserings-och auktoriseringsfel](reference-aadsts-error-codes.md) för att se om koden visas där.

### <a name="common-net-exceptions"></a>Vanliga .NET-undantag

Här följer de vanliga undantag som kan uppstå och vissa möjliga åtgärder:  

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: användaren eller administratören har inte samtyckt till att använda programmet med ID {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Hämta användar medgivande först. Om du inte använder .NET Core (som inte har något webb gränssnitt) anropar du (bara en gång) `AcquireTokeninteractive` . Om du använder .NET Core eller inte vill göra något `AcquireTokenInteractive` kan användaren navigera till en URL för att ge medgivande: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . för att anropa `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: användaren måste använda [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md).| Det finns ingen minskning. Om MFA har kon figurer ATS för din klient organisation och Azure Active Directory (AAD) bestämmer sig för att använda den, kan du återgå till ett interaktivt flöde, till exempel `AcquireTokenInteractive` eller `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: anslags typen stöds inte över */vanliga* -eller */consumers* -slutpunkterna. Använd den */organizations* eller klient-/regionsspecifika slut punkten. Du använde */vanliga*.| Som förklaras i meddelandet från Azure AD måste utfärdaren ha en klient eller på annat */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: begär ande texten måste innehålla följande parameter: `client_secret or client_assertion` .| Detta undantag kan genereras om programmet inte har registrerats som ett offentligt klient program i Azure AD. Redigera manifestet för ditt program i Azure Portal och ange `allowPublicClient` till `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Det gick inte att identifiera inloggad användare| Det gick inte att fråga den aktuella Windows-inloggade användaren eller så är den här användaren inte AD eller Azure AD-ansluten (arbets plats anslutna användare stöds inte). Minskning 1: på UWP kontrollerar du att programmet har följande funktioner: Enterprise-autentisering, privata nätverk (klient och Server), information om användar konton. Minskning 2: implementera din egen logik för att hämta användar namnet (till exempel john@contoso.com ) och Använd `AcquireTokenByIntegratedWindowsAuth` formuläret som tar med användar namnet.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Den här metoden förlitar sig på ett protokoll som exponeras av Active Directory (AD). Om en användare har skapats i Azure AD utan AD-återställning ("hanterad" användare) kommer den här metoden att Miss förväntas. Användare som skapats i AD och som backas upp av Azure AD ("federerade" användare) kan dra nytta av den här icke-interaktiva autentiseringsmetoden. Minskning: Använd interaktiv autentisering.|

### `MsalUiRequiredException`

En av de gemensamma status koderna som returnerades från MSAL.NET vid anrop `AcquireTokenSilent()` är `MsalError.InvalidGrantError` . Den här status koden innebär att programmet ska anropa autentiseringspaket igen, men i interaktivt läge (AcquireTokenInteractive eller AcquireTokenByDeviceCodeFlow för offentliga klient program måste du ha en utmaning i Web Apps). Detta beror på att ytterligare användar åtgärder krävs innan autentiseringstoken kan utfärdas.

I de flesta fall då `AcquireTokenSilent` detta Miss lyckas beror det på att token cache inte har tokens som matchar din begäran. Åtkomsttoken upphör att gälla om 1 timme och `AcquireTokenSilent` försöker hämta en ny baserat på en uppdateringstoken (i OAuth2 villkor är detta "Refresh token"-flödet). Det här flödet kan också inte utföras av olika orsaker, till exempel om en klient organisations administratör konfigurerar mer strikta inloggnings principer. 

Interaktionen syftar på att användaren ska göra en åtgärd. Några av dessa villkor är enkla för användarna att lösa (till exempel acceptera användnings villkor med ett enda klick) och vissa kan inte lösas med den aktuella konfigurationen (till exempel att datorn i fråga måste ansluta till ett visst företags nätverk). Vissa hjälper användaren att konfigurera Multi-Factor Authentication eller installera Microsoft Authenticator på deras enheter.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` klassificering av klassificering

MSAL exponerar ett `Classification` fält, som du kan läsa för att ge en bättre användar upplevelse. Om du till exempel vill meddela användaren att deras lösen ord har upphört att gälla eller om de måste ange medgivande för att använda vissa resurser. De värden som stöds är en del av `UiRequiredExceptionClassification` uppräkningen:

| Klassificering    | Innebörd           | Rekommenderad hantering |
|-------------------|-------------------|----------------------|
| BasicAction | Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |
| AdditionalAction | Villkoret kan lösas med hjälp av ytterligare återställnings interaktion med systemet, utanför det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar åtgärds åtgärden. Anrops programmet kan välja att dölja flöden som kräver additional_action om användaren inte är klar med åtgärden för att åtgärda problemet. |
| MessageOnly      | Villkoret kan inte lösas just nu. Om du startar ett interaktivt autentiseringsschema visas ett meddelande som förklarar villkoret. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar villkoret. AcquireTokenInteractively () returnerar UserCanceled-fel när användaren har läst meddelandet och stängt fönstret. Anrops programmet kan välja att dölja flöden som resulterar i message_only om användaren inte har nytta av meddelandet.|
| ConsentRequired  | Användar medgivande saknas eller har återkallats. | Anropa AcquireTokenInteractively () för att användaren ska kunna ge sitt medgivande. |
| UserPasswordExpired | Användarens lösen ord har upphört att gälla. | Anropa AcquireTokenInteractively () så att användaren kan återställa sina lösen ord. |
| PromptNeverFailed| Interaktiv autentisering anropades med parameter tolken = aldrig, tvinga MSAL att förlita sig på webbläsarens cookies och inte att visa webbläsaren. Detta har misslyckats. | Anropa AcquireTokenInteractively () utan prompt. None |
| AcquireTokenSilentFailed | MSAL SDK har inte tillräckligt med information för att hämta en token från cachen. Detta kan bero på att det inte finns några token i cachen eller om det inte gick att hitta något konto. Fel meddelandet innehåller mer information.  | Anropa AcquireTokenInteractively (). |
| Inget    | Det finns ingen ytterligare information. Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |

## <a name="net-code-example"></a>Exempel på .NET-kod

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

När du anropar ett API som kräver villkorlig åtkomst från MSAL.NET måste ditt program hantera anspråks utmanings undantag. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) där [anspråks](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) egenskapen inte är tom.

För att hantera anspråks utmaningen måste du använda `.WithClaim()` metoden i `PublicClientApplicationBuilder` klassen.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP-felkoder 500-600

MSAL.NET implementerar en enkel återförsöks funktion för fel med HTTP-felkoderna 500-600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) -ytor `System.Net.Http.Headers.HttpResponseHeaders` som en egenskap `namedHeaders` . Du kan använda ytterligare information från felkoden för att förbättra tillförlitligheten för dina program. I det fall som beskrivs kan du använda `RetryAfterproperty` (av typen `RetryConditionHeaderValue` ) och beräkna när du vill försöka igen.

Här är ett exempel på ett daemon-program som använder flödet för klientautentiseringsuppgifter. Du kan anpassa detta till någon av metoderna för att förvärva en token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Nästa steg

Överväg att aktivera [loggning i MSAL.net](msal-logging-dotnet.md) för att hjälpa dig att diagnostisera och felsöka problem.
