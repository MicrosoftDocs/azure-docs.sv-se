---
title: Hantera resurser med Microsoft Graph
titleSuffix: Azure AD B2C
description: Hur du hanterar resurser i en Azure AD B2C-klientorganisation genom att anropa Microsoft Graph-API:et och använda en programidentitet för att automatisera processen.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 884cb0c30bc754366fda79a4b54b977517fbadd3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530541"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Hantera Azure AD B2C med Microsoft Graph

Microsoft Graph kan du hantera resurser i din Azure AD B2C katalog. Följande API Microsoft Graph åtgärder stöds för hantering av Azure AD B2C resurser, inklusive användare, identitetsproviders, användarflöden, anpassade principer och principnycklar. Varje länk i följande avsnitt riktar in sig på motsvarande sida i Microsoft Graph API-referens för den åtgärden. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda MS Graph API och interagera med resurser i din Azure AD B2C klientorganisation behöver du en programregistrering som ger behörighet att göra det. Följ stegen i artikeln [Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md) för att skapa en programregistrering som hanteringsprogrammet kan använda. 

## <a name="user-management"></a>Användarhantering

- [Visa användare](/graph/api/user-list)
- [Skapa en konsumentanvändare](/graph/api/user-post-users)
- [Hämta en användare](/graph/api/user-get)
- [Uppdatera en användare](/graph/api/user-update)
- [Ta bort en användare](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Hantering av användarens telefonnummer (beta)

Ett telefonnummer som kan användas av en användare för att logga in med SMS eller [röstsamtal](identity-provider-local.md#phone-sign-in-preview)eller [multifaktorautentisering.](multi-factor-authentication.md) Mer information finns i [API för Azure AD-autentiseringsmetoder.](/graph/api/resources/phoneauthenticationmethod)

- [Lägg till](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Hämta](/graph/api/phoneauthenticationmethod-get)
- [Uppdatera](/graph/api/phoneauthenticationmethod-update)
- [Ta bort](/graph/api/phoneauthenticationmethod-delete)

Observera att [liståtgärden](/graph/api/authentication-list-phonemethods) endast returnerar aktiverade telefonnummer. Följande telefonnummer ska vara aktiverat för användning med liståtgärderna. 

![Aktivera telefon inloggning](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-postadress för lösenordsåterställning via självbetjäning (beta)

En e-postadress som kan användas av ett [inloggningskonto för användarnamn för](identity-provider-local.md#username-sign-in) att återställa lösenordet. Mer information finns i [API för Azure AD-autentiseringsmetoder.](/graph/api/resources/emailauthenticationmethod)

- [Lägg till](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Hämta](/graph/api/emailauthenticationmethod-get)
- [Uppdatera](/graph/api/emailauthenticationmethod-update)
- [Ta bort](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitetsprovidrar

Hantera de [identitetsproviders](add-identity-provider.md) som är tillgängliga för dina användarflöden i din Azure AD B2C klientorganisation.

- [Lista identitetsproviders som registrerats Azure AD B2C klientorganisationen](/graph/api/identityprovider-list)
- [Skapa en identitetsprovider](/graph/api/identityprovider-post-identityproviders)
- [Hämta en identitetsprovider](/graph/api/identityprovider-get)
- [Uppdatera identitetsprovider](/graph/api/identityprovider-update)
- [Ta bort en identitetsprovider](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Användarflöde

Konfigurera förbyggda principer för registrering, inloggning, kombinerad registrering och inloggning, lösenordsåterställning och profiluppdatering.

- [Visa en lista över användarflöden](/graph/api/identitycontainer-list-b2cuserflows)
- [Skapa ett användarflöde](/graph/api/identitycontainer-post-b2cuserflows)
- [Hämta ett användarflöde](/graph/api/b2cidentityuserflow-get)
- [Ta bort ett användarflöde](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Autentiseringsmetoder för användarflöde (beta)

Välj en mekanism för att låta användare registrera via lokala konton. Lokala konton är de konton där Azure AD gör identitetspåståendet. Mer information finns i [resurstypen b2cAuthenticationMethodsPolicy.](/graph/api/resources/b2cauthenticationmethodspolicy)

- [Hämta](/graph/api/b2cauthenticationmethodspolicy-get)
- [Uppdatera](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Anpassade principer

Med följande åtgärder kan du hantera dina Azure AD B2C Trust Framework-principer, så kallade [anpassade principer.](custom-policy-overview.md)

- [Lista alla principer för förtroenderamverk som konfigurerats i en klientorganisation](/graph/api/trustframework-list-trustframeworkpolicies)
- [Skapa förtroenderamverksprincip](/graph/api/trustframework-post-trustframeworkpolicy)
- [Läsa egenskaper för en befintlig förtroenderamverksprincip](/graph/api/trustframeworkpolicy-get)
- [Uppdatera eller skapa förtroenderamverksprincip.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Ta bort en befintlig princip för förtroenderamverk](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Principnycklar

Den Identity Experience Framework lagrar hemligheterna som refereras i en anpassad princip för att upprätta förtroende mellan komponenter. Dessa hemligheter kan vara symmetriska eller asymmetriska nycklar/värden. I Azure Portal visas dessa entiteter som **Principnycklar**.

Resursen på den översta nivån för principnycklar i MICROSOFT GRAPH-API:et är [Trusted Framework Keyset](/graph/api/resources/trustframeworkkeyset). Varje **nyckeluppsättning** innehåller minst en **nyckel.** Skapa en nyckel genom att först skapa en tom nyckeluppsättning och sedan generera en nyckel i nyckeluppsättningen. Du kan skapa en manuell hemlighet, ladda upp ett certifikat eller en PKCS12-nyckel. Nyckeln kan vara en genererad hemlighet, en sträng (till exempel Facebook-programhemligheten) eller ett certifikat som du laddar upp. Om en nyckeluppsättning har flera nycklar är bara en av nycklarna aktiv.

### <a name="trust-framework-policy-keyset"></a>Principnycklar för förtroenderamverk

- [Lista nyckeluppsättningar för förtroenderamverk](/graph/api/trustframework-list-keysets)
- [Skapa en nyckeluppsättningar för förtroenderamverk](/graph/api/trustframework-post-keysets)
- [Hämta en nyckeluppsättning](/graph/api/trustframeworkkeyset-get)
- [Uppdatera nyckeluppsättningar för förtroenderamverk](/graph/api/trustframeworkkeyset-update)
- [Ta bort en nyckeluppsättningar för förtroenderamverk](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Principnyckel för förtroenderamverk

- [Hämta den aktiva nyckeln i nyckeluppsättningen](/graph/api/trustframeworkkeyset-getactivekey)
- [Generera en nyckel i nyckeluppsättningen](/graph/api/trustframeworkkeyset-generatekey)
- [Ladda upp en strängbaserad hemlighet](/graph/api/trustframeworkkeyset-uploadsecret)
- [Ladda upp ett X.509-certifikat](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Ladda upp ett PKCS12-formatcertifikat](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Program

- [Lista program](/graph/api/application-list)
- [Skapa ett program](/graph/api/resources/application)
- [Uppdatera program](/graph/api/application-update)
- [Skapa servicePrincipal](/graph/api/resources/serviceprincipal)
- [Skapa oauth2Permission Grant](/graph/api/resources/oauth2permissiongrant)
- [Ta bort program](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Egenskaper för programtillägg

- [Lista tilläggsegenskaper](/graph/api/application-list-extensionproperty)

Azure AD B2C innehåller en katalog som kan innehålla 100 anpassade attribut per användare. För användarflöden hanteras dessa tilläggsegenskaper [med hjälp av Azure Portal](user-flow-custom-attributes.md). För anpassade principer Azure AD B2C skapar egenskapen åt dig första gången principen skriver ett värde till tilläggsegenskapen.

## <a name="audit-logs"></a>Granskningsloggar

- [Lista granskningsloggar](/graph/api/directoryaudit-list)

Mer information om åtkomst till Azure AD B2C finns i Åtkomst till [Azure AD B2C-granskningsloggar.](view-audit-logs.md)

## <a name="conditional-access"></a>Villkorlig åtkomst

- [Visa en lista över alla principer för villkorlig åtkomst](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Läsa egenskaper och relationer för en princip för villkorsstyrd åtkomst](/graph/api/conditionalaccesspolicy-get)
- [Skapa en ny princip för villkorlig åtkomst](/graph/api/resources/application)
- [Uppdatera en princip för villkorlig åtkomst](/graph/api/conditionalaccesspolicy-update)
- [Ta bort en princip för villkorlig åtkomst](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Kodexempel: Så här hanterar du användarkonton programmatiskt

Det här kodexe exemplet är ett .NET Core-konsolprogram som [använder Microsoft Graph SDK för](/graph/sdks/sdks-overview) att interagera med Microsoft Graph API. Koden visar hur du anropar API:et för att programmatiskt hantera användare i en Azure AD B2C klientorganisation.
Du kan [ladda ned exempelarkivet](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [bläddra på lagringsplatsen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) på GitHub eller klona lagringsplatsen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

När du har fått kodexe exemplet konfigurerar du det för din miljö och skapar sedan projektet:

1. Öppna projektet i [Visual Studio](https://visualstudio.microsoft.com) eller [Visual Studio Code](https://code.visualstudio.com).
1. Öppna `src/appsettings.json`.
1. I avsnittet `appSettings` ersätter du `your-b2c-tenant` med namnet på din klientorganisation och och `Application (client) ID` med värdena för registreringen av `Client secret` hanteringsprogrammet. Mer information finns i [Registrera ett Microsoft Graph program](microsoft-graph-get-started.md).
1. Öppna ett konsolfönster i din lokala klon av lagringsplatsen, växla till `src` katalogen och skapa sedan projektet:

    ```console
    cd src
    dotnet build
    ```
    
1. Kör programmet med `dotnet` kommandot :

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Programmet visar en lista över kommandon som du kan köra. Du kan till exempel hämta alla användare, hämta en enskild användare, ta bort en användare, uppdatera en användares lösenord och massimport.

### <a name="code-discussion"></a>Koddiskussion

Exempelkoden använder [sdk Microsoft Graph,](/graph/sdks/sdks-overview)som är utformat för att förenkla processen att skapa effektiva och elastiska program av hög kvalitet som har åtkomst Microsoft Graph.

Alla förfrågningar till MICROSOFT GRAPH-API:et kräver en åtkomsttoken för autentisering. Lösningen använder NuGet-paketet [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) som tillhandahåller en autentiseringsscenariobaserad wrapper av Microsoft Authentication Library (MSAL) för användning med Microsoft Graph SDK.

Metoden `RunAsync` i filen _Program.cs:_

1. Läser programinställningar från den _appsettings.jsfilen_
1. Initierar autentiseringsprovidern med hjälp av [beviljandeflödet för OAuth 2.0-klientautentiseringsuppgifter.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Med flödet för beviljande av klientautentiseringsuppgifter kan appen hämta en åtkomsttoken för att anropa Microsoft Graph-API:et.
1. Uppsättningar av Microsoft Graph-tjänstklienten med autentiseringsprovidern:

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

Den initierade *GraphServiceClient* används sedan i _UserService.cs för_ att utföra användarhanteringsåtgärder. Du kan till exempel hämta en lista över användarkonton i klientorganisationen:

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

[Api-anrop med Microsoft Graph-SDK:er](/graph/sdks/create-requests) innehåller information om hur du läser och skriver information från Microsoft Graph, använder för att styra de returnerade egenskaperna, anger anpassade frågeparametrar och använder frågeparametrarna och `$select` `$filter` `$orderBy` .

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
