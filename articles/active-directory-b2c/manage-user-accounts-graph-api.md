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
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6abc3316e18fc70a2969bc220fd75e10e10f0e6e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507786"
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

## <a name="user-properties"></a>Användaregenskaper

### <a name="display-name-property"></a>Egenskapen visnings namn

`displayName`Är namnet som ska visas i Azure Portal användar hantering för användaren och i åtkomsttoken Azure AD B2C återgår till programmet. Den här egenskapen är obligatorisk.

### <a name="identities-property"></a>Identiteter-egenskapen

Ett kund konto, som kan vara en konsument, partner eller medborgare, kan associeras med dessa identitets typer:

- **Lokal** identitet – användar namn och lösen ord lagras lokalt i Azure AD B2C-katalogen. Vi refererar ofta till dessa identiteter som "lokala konton".
- **Federerad** identitet – även kallat *sociala* eller *företags* konton, användarens identitet hanteras av en federerad identitets leverantör som Facebook, Microsoft, ADFS eller Salesforce.

En användare med ett kund konto kan logga in med flera identiteter. Till exempel användar namn, e-post, medarbetar-ID, myndighets-ID och andra. Ett enda konto kan ha flera identiteter, både lokala och sociala, med samma lösen ord.

I Microsoft Graph API lagras både lokala och federerade identiteter i `identities` attributet User, som är av typen [objectIdentity][graph-objectIdentity]. `identities`Samlingen representerar en uppsättning identiteter som används för att logga in på ett användar konto. Den här samlingen gör att användaren kan logga in på användar kontot med någon av dess associerade identiteter.

| Egenskap   | Typ |Beskrivning|
|:---------------|:--------|:----------|
|signInType|sträng| Anger användarnas inloggnings typer i din katalog. För lokalt konto:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` eller någon annan typ som du vill ha. Socialt konto måste anges till  `federated` .|
|utfärdare|sträng|Anger utfärdarens identitet. För lokala konton (där **signInType** inte är `federated` det) är den här egenskapen namnet på den lokala B2C-klientens standard domän, till exempel `contoso.onmicrosoft.com` . För sociala identiteter (där **signInType** är  `federated` ) är värdet namnet på utfärdaren, till exempel `facebook.com`|
|issuerAssignedId|sträng|Anger den unika identifierare som användaren har tilldelats av utfärdaren. Kombinationen av **utfärdare** och **issuerAssignedId** måste vara unik inom din klient organisation. För lokalt konto, när **signInType** är inställt på `emailAddress` eller `userName` , representerar det inloggnings namnet för användaren.<br>När **signInType** är inställt på: <ul><li>`emailAddress` (eller börjar med `emailAddress` gilla `emailAddress1` ) **issuerAssignedId** måste vara en giltig e-postadress</li><li>`userName` (eller något annat värde) måste **issuerAssignedId** vara en giltig [lokal del av en e-postadress](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`representerar **issuerAssignedId** det federerade kontots unika identifierare</li></ul>|

Följande **identiteter** -egenskap, med en lokal konto identitet med ett inloggnings namn, en e-postadress som inloggning och med en social identitet. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

För federerade identiteter, beroende på identitets leverantören, är **issuerAssignedId** ett unikt värde för en viss användare per program eller utvecklings konto. Konfigurera Azure AD B2C principen med samma program-ID som tidigare har tilldelats av den sociala leverantören eller något annat program inom samma utvecklings konto.

### <a name="password-profile-property"></a>Egenskap för lösen ords profil

För en lokal identitet är egenskapen **passwordProfile** obligatorisk och innehåller användarens lösen ord. `forceChangePasswordNextSignIn`Egenskapen måste anges till `false` .

För en federerad (social) identitet krävs inte egenskapen **passwordProfile** .

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Egenskap för lösen ords princip

Azure AD B2C lösen ords princip (för lokala konton) baseras på principen för Azure Active Directory [stark lösen ords styrka](../active-directory/authentication/concept-sspr-policy.md) . Principerna Azure AD B2C för registrering eller inloggning och lösen ords återställning kräver den här starka lösen ords styrkan och upphör aldrig att gälla lösen ord.

Om de konton som du vill migrera har en mindre lösen ords styrka än den [starka lösen ords styrkan](../active-directory/authentication/concept-sspr-policy.md) som tillämpas av Azure AD B2C, kan du inaktivera kravet på starkt lösen ord i scenarier med användar migrering. Ange egenskapen till om du vill ändra standard lösen ords principen `passwordPolicies` `DisableStrongPassword` . Du kan till exempel ändra begäran om att skapa användare på följande sätt:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Tilläggs egenskaper

Varje kundriktad app har unika krav för att informationen ska samlas in. Din Azure AD B2C klient har en inbyggd uppsättning information som lagras i egenskaper, t. ex. namn, efter namn, stad och post nummer. Med Azure AD B2C kan du utöka uppsättningen med egenskaper som lagras i varje kund konto. Mer information om hur du definierar anpassade attribut finns i [anpassade attribut](user-flow-custom-attributes.md).

Microsoft Graph API har stöd för att skapa och uppdatera en användare med attribut för tillägg. Attribut för tillägg i Graph API namnges med hjälp av konventionen `extension_ApplicationClientID_attributename` , där `ApplicationClientID` är **programmets (klient) ID** för `b2c-extensions-app` programmet (hittas i **Appregistreringar**  >  **alla program** i Azure Portal). Observera att **program-ID: t (klient)** som det visas i attributets namn för tillägg inte innehåller några bindestreck. Till exempel:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

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