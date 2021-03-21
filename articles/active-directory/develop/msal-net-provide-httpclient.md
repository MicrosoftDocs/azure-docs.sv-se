---
title: Ange en HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tillhandahålla din egen HttpClient och proxy för att ansluta till Azure AD med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064631"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Ange din egen HttpClient och proxy med MSAL.NET
När du [initierar ett offentligt klient program](msal-net-initializing-client-applications.md)kan du använda `.WithHttpClientFactory method` för att ange din egen httpclient.  Genom att tillhandahålla dina egna HttpClient kan du använda avancerade scenarier för att få en begränsad kontroll av en HTTP-proxy, anpassa användar agentens huvuden eller tvinga MSAL att använda en viss HttpClient (till exempel i ASP.NET Core Web Apps/API: er).

## <a name="initialize-with-httpclientfactory"></a>Initiera med HttpClientFactory
I följande exempel visas hur du skapar en `HttpClientFactory` och sedan initierar ett offentligt klient program med det:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient och Xamarin iOS
När du använder Xamarin iOS rekommenderar vi att du skapar en `HttpClient` som uttryckligen använder den `NSURLSession` -baserade hanteraren för iOS 7 och senare. MSAL.NET skapar automatiskt en `HttpClient` som använder `NSURLSessionHandler` för iOS 7 och senare. Mer information finns i [Xamarin iOS-dokumentationen för httpclient](/xamarin/cross-platform/macios/http-stack).