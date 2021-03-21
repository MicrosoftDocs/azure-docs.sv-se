---
title: Snabb start – Azure Key Vault hemligheter för klient bibliotek för .NET (version 4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av .NET-klient biblioteket (version 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a82c2cdf7084b31eb6ba861e48ecffb81e6d1363
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453707"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Snabb start: Azure Key Vault hemligt klient bibliotek för .NET (SDK v4)

Kom igång med det Azure Key Vault hemliga klient biblioteket för .NET. [Azure Key Vault](../general/overview.md) är en moln tjänst som tillhandahåller en säker lagring för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabb starten lär du dig att skapa, hämta och ta bort hemligheter från ett Azure Key Vault med hjälp av .NET-klient biblioteket

Key Vault klient biblioteks resurser:

[API-referens dokumentation](/dotnet/api/azure.security.keyvault.secrets)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Mer information om Key Vault och hemligheter finns i:
- [Översikt över Key Vault](../general/overview.md)
- [Översikt över hemligheter](about-secrets.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en med hjälp av [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)

Den här snabb starten använder `dotnet` och Azure CLI

## <a name="setup"></a>Konfiguration

Den här snabb starten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. mer information finns i [autentisera klienten med klient biblioteket för Azure Identity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger hemliga behörigheter till ditt användar konto

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

Från kommando gränssnittet installerar du Azure Key Vault Secret-klient biblioteket för .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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

Med Azure Key Vault hemliga klient biblioteket för .NET kan du hantera hemligheter. I avsnittet [kod exempel](#code-examples) visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i *programmet. cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Det här exemplet använder klassen ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om autentisering till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när-konsol programmet är autentiserat lägger du till en hemlighet till nyckel valvet. För den här uppgiften använder du metoden [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) . Metodens första parameter accepterar ett namn för hemligheten &mdash; "hemlig hemlighet" i det här exemplet.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Om det finns ett hemligt namn kommer över koden att skapa en ny version av hemligheten.


### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare angivna värdet med metoden [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) .

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Din hemlighet sparas nu som `secret.Value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort hemligheten från ditt nyckel valv med metoderna [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) och [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient) .

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Exempelkod

Ändra .NET Core-konsolens app för att interagera med Key Vault genom att utföra följande steg:

1. Ersätt koden i *Program.cs* med följande kod:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testa och verifiera

1. Kör följande kommando för att köra appen.

    ```dotnetcli
    dotnet run
    ```

1. När du uppmanas till det anger du ett hemligt värde. Till exempel mySecretPassword.

En variant av följande utdata visas:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Nästa steg

Mer information om Key Vault och hur du integrerar det med dina appar finns i följande artiklar:

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se en [åtkomst Key Vault från självstudien för App Service program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se en [åtkomst Key Vault från den virtuella datorns självstudie](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
