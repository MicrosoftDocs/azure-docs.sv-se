---
title: Snabbstart – Azure Key Vault klientbibliotek för hemligheter för .NET (version 4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med hjälp av .NET-klientbiblioteket (version 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 6338b76e2f14e9f842b3395113badadcd37c8d32
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814180"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Snabbstart: Azure Key Vault hemligt klientbibliotek för .NET (SDK v4)

Kom igång med Azure Key Vault hemligt klientbibliotek för .NET. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten får du lära dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med hjälp av .NET-klientbiblioteket

Key Vault klientbiblioteksresurser:

[API-referensdokumentation](/dotnet/api/azure.security.keyvault.secrets)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Mer information om Key Vault och hemligheter finns i:
- [Key Vault översikt](../general/overview.md)
- [Översikt över hemligheter.](about-secrets.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– skapa en utan kostnad](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en [med hjälp Azure Portal](../general/quick-create-portal.md) Azure [CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)

Den här snabbstarten använder `dotnet` och Azure CLI

## <a name="setup"></a>Konfiguration

Den här snabbstarten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för ditt nyckelvalv som beviljar hemliga behörigheter till ditt användarkonto

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

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

Installera det hemliga klientbiblioteket Azure Key Vault .NET från kommandogränssnittet:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell

Med Azure Key Vault klientbibliotek för .NET kan du hantera hemligheter. Avsnittet [Kodexempel](#code-examples) visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande -direktiv överst i *Program.cs:*

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder [klassen "DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om hur du autentiserar till Key Vault finns [i Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när konsolappen har autentiserats lägger du till en hemlighet i nyckelvalvet. För den här uppgiften använder du [metoden SetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) Metodens första parameter accepterar ett namn för hemligheten &mdash; "mySecret" i det här exemplet.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Om det finns ett hemligt namn skapar koden ovan en ny version av hemligheten.


### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Nu kan du hämta det tidigare inställda värdet med [metoden GetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync)

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Din hemlighet sparas nu som `secret.Value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort hemligheten från nyckelvalvet med metoderna [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) och [PurgeDeletedSecretAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient)

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Exempelkod

Ändra .NET Core-konsolappen så att den interagerar Key Vault genom att utföra följande steg:

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

1. Kör appen genom att köra följande kommando.

    ```dotnetcli
    dotnet run
    ```

1. Ange ett hemligt värde när du uppmanas till det. Till exempel mySecretPassword.

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

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se [självstudien åtkomst Key Vault från App Service-program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se en [access-Key Vault från självstudien för virtuell dator](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault för utvecklare](../general/developers-guide.md)
- Granska [Key Vault säkerhetsöversikten](../general/security-features.md)
