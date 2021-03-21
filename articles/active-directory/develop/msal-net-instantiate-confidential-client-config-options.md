---
title: Instansiera en konfidentiell klient app (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du instansierar ett konfidentiellt klient program med konfigurations alternativ med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583950"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instansiera ett konfidentiellt klient program med konfigurations alternativ med MSAL.NET

Den här artikeln beskriver hur du instansierar ett [konfidentiellt klient program](msal-client-applications.md) med hjälp av Microsoft Authentication Library för .net (MSAL.net).  Programmet instansieras med konfigurations alternativ som definierats i en inställnings fil.

Innan du initierar ett program måste du först [Registrera](quickstart-register-app.md) det så att din app kan integreras med Microsoft Identity Platform. Efter registreringen kan du behöva följande information (som du hittar i Azure Portal):

- Klient-ID (en sträng som representerar ett GUID)
- Identitets leverantörens URL (kallas instansen) och inloggnings gruppen för ditt program. Dessa två parametrar kallas gemensamt för utfärdaren.
- Klient-ID: t om du skriver ett affärs program enbart för din organisation (även kallat program med en enda klient).
- Program hemligheten (klientens hemliga sträng) eller certifikat (av typen X509Certificate2) om det är ett konfidentiellt klient program.
- För webbappar, och ibland för offentliga klient program (särskilt när appen behöver använda en Service Broker), har du också angett redirectUri där identitets leverantören ska kontakta ditt program igen med säkerhetstoken.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurera programmet från konfigurations filen
Namnet på egenskaperna för alternativen i MSAL.NET matchar namnet på egenskaperna för `AzureADOptions` i ASP.net Core, så du behöver inte skriva någon LIMS kod.

En ASP.NET Core program konfiguration beskrivs i en *appsettings.jspå* filen:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Från och med MSAL.NET v3. x kan du konfigurera ditt konfidentiella klient program från konfigurations filen.

I den klass där du vill konfigurera och instansiera ditt program, deklarera ett `ConfidentialClientApplicationOptions` objekt.  Bind konfigurationen från källan (inklusive appconfig.jspå fil) till instansen av program alternativen med hjälp av `IConfigurationRoot.Bind()` metoden från [Microsoft.Extensions.Configuration. Binder NuGet-paket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Detta gör att innehållet i avsnittet "AzureAD" i *appsettings.jspå* filen binds till motsvarande egenskaper för `ConfidentialClientApplicationOptions` objektet.  Bygg sedan ett `ConfidentialClientApplication` objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Lägg till körnings konfiguration
I ett konfidentiellt klient program har du vanligt vis en cache per användare. Därför måste du hämta cacheminnet som är associerat med användaren och informera det program verktyg som du vill använda. På samma sätt kan du ha en dynamiskt Beräknad omdirigerings-URI. I det här fallet är koden följande:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

