---
title: Konfigurera skyddade webb-API-appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API och konfigurerar programmets kod.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9ffb77db4f7bcd5a07e25085eed17e8972aa9a33
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683767"
---
# <a name="protected-web-api-code-configuration"></a>Skyddat webb-API: kod konfiguration

Om du vill konfigurera koden för ditt skyddade webb-API måste du förstå:

- Vad definierar API: er som skyddade.
- Så här konfigurerar du en Bearer-token.
- Verifiera token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Vad definierar ASP.NET och ASP.NET Core API: er som skyddade?

Precis som Web Apps, är ASP.NET-och ASP.NET Core webb-API: erna skyddade eftersom deras styrenhets åtgärder föregås av attributet **[auktorisera]** . Styrenhets åtgärder kan endast anropas om API: et anropas med en auktoriserad identitet.

Överväg följande frågor:

- Endast en app kan anropa ett webb-API. Hur känner API: et till identiteten för den app som anropar den?
- Om appen anropar API: et för en användares räkning, vad är användarens identitet?

## <a name="bearer-token"></a>Bearer-token

Bearer-token som anges i rubriken när appen kallas innehåller information om appens identitet. Den innehåller också information om användaren, om inte webbappen accepterar tjänst-till-tjänst-anrop från en daemon-app.

Här är ett C#-kod exempel som visar en klient som anropar API: et när den har skaffat en token med Microsoft Authentication Library för .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Ett klient program begär Bearer-token till Microsoft Identity Platform-slutpunkten *för webb-API: et*. Webb-API är det enda program som ska verifiera token och Visa de anspråk som den innehåller. Klient program bör aldrig försöka inspektera anspråk i tokens.
>
> I framtiden kan webb-API: et kräva att token krypteras. Detta krav förhindrar åtkomst för klient program som kan visa åtkomsttoken.

## <a name="jwtbearer-configuration"></a>JwtBearer-konfiguration

I det här avsnittet beskrivs hur du konfigurerar en Bearer-token.

### <a name="config-file"></a>Konfigurations fil

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Om du använde en anpassad app-ID-URI för ditt webb-API

