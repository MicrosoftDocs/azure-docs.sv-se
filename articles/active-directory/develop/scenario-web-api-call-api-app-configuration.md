---
title: 'Konfigurera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er (appens kod konfiguration)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686588"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Ett webb-API som anropar webb-API: er kod konfiguration

När du har registrerat ditt webb-API kan du konfigurera koden för programmet.

Den kod som du använder för att konfigurera ditt webb-API så att det anropar underordnade webb-API: er som bygger på den kod som används för att skydda ett webb-API. Mer information finns i [Protected Web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Microsoft rekommenderar att du använder [Microsoft. Identity. Web NuGet-](https://www.nuget.org/packages/Microsoft.Identity.Web) paketet när du utvecklar ett ASP.net Core skyddat API som anropar underordnade webb-API: er. Se det [skyddade webb-API: kod konfiguration | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) för en snabb presentation av biblioteket i kontexten för ett webb-API.

## <a name="client-secrets-or-client-certificates"></a>Klient hemligheter eller klient certifikat

Under förutsättning att ditt webb-API nu anropar ett underordnat webb-API, anger du en klient hemlighet eller ett klient certifikat i *appsettings.jspå* filen. Du kan också lägga till ett avsnitt som anger:

- URL: en för det underordnade webb-API: et
- De omfattningar som krävs för att anropa API: et

I följande exempel `GraphBeta` anger avsnittet de här inställningarna.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

I stället för en klient hemlighet kan du ange ett klient certifikat. Följande kodfragment visar hur du använder ett certifikat som lagras i Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity. Web tillhandahåller flera olika sätt att beskriva certifikat, både genom konfiguration eller kod. Mer information finns i [Microsoft. Identity. Web wiki – använda certifikat](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) på GitHub.

## <a name="startupcs"></a>Startup.cs

Ditt webb-API måste hämta en token för det underordnade API: et. Du anger det genom att lägga till `.EnableTokenAcquisitionToCallDownstreamApi()` raden efter `.AddMicrosoftIdentityWebApi(Configuration)` . Den här raden visar `ITokenAcquisition` tjänsten som du kan använda i åtgärder för styrenhet/sidor. Men som du ser i de följande två punkterna kan du göra ännu enklare. Du måste också välja en implementation av tokenbaserad cache, till exempel `.AddInMemoryTokenCaches()` i *Start. cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Om du inte vill hämta token själv ger *Microsoft. Identity. Web* två mekanismer för att anropa ett underordnat webb-API från ett annat API. Vilket alternativ du väljer beror på om du vill anropa Microsoft Graph eller något annat API.

### <a name="option-1-call-microsoft-graph"></a>Alternativ 1: anropa Microsoft Graph

Om du vill anropa Microsoft Graph kan du direkt använda `GraphServiceClient` (som exponeras av Microsoft Graph SDK) i dina API-åtgärder med hjälp av Microsoft. Identity. Web. För att exponera Microsoft Graph:

1. Lägg till [Microsoft. Identity. Web. MicrosoftGraph NuGet-](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) paketet i projektet.
1. Lägg till `.AddMicrosoftGraph()` efter `.EnableTokenAcquisitionToCallDownstreamApi()` i filen *startup. cs* . `.AddMicrosoftGraph()` har flera åsidosättningar. Med den åsidosättning som tar ett konfigurations avsnitt som en parameter blir koden:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Alternativ 2: anropa ett underordnat webb-API förutom Microsoft Graph

För att anropa ett underordnat API förutom Microsoft Graph tillhandahåller *Microsoft. Identity. Web* `.AddDownstreamWebApi()` , som begär token och anropar det underordnade webb-API: et.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Precis som med Web Apps kan du välja olika implementationer för cachelagring av token. Mer information finns i [Microsoft Identity Web-token cache-serialisering](https://aka.ms/ms-id-web/token-cache-serialization) på GitHub.

Följande bild visar de olika möjligheterna med *Microsoft. Identity. Web* och deras inverkan på filen *startup. cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blockera diagram som visar tjänst konfigurations alternativ i Start punkt C S för att anropa ett webb-API och ange en implementation av token cache":::

> [!NOTE]
> För att fullständigt förstå kod exemplen här, bör du vara bekant med [ASP.net Core fundament ALS](/aspnet/core/fundamentals), särskilt med [beroende inmatning](/aspnet/core/fundamentals/dependency-injection) och [alternativ](/aspnet/core/fundamentals/configuration/options).

# <a name="java"></a>[Java](#tab/java)

OBO-flödet (on-behalf-of) används för att hämta en token för att anropa det underordnade webb-API: et. I det här flödet tar ditt webb-API emot en Bearer-token med användar delegerade behörigheter från klient programmet och utbyter sedan denna token för en annan åtkomsttoken för att anropa det underordnade webb-API: et.

I koden nedan används våren Security Framework `SecurityContextHolder` i webb-API: et för att hämta den validerade Bearer-token. Den använder sedan MSAL Java-biblioteket för att hämta en token för underordnad API med hjälp av `acquireToken` anropet med `OnBehalfOfParameters` . MSAL cachelagrar token så att efterföljande anrop till API: et kan användas `acquireTokenSilently` för att hämta den cachelagrade token.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

OBO-flödet (on-behalf-of) används för att hämta en token för att anropa det underordnade webb-API: et. I det här flödet tar ditt webb-API emot en Bearer-token med användar delegerade behörigheter från klient programmet och utbyter sedan denna token för en annan åtkomsttoken för att anropa det underordnade webb-API: et.

Ett python-webb-API måste använda vissa mellanprogram för att verifiera Bearer-token som tas emot från klienten. Webb-API: et kan sedan hämta åtkomsttoken för underordnad API med hjälp av MSAL python Library genom att anropa [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metoden. Ett exempel på hur du använder det här API: et finns i [test koden för Microsoft-Authentication-Library-for-python på GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Se även diskussionen om [problem 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) i samma lagrings plats för en metod som kringgår behovet av ett program på mellan nivå.

Du kan också se ett exempel på OBO Flow-implementeringen i exemplet [MS-Identity-python-on-of-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) .

---

Du kan också se ett exempel på OBO Flow-implementering i [Node.js och Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

Mer information om OBO-protokollet finns i [Microsoft Identity Platform och OAuth 2,0 on-of-Flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot och [Hämta en token för appen](scenario-web-api-call-api-acquire-token.md).
