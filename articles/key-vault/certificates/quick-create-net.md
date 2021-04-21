---
title: Snabbstart – Azure Key Vault klientbibliotek för .NET (version 4)
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure-nyckelvalv med hjälp av .NET-klientbiblioteket (version 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 9188079a93c9c2688a310413401e90014b278bf8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813424"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Snabbstart: Azure Key Vault för .NET (SDK v4)

Kom igång med Azure Key Vault klientbibliotek för .NET. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert arkiv för certifikat. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten lär du dig att skapa, hämta och ta bort certifikat från ett Azure-nyckelvalv med hjälp av .NET-klientbiblioteket

Key Vault klientbiblioteksresurser:

[API-referensdokumentation](/dotnet/api/azure.security.keyvault.certificates)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Mer information om Key Vault certifikat finns i:
- [Key Vault översikt](../general/overview.md)
- [Certifikatöversikt](about-certificates.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK eller senare](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en [med Azure Portal,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md) [eller Azure PowerShell](../general/quick-create-powershell.md).

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

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för nyckelvalvet som beviljar certifikatbehörighet till ditt användarkonto

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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

    Byggutdata får inte innehålla några varningar eller fel.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installera paketen

Installera klientbiblioteket för Azure Key Vault .NET från kommandogränssnittet:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

I den här snabbstarten måste du även installera Azure SDK-klientbiblioteket för Azure Identity:

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

Med Azure Key Vault för .NET kan du hantera certifikat. Avsnittet [Kodexempel](#code-examples) visar hur du skapar en klient, anger ett certifikat, hämtar ett certifikat och tar bort ett certifikat.

## <a name="code-examples"></a>Kodexempel

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande -direktiv överst i *Program.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterade identiteter tilldelas till App Service eller virtuell dator. Mer information finns i [Översikt över hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI:t i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder klassen ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om autentisering till Key Vault finns i [Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Spara ett certifikat

I det här exemplet kan du för enkelhetens skull använda själv signerade certifikat med standardutfärdandeprincip. För den här uppgiften använder du [metoden StartCreateCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) Metodens parametrar accepterar ett certifikatnamn och [certifikatprincipen](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Om certifikatnamnet finns skapar koden ovan en ny version av certifikatet.

### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Nu kan du hämta det tidigare skapade certifikatet med metoden [GetCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync)

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Slutligen tar vi bort och rensar certifikatet från nyckelvalvet med metoderna [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) och [PurgeDeletedCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Exempelkod

Ändra .NET Core-konsolappen så att den interagerar med Key Vault genom att utföra följande steg:

- Ersätt koden i *Program.cs* med följande kod:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testa och verifiera

Kör följande kommando för att skapa projektet

```dotnetcli
dotnet build
```

En variant av följande utdata visas:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade ett certifikat och hämtade certifikatet. 

Mer information om Key Vault och hur du integrerar det med dina appar finns i följande artiklar:

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs en [översikt över certifikat](about-certificates.md)
- Se [självstudien åtkomst Key Vault från App Service-program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se [självstudien åtkomst Key Vault från virtuell dator](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault utvecklarhandbok](../general/developers-guide.md)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
