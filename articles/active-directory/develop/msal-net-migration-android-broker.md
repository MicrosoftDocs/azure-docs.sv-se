---
title: Migrera Xamarin Android-appar med hjälp av utjämnare till MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar Xamarin Android-appar som använder Microsoft Authenticator eller Intune-företagsportal från ADAL.NET till MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206677"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Migrera Android-program som använder en Broker från ADAL.NET till MSAL.NET

Om du använder en Xamarin Android-app för närvarande med Azure Active Directory Authentication Library för .NET (ADAL.NET) och en [autentiseringstjänst](msal-android-single-sign-on.md), är det dags att migrera till [Microsoft Authentication Library för .net ](msal-overview.md) (MSAL.net).

## <a name="prerequisites"></a>Förutsättningar

* En Xamarin Android-app är redan integrerad med en Service Broker ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) eller [Intune-företagsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) och ADAL.net som du behöver migrera till MSAL.net.

## <a name="step-1-enable-the-broker"></a>Steg 1: aktivera Service Broker

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
I ADAL.NET aktive ras stöd för Broker per autentisering.

Om du vill anropa Service Broker måste du ange en `useBroker` till *True* i `PlatformParameters` konstruktorn:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

I den plattformsspecifika sid åter givnings koden för Android ställer du in `useBroker` flaggan på sant:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Ta sedan med parametrarna i anropet för inhämta token:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
I MSAL.NET aktive ras stöd för service nivå per PublicClientApplication.

Använd `WithBroker()` parametern (som har angetts till sant som standard) för att anropa Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

I AcquireToken-anropet:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Steg 2: Ange en aktivitet

I ADAL.NET skickades en aktivitet (vanligt vis MainActivity) som en del av PlatformParameters som visas i [steg 1: aktivera Service Broker](#step-1-enable-the-broker).

MSAL.NET använder också en aktivitet, men det krävs inte i vanlig Android-användning utan någon Broker. Om du vill använda Broker ställer du in aktiviteten för att skicka och ta emot svar från Broker.

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
Aktiviteten skickas till PlatformParameters i den Android-speciella plattformen.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

I MSAL.NET gör du två saker för att ställa in aktiviteten för Android:

1. I ställer du in på för att se till att `MainActivity.cs` `App.RootViewController` `MainActivity` det finns en aktivitet med anropet till Broker.

    Om den inte har angetts korrekt kan du få följande fel meddelande: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Använd `.WithParentActivityOrWindow(App.RootViewController)` och släpp i referensen till den aktivitet som du vill använda på AcquireTokenInteractive-anropet. I det här exemplet används MainActivity.

**Exempel:**

I *app. cs*:

```CSharp
   public static object RootViewController { get; set; }
```

I *MainActivity. cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

I AcquireToken-anropet:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Nästa steg

Mer information om Android-särskilda överväganden när du använder MSAL.NET med Xamarin finns i [konfigurations krav och fel söknings tips för Xamarin Android med MSAL.net](msal-net-xamarin-android-considerations.md).