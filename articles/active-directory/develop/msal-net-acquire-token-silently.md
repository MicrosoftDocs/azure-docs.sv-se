---
title: Hämta en token från cachen (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hämtar en åtkomsttoken tyst (från token cache) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 94e4a4d5b80e246ce822e977deafe5c41c9ff4ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064784"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Hämta en token från token-cachen med MSAL.NET

När du hämtar en åtkomsttoken med hjälp av Microsoft Authentication Library för .NET (MSAL.NET) cachelagras token. När programmet behöver en token ska det först anropa `AcquireTokenSilent` metoden för att kontrol lera om en acceptabel token finns i cacheminnet. I många fall är det möjligt att förvärva en annan token med fler omfång baserade på en token i cachen. Det är också möjligt att uppdatera en token när den snart upphör att gälla (eftersom token cache också innehåller en uppdateringstoken).

Det rekommenderade mönstret är att anropa `AcquireTokenSilent` metoden först.  Om `AcquireTokenSilent` det Miss lyckas kan du hämta en token med andra metoder.

I följande exempel försöker programmet först hämta en token från token-cachen.  Om ett `MsalUiRequiredException` undantag genereras, kommer programmet att förvärva en token interaktivt. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
