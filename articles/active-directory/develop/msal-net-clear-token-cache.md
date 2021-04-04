---
title: Rensa token-cachen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du rensar token-cachen med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064767"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Rensa token-cachen med MSAL.NET

När du [hämtar en](msal-acquire-cache-tokens.md) åtkomsttoken med hjälp av Microsoft Authentication Library för .net (MSAL.net) cachelagras token. När programmet behöver en token ska det först anropa `AcquireTokenSilent` metoden för att kontrol lera om en acceptabel token finns i cacheminnet. 

Att rensa cacheminnet uppnås genom att ta bort kontona från cachen. Detta tar inte bort sessions-cookien som finns i webbläsaren, men.  I följande exempel instansieras ett offentligt klient program, hämtar konton för programmet och tar bort kontona.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Läs mer om att förvärva och cachelagra token genom att läsa [Hämta en](msal-acquire-cache-tokens.md)åtkomsttoken.
