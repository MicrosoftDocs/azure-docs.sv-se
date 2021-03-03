---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 2213da7b9c6e4776a0e463e6a43d0cc645a1e911
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750555"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Den senaste versionen av [klient biblioteket för .net Core](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativ system.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `AccessTokensQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services Identity Library för .NET-paketet med hjälp av `dotnet add package` kommandot.

```console
dotnet add package Azure.Communication.Identity
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna **program.cs** -filen i en text redigerare
1. Lägg till ett `using` direktiv för att inkludera `Azure.Communication.Identity` namn området
1. Uppdatera `Main` metod deklarationen för att stödja asynkron kod

Använd följande kod för att börja:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Autentisera klienten

Initiera en `CommunicationIdentityClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till följande kod i `Main`-metoden:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Skapa en identitet

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `createUser` metoden för att skapa en ny post i katalogen med en unik `Id` . Lagra mottagen identitet med mappning till programmets användare. Till exempel genom att lagra dem i program serverns databas. Identiteten krävs senare för att utfärda åtkomsttoken.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Utfärda token för identitets åtkomst

Använd `issueToken` metoden för att utfärda en åtkomsttoken för redan befintlig kommunikations tjänst identitet. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md). En ny instans av parametern `communicationUser` kan konstrueras baserat på en sträng representation av Azure Communication Service-identiteten.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Åtkomsttoken är korta autentiseringsuppgifter som måste återutfärdas. Om du inte gör det kan det orsaka störningar i programmets användar upplevelse. `expiresOn`Egenskapen svar anger livs längden för åtkomsttoken. 

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Skapa en identitet och utfärda en åtkomsttoken inom samma begäran

Använd `createUserWithToken` metoden för att skapa en kommunikations tjänst identitet och utfärda en åtkomsttoken för den. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md).

```csharp  
// Issue an identity and an access token with the "voip" scope for the new identity
var identityWithTokenResponse = await client.CreateUserWithTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityWithTokenResponse.Value.user.Id;
var token = identityWithTokenResponse.Value.token.Token;
var expiresOn = identityWithTokenResponse.Value.token.ExpiresOn;
```

## <a name="refresh-access-tokens"></a>Uppdatera åtkomsttoken

Om du vill uppdatera en åtkomsttoken skickar du en instans av `CommunicationUser` objektet till `IssueTokenAsync` . Om du har lagrat detta `Id` och behöver skapa en ny kan `CommunicationUser` du göra det genom att skicka din lagrade `Id` till `CommunicationUser` konstruktorn enligt följande:

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Återkalla åtkomsttoken

I vissa fall kan du uttryckligen återkalla åtkomsttoken. Till exempel när ett programs användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Metoden `RevokeTokensAsync` ogiltig förklarade alla aktiva åtkomsttoken som utfärdats till identiteten.

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Ta bort en identitet

Om du tar bort en identitet återkallar du alla aktiva åtkomsttoken och förhindrar att du utfärdar åtkomsttoken för identiteterna. Det tar också bort allt beständigt innehåll som är associerat med identiteten.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `dotnet run` kommandot.

```console
dotnet run
```
