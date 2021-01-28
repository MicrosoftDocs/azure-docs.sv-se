---
title: Hantera resurser med Microsoft Graph
titleSuffix: Azure AD B2C
description: Hur du hanterar resurser i en Azure AD B2C klient genom att anropa Microsoft Graph API och använda en program identitet för att automatisera processen.
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
ms.openlocfilehash: a7e9e523d3aae7cf1444c048c023ca1d85fde41f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952246"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Hantera Azure AD B2C med Microsoft Graph

Med Microsoft Graph kan du hantera resurser i Azure AD B2Cs katalogen. Följande Microsoft Graph API-åtgärder stöds för hantering av Azure AD B2C resurser, inklusive användare, identitets leverantörer, användar flöden, anpassade principer och princip nycklar. Varje länk i följande avsnitt riktar sig mot motsvarande sida i Microsoft Graph API-referens för åtgärden. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda MS Graph API och interagera med resurser i din Azure AD B2C klient behöver du en program registrering som ger behörighet att göra detta. Följ stegen i artikeln [hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md) för att skapa en program registrering som hanterings programmet kan använda. 

## <a name="user-management"></a>Användarhantering

- [Visa användare](/graph/api/user-list)
- [Skapa en konsument användare](/graph/api/user-post-users)
- [Hämta en användare](/graph/api/user-get)
- [Uppdatera en användare](/graph/api/user-update)
- [Ta bort en användare](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Hantering av användar telefonnummer (beta)

Ett telefonnummer som kan användas av en användare för att logga in med [SMS eller röst samtal](identity-provider-local.md#phone-sign-in-preview)eller [Multi-Factor Authentication](multi-factor-authentication.md). Mer information finns i [Azure AD Authentication Methods API](/graph/api/resources/phoneauthenticationmethod).

- [Lägg till](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Hämta](/graph/api/phoneauthenticationmethod-get)
- [Uppdatera](/graph/api/phoneauthenticationmethod-update)
- [Ta bort](/graph/api/phoneauthenticationmethod-delete)

Obs! [list](/graph/api/authentication-list-phonemethods) åtgärden returnerar bara aktiverade telefonnummer. Följande telefonnummer måste vara aktiverat för att kunna användas med list åtgärderna. 

![Aktivera telefonin loggning](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-postadress för lösen ords återställning via självbetjäning (beta)

En e-postadress som kan användas av ett [inloggnings konto för användar namn](identity-provider-local.md#username-sign-in) för att återställa lösen ordet. Mer information finns i [Azure AD Authentication Methods API](/graph/api/resources/emailauthenticationmethod).

- [Lägg till](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Hämta](/graph/api/emailauthenticationmethod-get)
- [Uppdatera](/graph/api/emailauthenticationmethod-update)
- [Ta bort](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitetsprovidrar

Hantera de [identitets leverantörer](add-identity-provider.md) som är tillgängliga för dina användar flöden i Azure AD B2C-klienten.

- [Visa lista med identitets leverantörer som registrerats i Azure AD B2C klient organisationen](/graph/api/identityprovider-list)
- [Skapa en identitets leverantör](/graph/api/identityprovider-post-identityproviders)
- [Hämta en identitets leverantör](/graph/api/identityprovider-get)
- [Uppdatera identitets leverantör](/graph/api/identityprovider-update)
- [Ta bort en identitets leverantör](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Användarflöde

Konfigurera fördefinierade principer för registrering, inloggning, kombinerad registrering och inloggning, återställning av lösen ord och profil uppdatering.

- [Visa lista över användar flöden](/graph/api/identitycontainer-list-b2cuserflows)
- [Skapa ett användarflöde](/graph/api/identitycontainer-post-b2cuserflows)
- [Hämta ett användar flöde](/graph/api/b2cidentityuserflow-get)
- [Ta bort ett användar flöde](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Autentiseringsmetoder för användar flöde (beta)

Välj en mekanism för att låta användare registrera sig via lokala konton. Lokala konton är de konton där Azure AD gör identitets kontrollen. Mer information finns i [resurs typen b2cAuthenticationMethodsPolicy](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Hämta](/graph/api/b2cauthenticationmethodspolicy-get)
- [Uppdatera](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Anpassade principer

Med följande åtgärder kan du hantera principer för Azure AD B2C förtroende Framework, så kallade [anpassade principer](custom-policy-overview.md).

- [Lista alla förtroende Ramverks principer som kon figurer ATS i en klient](/graph/api/trustframework-list-trustframeworkpolicies)
- [Skapa förtroende Ramverks princip](/graph/api/trustframework-post-trustframeworkpolicy)
- [Läsa egenskaper för en befintlig förtroende Ramverks princip](/graph/api/trustframeworkpolicy-get)
- [Uppdatera eller skapa förtroende Ramverks princip.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Ta bort en befintlig princip för förtroende ramverk](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Principnycklar

I ramverket med identitets upplevelsen lagras hemligheter som refereras i en anpassad princip för att upprätta förtroende mellan komponenter. Dessa hemligheter kan vara symmetriska eller asymmetriska nycklar/värden. I Azure Portal visas dessa entiteter som **princip nycklar**.

Resursen på den översta nivån för princip nycklar i Microsoft Graph API är den [betrodda Ramverks nyckel uppsättningen](/graph/api/resources/trustframeworkkeyset). Varje nyckel **uppsättning** innehåller minst en **nyckel**. Skapa en nyckel genom att först skapa en tom nyckel uppsättning och sedan generera en nyckel i nyckel uppsättningen. Du kan skapa en manuell hemlighet, överföra ett certifikat eller en PKCS12 nyckel. Nyckeln kan vara en genererad hemlighet, en sträng (till exempel Facebook-programmets hemlighet) eller ett certifikat som du överför. Om en nyckel uppsättning har flera nycklar är bara en av nycklarna aktiv.

### <a name="trust-framework-policy-keyset"></a>Princip uppsättning för förtroende ramverk

- [Visa en lista med de förtroende Framework-standarduppsättningarna](/graph/api/trustframework-list-keysets)
- [Skapa ett förtroende Framework-standarduppsättningar](/graph/api/trustframework-post-keysets)
- [Hämta en nyckel uppsättning](/graph/api/trustframeworkkeyset-get)
- [Uppdatera en förtroende Framework-gruppuppsättning](/graph/api/trustframeworkkeyset-update)
- [Ta bort ett förtroende Framework-gruppuppsättningar](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Princip nyckel för förtroende ramverk

- [Hämta den aktuella aktiva nyckeln i nyckel uppsättningen](/graph/api/trustframeworkkeyset-getactivekey)
- [Generera en nyckel i nyckel uppsättningen](/graph/api/trustframeworkkeyset-generatekey)
- [Ladda upp en sträng baserad hemlighet](/graph/api/trustframeworkkeyset-uploadsecret)
- [Ladda upp ett X. 509-certifikat](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Ladda upp ett PKCS12-format certifikat](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Program

- [Lista program](/graph/api/application-list)
- [Skapa ett program](/graph/api/resources/application)
- [Uppdatera program](/graph/api/application-update)
- [Skapa servicePrincipal](/graph/api/resources/serviceprincipal)
- [Skapa oauth2Permission-beviljande](/graph/api/resources/oauth2permissiongrant)
- [Ta bort program](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Egenskaper för program tillägg

- [Egenskaper för List tillägg](/graph/api/application-list-extensionproperty)

Azure AD B2C tillhandahåller en katalog som kan innehålla 100 anpassade attribut per användare. För användar flöden hanteras de här tilläggs egenskaperna [med hjälp av Azure Portal](user-flow-custom-attributes.md). För anpassade principer skapar Azure AD B2C egenskapen åt dig, första gången principen skriver ett värde för egenskapen Extension.

## <a name="audit-logs"></a>Granskningsloggar

- [Lista gransknings loggar](/graph/api/directoryaudit-list)

Mer information om hur du kommer åt Azure AD B2C gransknings loggar finns i avsnittet [komma åt Azure AD B2C gransknings loggar](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Kod exempel: program mässigt hantera användar konton

Det här kod exemplet är ett .NET Core-konsolprogram som använder [Microsoft Graph SDK](/graph/sdks/sdks-overview) för att interagera med Microsoft Graph API. Koden visar hur du anropar API: et för att hantera användare program mässigt i en Azure AD B2C klient organisation.
Du kan [Ladda ned exempel arkivet](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [Bläddra i lagrings platsen](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) på GitHub eller klona lagrings platsen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

När du har fått kod exemplet konfigurerar du det för din miljö och skapar sedan projektet:

1. Öppna projektet i [Visual Studio](https://visualstudio.microsoft.com) eller [Visual Studio Code](https://code.visualstudio.com).
1. Öppna `src/appsettings.json`.
1. I `appSettings` avsnittet ersätter du `your-b2c-tenant` med namnet på din klient och `Application (client) ID` och `Client secret` med värdena för registreringen av hanterings programmet. Mer information finns i [Registrera ett Microsoft Graph-program](microsoft-graph-get-started.md).
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
