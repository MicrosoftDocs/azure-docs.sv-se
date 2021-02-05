---
title: 'Flytta Desktop-appen som anropar webb-API: er till produktion | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du flyttar en stationär app som anropar webb-API: er till produktion'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22e61ea767d781dc9da54d61143c1b2524e06e94
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584422"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Stationär app som anropar webb-API: er: flytta till produktion

I den här artikeln får du lära dig hur du flyttar din Desktop-app som anropar webb-API: er till produktion.

## <a name="handle-errors-in-desktop-applications"></a>Hantera fel i Skriv bords program

I de olika flödena har du lärt dig hur du hanterar felen för de tysta flödena, som du ser i kodfragmenten. Du har också sett att det finns fall där interaktion krävs, som i stegvisa medgivande och villkorlig åtkomst.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Låt användaren godkännas framför flera resurser

> [!NOTE]
> Att få ett medgivande för flera resurser fungerar för Microsoft Identity Platform men inte för Azure Active Directory (Azure AD) B2C. Azure AD B2C stöder endast administrativt godkännande, inte användar medgivande.

Du kan inte hämta en token för flera resurser samtidigt med Microsoft Identity Platform. `scopes`Parametern får bara innehålla omfång för en enda resurs. Du kan se till att användaren i förväg samtycka till flera resurser genom att använda- `extraScopesToConsent` parametern.

Du kan till exempel ha två resurser som har två omfång:

- `https://mytenant.onmicrosoft.com/customerapi` med omfattningar `customer.read` och `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` med omfattningar `vendor.read` och `vendor.write`

I det här exemplet använder du `.WithExtraScopesToConsent` modifieraren som har `extraScopesToConsent` parametern.

Till exempel:

### <a name="in-msalnet"></a>I MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Införliva

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Det här anropet får du en åtkomsttoken för det första webb-API: et.

Anropa API: et när du anropar det andra webb-API: `AcquireTokenSilent` et.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsoft personal Account kräver godkännande varje gången appen körs

För användare av Microsofts personliga konto uppmanas du att ange medgivande för varje intern klient (stationär eller mobilapp) anrop till auktoriseran. Intern klient identitet är insäker, vilket strider mot konfidentiell klient program identitet. Konfidentiella klient program utbyter en hemlighet med Microsofts identitets plattform för att bevisa sin identitet. Microsoft Identity Platform valde att minimera den här insäkerheten för konsument tjänster genom att användaren uppmanas att ange medgivande varje gången programmet auktoriseras.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du provar ytterligare exempel finns i [Desktop och mobila offentliga klient program](sample-v2-code.md#desktop-and-mobile-public-client-apps).