Om du har accepterat app-ID-URI: n som föreslås av appens registrerings Portal behöver du inte ange mål gruppen (se [program-ID-URI och omfattningar](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). Annars bör du lägga till en `Audience` egenskap vars värde är app-ID-URI: n för ditt webb-API.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Kod initiering

När en app anropas på en styrenhets åtgärd som innehåller ett **[auktorisera]** -attribut, ASP.NET och ASP.net Core extrahera åtkomsttoken från Authorization-huvudets Bearer-token. Åtkomsttoken vidarebefordras sedan till JwtBearer mellanprogram, som anropar Microsoft IdentityModel-tillägg för .NET.

#### <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Microsoft rekommenderar att du använder [Microsoft. Identity. Web NuGet-](https://www.nuget.org/packages/Microsoft.Identity.Web) paketet när du utvecklar ett webb-API med ASP.net Core.

_Microsoft. Identity. Web_ ger ett lim mellan ASP.net Core, autentisering mellanprogram och [Microsoft Authentication Library (MSAL)](msal-overview.md) för .net. Det ger en tydligare och mer robust utvecklare och utnyttjar kraften hos Microsoft Identity Platform och Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Använda Microsoft. Identity. Web templates

Du kan skapa ett webb-API från grunden med hjälp av Microsoft. Identity. Web Project-mallar. Mer information finns i [projekt mal len Microsoft. Identity. Web-Web-API](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Från ett befintligt ASP.NET Core 3,1-program

I dag ASP.NET Core 3,1 använder biblioteket Microsoft. AspNetCore. AzureAD. UI. Mellanprogram initieras i Startup.cs-filen.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Mellanprogram läggs till i webb-API: et med den här instruktionen:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 För närvarande skapar ASP.NET Core mallar Azure Active Directory (Azure AD) webb-API: er som loggar in användare i din organisation eller i en organisation. De loggar inte in användare med personliga konton. Du kan dock ändra mallarna till att använda Microsoft Identity Platform-slutpunkten genom att använda [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) och ersätta koden i *startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

Du kan också skriva följande (vilket är motsvarande)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Om du använder Microsoft. identitet. Web och inte anger `Audience` i *appsettings.jspå*, används följande:
> -  `$"{ClientId}"` Om du har angett [åtkomst-token godkänd version](scenario-protected-web-api-app-registration.md#accepted-token-version) till `2` , eller för Azure AD B2C webb-API: er.
> - `$"api://{ClientId}` i alla andra fall (för v 1.0- [åtkomsttoken](access-tokens.md)).
> Mer information finns i Microsoft. Identity. Web [Source Code](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83).

Föregående kodfragment extraheras från den [ASP.net Core stegvisa självstudien för webb-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). Informationen om **AddMicrosoftIdentityWebApiAuthentication** finns i [Microsoft. Identity. Web](microsoft-identity-web.md). Den här metoden anropar [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?preserve-view=true&view=azure-dotnet-preview), som i sin tur instruerar mellanprogram för att validera token.

## <a name="token-validation"></a>Verifiering av token

I föregående kodfragment kontrollerar JwtBearer mellan program varan, t. ex. OpenID Connect mellan program i webbappar, token baserat på värdet för `TokenValidationParameters` . Token dekrypteras vid behov, anspråken extraheras och signaturen verifieras. Mellanprogram validerar sedan token genom att söka efter dessa data:

- Mål grupp: token är avsedd för webb-API: et.
- Sub: det har utfärdats för en app som har tillåtelse att anropa webb-API: et.
- Utfärdare: den har utfärdats av en betrodd säkerhetstokentjänst (STS).
- Expires: livs längden är i intervallet.
- Signatur: den har inte manipulerats.

Det kan också finnas särskilda valideringar. Det är till exempel möjligt att verifiera att signerings nycklar, när de bäddas in i en token, är betrodda och att token inte spelas upp igen. Slutligen kräver vissa protokoll vissa valideringar.

### <a name="validators"></a>Verifierare

Validerings stegen samlas in i verifierare, som tillhandahålls av [Microsoft IdentityModel-tilläggen för .net med](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) öppen källkod. Verifierarna definieras i biblioteks käll filen [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

I den här tabellen beskrivs verifierarna:

| Systemhälsoverifierare | Description |
|---------|---------|
| **ValidateAudience** | Säkerställer att token är för programmet som validerar token åt dig. |
| **ValidateIssuer** | Säkerställer att token utfärdats av en betrodd STS, vilket innebär att det är från någon som du litar på. |
| **ValidateIssuerSigningKey** | Säkerställer att programmet validerar token-förtroendet för den nyckel som användes för att signera token. Det finns ett särskilt fall där nyckeln är inbäddad i token. Men det här fallet uppstår vanligt vis inte. |
| **ValidateLifetime** | Säkerställer att token fortfarande är giltig eller redan är giltig. Verifieraren kontrollerar om livs längden för token är inom det intervall som anges av **notBefore** och **upphör att gälla** . |
| **ValidateSignature** | Säkerställer att token inte har manipulerats. |
| **ValidateTokenReplay** | Säkerställer att token inte spelas upp. Det finns särskilda fall för vissa Databasmigrering-protokoll. |

#### <a name="customizing-token-validation"></a>Anpassa verifiering av token

Verifierarna är kopplade till egenskaperna för klassen **TokenValidationParameters** . Egenskaperna initieras från ASP.NET och ASP.NET Core konfiguration.

I de flesta fall behöver du inte ändra parametrarna. Appar som inte är enskilda klienter är undantag. Dessa webbappar godkänner användare från vilken organisation som helst eller från personliga Microsoft-konton. Utfärdare i det här fallet måste val IDE ras. Microsoft. Identity. Web tar hand om verifiering av utfärdaren. Mer information finns i Microsoft. Identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

I ASP.NET Core, om du vill anpassa parametrarna för token-validering, använder du följande kod avsnitt i din *startup.cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Följande kod exempel visar hur du gör en anpassad token-verifiering för ASP.NET MVC:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Verifiering av token i Azure Functions

Du kan också validera inkommande åtkomsttoken i Azure Functions. Du hittar exempel på sådan validering i följande kod exempel på GitHub:

- .NET: [Azure-samples/MS-Identity-dotNet-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-samples/MS-Identity-NodeJS-WebAPI-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-samples/MS-Identity-python-WebAPI-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [kontrol lera omfattningarna och app-rollerna i din kod](scenario-protected-web-api-verification-scope-app-roles.md).