---
title: 'Migrera OWIN-baserade webb-API: er till b2clogin.com eller en anpassad domän'
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar ett .NET-webb-API för att stödja tokens som utfärdats av flera token utfärdare när du migrerar dina program till b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ab93a8742d210969ef6ad10683bed92221c1a57
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256709"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>Migrera ett OWIN webb-API till b2clogin.com eller en anpassad domän

Den här artikeln beskriver en teknik för att aktivera stöd för flera token utfärdare i webb-API: er som implementerar [Open Web Interface för .net (OWIN)](http://owin.org/). Stöd för flera token-slutpunkter är användbart när du migrerar Azure Active Directory B2C-API: er (Azure AD B2C) och deras program från en domän till en annan. Till exempel från *login.microsoftonline.com* till *b2clogin.com* eller till en [anpassad domän](custom-domain.md).

Genom att lägga till stöd i ditt API för att acceptera token som utfärdats av b2clogin.com, login.microsoftonline.com eller en anpassad domän, kan du migrera dina webb program på ett stegvist sätt innan du tar bort stöd för login.microsoftonline.com utfärdade token från API: et.

I följande avsnitt finns ett exempel på hur du aktiverar flera utfärdare i ett webb-API som använder [Microsoft OWIN][katana] -komponenter för mellanprogram (Katana). Även om kod exemplen är speciella för Microsoft OWIN mellanprogram, bör den allmänna tekniken vara tillämplig på andra OWIN-bibliotek.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande Azure AD B2C resurser på plats innan du fortsätter med stegen i den här artikeln:

* [Användar flöden](tutorial-create-user-flows.md?pivots=b2c-user-flow) eller [anpassade principer](tutorial-create-user-flows.md?pivots=b2c-custom-policy) som skapats i din klient organisation

## <a name="get-token-issuer-endpoints"></a>Hämta token för utfärdarens slut punkter

Du måste först hämta slut punkts-URI: er för token för varje utfärdare som du vill stödja i ditt API. Använd följande procedur i Azure Portal för att hämta de *b2clogin.com* -och *login.microsoftonline.com* -slutpunkter som stöds av din Azure AD B2C klient.

Börja med att välja ett befintligt användar flöde:

1. Navigera till din Azure AD B2C-klient i [Azure Portal](https://portal.azure.com)
1. Under **principer** väljer du **användar flöden (principer)**
1. Välj en befintlig princip, till exempel *B2C_1_signupsignin1*, och välj sedan **Kör användar flöde**
1. Under rubriken **Kör användar flöde** längst upp på sidan väljer du hyperlänken för att navigera till OpenID Connect Discovery-slutpunkten för det användar flödet.

    ![Välkänd URI-hyperlänk på sidan kör nu i Azure Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Registrera värdet på sidan som öppnas i webbläsaren, `issuer` till exempel:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Använd List rutan **Välj domän** för att välja den andra domänen och utför sedan de föregående två stegen igen och registrera dess `issuer` värde.

Du bör nu ha två registrerade URI: er som liknar:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Anpassade principer

Om du har anpassade principer i stället för användar flöden kan du använda en liknande process för att hämta utfärdar-URI: er.

1. Navigera till din Azure AD B2C-klient
1. Välj **ramverk för identitets upplevelse**
1. Välj en av principerna för förlitande part, till exempel *B2C_1A_signup_signin*
1. Använd List rutan **Välj domän** för att välja en domän, till exempel *yourtenant.b2clogin.com*
1. Välj den hyperlänk som visas under **OpenID Connect Discovery-slutpunkt**
1. Registrera `issuer` värdet
1. Utför stegen 4-6 för den andra domänen, till exempel *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Hämta exempelkoden

Nu när du har båda URI: erna för token för token måste du uppdatera koden för att ange att båda slut punkterna är giltiga utfärdare. Du kan gå igenom ett exempel genom att ladda ned eller klona exempel programmet och sedan uppdatera exemplet så att det stöder båda slut punkterna som giltiga utfärdare.

Hämta arkivet: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Aktivera flera utfärdare i webb-API

I det här avsnittet uppdaterar du koden för att ange att både token utfärdarens slut punkter är giltiga.

1. Öppna lösningen **B2C-WebAPI-dotNet. SLN** i Visual Studio
1. I **TaskService** -projektet öppnar du filen *TaskService \\ App_Start \\ * * startup. auth. cs** * i redigeraren
1. Lägg till följande `using` direktiv överst i filen:

    `using System.Collections.Generic;`
1. Lägg till [`ValidIssuers`][validissuers] egenskapen i [`TokenValidationParameters`][tokenvalidationparameters] definitionen och ange båda URI: er som du registrerade i föregående avsnitt:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` tillhandahålls av MSAL.NET och används av OWIN mellanprogram i nästa avsnitt av kod i *Start. auth. cs*. När flera giltiga utfärdare har angetts görs OWIN program pipelinen medveten om att båda token-slutpunkterna är giltiga utfärdare.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Som tidigare nämnts tillhandahåller andra OWIN-bibliotek vanligt vis en liknande funktion för att stödja flera utfärdare. Även om det finns exempel för varje bibliotek som ligger utanför omfånget för den här artikeln, kan du använda en liknande teknik för de flesta bibliotek.

## <a name="switch-endpoints-in-web-app"></a>Växla slut punkter i webb program

Med båda URI: erna som stöds av ditt webb-API, behöver du nu uppdatera webb programmet så att det hämtar tokens från b2clogin.com-slutpunkten.

Du kan till exempel konfigurera exempel webb programmet att använda den nya slut punkten genom att ändra `ida:AadInstance` värdet i *filen TaskWebApp \\ **Web.config** _ _* TaskWebApp * * Project.

Ändra `ida:AadInstance` värdet i *Web.config* av TaskWebApp så att det refererar till `{your-b2c-tenant-name}.b2clogin.com` i stället för `login.microsoftonline.com` .

Innan:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Efter (Ersätt `{your-b2c-tenant}` med namnet på din B2C-klient):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

När slut punkts strängarna skapas när webbappen körs, används b2clogin.com-baserade slut punkter när den begär token.

När du använder en anpassad domän:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln presenteras en metod för att konfigurera ett webb-API som implementerar Microsoft OWIN mellanprogram (Katana) för att acceptera tokens från flera utfärdares slut punkter. Som du kanske märker finns det flera andra strängar i *Web.Config* filer för både TaskService-och TaskWebApp-projekt som skulle behöva ändras om du vill skapa och köra dessa projekt mot din egen klient. Du är välkommen att ändra projekten på rätt sätt om du vill se dem i praktiken, men en fullständig genom gång av detta ligger utanför omfånget för den här artikeln.

Mer information om de olika typerna av säkerhetstoken som genereras av Azure AD B2C finns i [Översikt över tokens i Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
