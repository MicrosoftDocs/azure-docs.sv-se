---
title: Snabbstart – Azure Key Vault klientbibliotek för .NET (version 4)
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure-nyckelvalv med hjälp av .NET-klientbiblioteket (version 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 5c1e4d64ba3359a07dddbbf89774e31815935230
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818430"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Snabbstart: Azure Key Vault nyckelklientbibliotek för .NET (SDK v4)

Kom igång med Azure Key Vault för .NET. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för kryptografiska nycklar. Du kan lagra kryptografiska nycklar, lösenord, certifikat och andra hemligheter på ett säkert sätt. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten lär du dig att skapa, hämta och ta bort nycklar från ett Azure-nyckelvalv med hjälp av .NET-nyckelklientbiblioteket

Key Vault viktiga klientbiblioteksresurser:

[API-referensdokumentation](/dotnet/api/azure.security.keyvault.keys)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Mer information om Key Vault och nycklar finns i:
- [Key Vault översikt](../general/overview.md)
- [Översikt över nycklar.](about-keys.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– skapa en utan kostnad](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en [med Azure Portal,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md) [eller Azure PowerShell](../general/quick-create-powershell.md).

## <a name="setup"></a>Konfiguration

Den här snabbstarten använder Azure Identity-biblioteket för att autentisera användare för Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    Om Azure CLI eller Azure PowerShell kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för ditt nyckelvalv som beviljar nyckelbehörigheter till ditt användarkonto

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

### <a name="create-new-net-console-app"></a>Skapa en ny .NET-konsolapp

1. Kör följande kommando i ett kommandogränssnitt för att skapa ett projekt med namnet `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Ändra till den nyligen skapade *katalogen key-vault-console-app* och kör följande kommando för att skapa projektet:

    ```dotnetcli
    dotnet build
    ```

    Build-utdata får inte innehålla några varningar eller fel.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installera paketen

Från kommandogränssnittet installerar du Azure Key Vault klientbibliotek för .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

I den här snabbstarten måste du också installera Azure SDK-klientbiblioteket för Azure Identity:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder nyckelvalvsnamnet som en miljövariabel med namnet `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault nyckelklientbiblioteket för .NET kan du hantera nycklar. Avsnittet [Kodexempel](#code-examples) visar hur du skapar en klient, anger en nyckel, hämtar en nyckel och tar bort en nyckel.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande -direktiv överst i *Program.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder [klassen "DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om hur du autentiserar till Key Vault finns [i Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Spara en nyckel

För den här uppgiften använder du [metoden CreateKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) Metodens parametrar accepterar ett nyckelnamn och [nyckeltypen](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Om nyckelnamnet finns skapar koden ovan en ny version av nyckeln.

### <a name="retrieve-a-key"></a>Hämta en nyckel

Nu kan du hämta den tidigare skapade nyckeln med [metoden GetKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync)

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Ta bort en nyckel

Slutligen tar vi bort och rensar nyckeln från nyckelvalvet med metoderna [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) och [PurgeDeletedKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync)

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Exempelkod

Ändra .NET Core-konsolappen så att den interagerar Key Vault genom att utföra följande steg:

- Ersätt koden i *Program.cs* med följande kod:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testa och verifiera

1.  Kör följande kommando för att skapa projektet

    ```dotnetcli
    dotnet build
    ```

1. Kör appen genom att köra följande kommando.

    ```dotnetcli
    dotnet run
    ```

1. Ange ett hemligt värde när du uppmanas till det. Till exempel mySecretPassword.

    En variant av följande utdata visas:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en nyckel och hämtade nyckeln. 

Mer information om Key Vault och hur du integrerar det med dina appar finns i följande artiklar:

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs en [översikt över nycklar](about-keys.md)
- Se [självstudien åtkomst Key Vault från App Service-program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se en [access-Key Vault från självstudien för virtuell dator](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault för utvecklare](../general/developers-guide.md)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
