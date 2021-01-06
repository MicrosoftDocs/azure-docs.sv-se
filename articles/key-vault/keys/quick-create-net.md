---
title: Snabb start – Azure Key Vault nycklar klient bibliotek för .NET (version 4)
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure Key Vault med hjälp av .NET-klient biblioteket (version 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cecf8330b7060a4cbc4691f64571a3c7865c575c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935265"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Snabb start: Azure Key Vault nyckel klient bibliotek för .NET (SDK v4)

Kom igång med klient biblioteket för Azure Key Vaults nyckel för .NET. [Azure Key Vault](../general/overview.md) är en moln tjänst som tillhandahåller en säker lagring för kryptografiska nycklar. Du kan lagra kryptografiska nycklar, lösen ord, certifikat och andra hemligheter på ett säkert sätt. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabb starten lär du dig att skapa, hämta och ta bort nycklar från ett Azure Key Vault med hjälp av klient biblioteket för .NET-nyckel

Key Vault viktiga klient biblioteks resurser:

[API-referens dokumentation](/dotnet/api/azure.security.keyvault.keys)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Mer information om Key Vault och nycklar finns i:
- [Översikt över Key Vault](../general/overview.md)
- [Översikt över nycklar](about-keys.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en med hjälp av [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md).

Den här snabb starten använder `dotnet` och Azure CLI

## <a name="setup"></a>Installation

Den här snabb starten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. mer information finns i [autentisera klienten med klient biblioteket för Azure Identity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som beviljar nyckel behörigheter till ditt användar konto

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Skapa ny .NET-konsol program

1. Kör följande kommando i ett kommando gränssnitt för att skapa ett projekt med namnet `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Ändra till den nyligen skapade *Key-valvet-console-app-* katalogen och kör följande kommando för att skapa projektet:

    ```dotnetcli
    dotnet build
    ```

    Build-utdata får inte innehålla varningar eller fel.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installera paketen

Från kommando gränssnittet installerar du Azure Key Vault nyckel klient bibliotek för .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

I den här snabb starten måste du också installera Azure SDK-klient biblioteket för Azure-identitet:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder Key Vault-namnet som en miljö variabel som kallas `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objekt modell

Med klient biblioteket för Azure Key Vaults nyckel för .NET kan du hantera nycklar. I avsnittet [kod exempel](#code-examples) visas hur du skapar en-klient, ställer in en nyckel, hämtar en nyckel och tar bort en nyckel.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i *program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Det här exemplet använder klassen ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om autentisering till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Spara en nyckel

För den här uppgiften använder du metoden [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) . Metodens parametrar accepterar ett nyckel namn och [nyckel typen](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Om det finns ett nyckel namn, kommer över koden att skapa en ny version av nyckeln.

### <a name="retrieve-a-key"></a>Hämta en nyckel

Nu kan du hämta den tidigare skapade nyckeln med [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) -metoden.

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Ta bort en nyckel

Slutligen tar vi bort och rensar nyckeln från nyckel valvet med metoderna [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) och [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) .

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Exempelkod

Ändra .NET Core-konsolens app för att interagera med Key Vault genom att utföra följande steg:

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

1.  Kör följande kommando för att bygga projektet

    ```dotnetcli
    dotnet build
    ```

1. Kör följande kommando för att köra appen.

    ```dotnetcli
    dotnet run
    ```

1. När du uppmanas till det anger du ett hemligt värde. Till exempel mySecretPassword.

    En variant av följande utdata visas:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en nyckel och hämtat nyckeln. 

Mer information om Key Vault och hur du integrerar det med dina appar finns i följande artiklar:

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Läs en [Översikt över nycklar](about-keys.md)
- Se en [åtkomst Key Vault från självstudien för App Service program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se en [åtkomst Key Vault från den virtuella datorns självstudie](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
