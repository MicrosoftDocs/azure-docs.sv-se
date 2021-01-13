---
title: Hantera användare med Microsoft Graph API
titleSuffix: Azure AD B2C
description: Så här hanterar du användare i en Azure AD B2C klient genom att anropa Microsoft Graph API och använda en program identitet för att automatisera processen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178882"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Hantera Azure AD B2C användar konton med Microsoft Graph

Med Microsoft Graph kan du hantera användar konton i din Azure AD B2C katalog genom att tillhandahålla metoder för att skapa, läsa, uppdatera och ta bort i Microsoft Graph API. Du kan migrera ett befintligt användar arkiv till en Azure AD B2C-klient och utföra andra åtgärder för användar konto hantering genom att anropa Microsoft Graph-API: et.

I avsnitten nedan visas viktiga aspekter av Azure AD B2C användar hantering med Microsoft Graph-API: et. Microsoft Graph API-åtgärder, typer och egenskaper som visas här är en del av den som visas i referens dokumentationen för Microsoft Graph API.

## <a name="register-a-management-application"></a>Registrera ett hanterings program

Innan ett användar hanterings program eller skript som du skriver kan interagera med resurserna i din Azure AD B2C klient, behöver du en program registrering som ger behörighet att göra detta.

Följ stegen i den här instruktions artikeln för att skapa en program registrering som hanterings programmet kan använda:

[Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph åtgärder för användar hantering

Följande användar hanterings åtgärder är tillgängliga i [Microsoft Graph API](/graph/api/resources/user):

- [Hämta en lista över användare](/graph/api/user-list)
- [Skapa en användare](/graph/api/user-post-users)
- [Hämta en användare](/graph/api/user-get)
- [Uppdatera en användare](/graph/api/user-update)
- [Ta bort en användare](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Kod exempel: program mässigt hantera användar konton

Det här kod exemplet är ett .NET Core-konsolprogram som använder [Microsoft Graph SDK](/graph/sdks/sdks-overview) för att interagera med Microsoft Graph API. Koden visar hur du anropar API: et för att hantera användare program mässigt i en Azure AD B2C klient organisation.
Du kan [Ladda ned exempel arkivet](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [Bläddra i lagrings platsen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) på GitHub eller klona lagrings platsen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

När du har fått kod exemplet konfigurerar du det för din miljö och skapar sedan projektet:

1. Öppna projektet i [Visual Studio](https://visualstudio.microsoft.com) eller [Visual Studio Code](https://code.visualstudio.com).
1. Öppna `src/appsettings.json`.
1. I `appSettings` avsnittet ersätter du `your-b2c-tenant` med namnet på din klient och `Application (client) ID` och `Client secret` med värdena för registreringen av hanterings programmet (se avsnittet [Registrera ett hanterings program](#register-a-management-application) i den här artikeln).
1. Öppna ett konsol fönster i din lokala klon av lagrings platsen, växla till `src` katalogen och skapa projektet:
    ```console
    cd src
    dotnet build
    ```
1. Kör programmet med `dotnet` kommandot:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Programmet visar en lista över kommandon som du kan köra. Du kan till exempel hämta alla användare, hämta en enskild användare, ta bort en användare, uppdatera en användares lösen ord och Mass import.

### <a name="code-discussion"></a>Kod diskussion

Exempel koden använder [Microsoft Graph SDK](/graph/sdks/sdks-overview), som är utformad för att förenkla skapandet av högkvalitativa, effektiva och elastiska program som har åtkomst till Microsoft Graph.

Alla begär anden till Microsoft Graph API kräver en åtkomsttoken för autentisering. Lösningen använder sig av [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet-paketet som tillhandahåller ett gränssnitts scenario baserat på Microsoft Authentication Library (MSAL) för användning med Microsoft Graph SDK.

`RunAsync`Metoden i _program.cs_ -filen:

1. Läser program inställningar från _appsettings.jspå_ filen
1. Initierar auth-providern med hjälp av [OAuth 2,0-klientens autentiseringsuppgifter för tilldelnings](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) flöde. Med hjälp av flödet för tilldelning av klientautentiseringsuppgifter kan appen Hämta en åtkomsttoken för att anropa Microsoft Graph-API: et.
1. Konfigurerar Microsoft Graph tjänst klienten med auth-providern:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Den initierade *GraphServiceClient* används sedan i _UserService.cs_ för att utföra användar hanterings åtgärder. Du kan till exempel hämta en lista över användar kontona i klienten:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Gör API-anrop med hjälp av Microsoft Graph SDK: er](/graph/sdks/create-requests) innehåller information om hur du läser och skriver information från Microsoft Graph, använder `$select` för att kontrol lera de egenskaper som returneras, tillhandahålla anpassade frågeparametrar och använda `$filter` `$orderBy` parametrarna och.

## <a name="next-steps"></a>Nästa steg

Ett fullständigt index för de Microsoft Graph API-åtgärder som stöds för Azure AD B2C resurser finns i [Microsoft Graph åtgärder som är tillgängliga för Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
