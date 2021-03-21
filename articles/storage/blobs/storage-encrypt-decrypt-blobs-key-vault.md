---
title: Självstudie – kryptera och dekryptera blobbar med Azure Key Vault
titleSuffix: Azure Storage
description: Lär dig hur du krypterar och dekrypterar en blob med kryptering på klient sidan med Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c2daed4a8df89ed176749900dc75eb231c00af87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049278"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Självstudie – kryptera och dekryptera blobbar med Azure Key Vault

Den här självstudien beskriver hur du använder lagrings kryptering på klient sidan med Azure Key Vault. Den vägleder dig genom hur du krypterar och dekrypterar en BLOB i ett konsol program med hjälp av dessa tekniker.

**Uppskattad tidsåtgång:** 20 minuter

Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md).

Översikts information om kryptering på klient sidan för Azure Storage finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudiekursen behöver du följande:

* Ett Azure Storage konto
* Visual Studio 2013 eller senare
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Översikt över kryptering på klient Sidan

En översikt över kryptering på klient sidan för Azure Storage finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Här är en kort beskrivning av hur kryptering på klient sidan fungerar:

1. Den Azure Storage klient-SDK: n genererar en innehålls krypterings nyckel (CEK), som är en symmetrisk nyckel som används för att använda en enda tidpunkt.
2. Kund information krypteras med den här CEK.
3. CEK omsluts sedan (krypteras) med nyckel krypterings nyckeln (KEK). KEK identifieras av en nyckel identifierare och kan vara ett asymmetriskt nyckel par eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault. Själva lagrings klienten har aldrig åtkomst till KEK. Den anropar bara den nyckel brytnings algoritm som tillhandahålls av Key Vault. Kunder kan välja att använda anpassade providers för nyckel brytningar/avbrytningar om de vill.
4. Krypterade data överförs sedan till Azure Storages tjänsten.

## <a name="set-up-your-azure-key-vault"></a>Konfigurera din Azure Key Vault

För att kunna fortsätta med den här självstudien måste du utföra följande steg, som beskrivs i snabb starten för självstudier [: Ange och hämta en hemlighet från Azure Key Vault med hjälp av en .NET-webbapp](../../key-vault/secrets/quick-create-net.md):

* Skapa ett nyckelvalv.
* Lägg till en nyckel eller hemlighet till nyckel valvet.
* Registrera ett program med Azure Active Directory.
* Ge programmet behörighet att använda nyckeln eller hemligheten.

Anteckna de ClientID och ClientSecret som genererades när du registrerade ett program med Azure Active Directory.

Skapa båda nycklarna i nyckel valvet. Vi utgår från resten av kursen som du har använt följande namn: ContosoKeyVault och TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Skapa ett konsol program med paket och AppSettings

Skapa ett nytt konsol program i Visual Studio.

Lägg till nödvändiga NuGet-paket i Package Manager-konsolen.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Lägg till AppSettings till App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Lägg till följande `using` direktiv och se till att lägga till en referens till System.Configuration i projektet.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```
---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Lägg till en metod för att hämta en token till konsol programmet

Följande metod används av Key Vault klasser som behöver autentiseras för åtkomst till ditt nyckel valv.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```
---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Komma åt Azure Storage och Key Vault i ditt program

Lägg till följande kod i Main ()-metoden.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```
---

> [!NOTE]
> Key Vault objekt modeller
> 
> Det är viktigt att förstå att det finns två Key Vault objekt modeller som är medvetna om: en är baserad på REST API (nyckel namns namn område) och det andra är ett tillägg för kryptering på klient sidan.
> 
> Key Vault-klienten interagerar med REST API och förstår JSON-webbnycklar och hemligheter för de två typer av saker som finns i Key Vault.
> 
> Key Vault tillägg är klasser som verkar specifikt skapade för kryptering på klient sidan i Azure Storage. De innehåller ett gränssnitt för nycklar (IKey) och klasser baserat på begreppet nyckel lösare. Det finns två implementeringar av IKey som du behöver känna till: RSAKey och SymmetricKey. De kan nu sammanfalla med de saker som finns i en Key Vault, men i den här punkten är de oberoende klasser (så att nyckeln och hemligheten som hämtas av Key Vault-klienten inte implementerar IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Kryptera blob och ladda upp

Lägg till följande kod för att kryptera en blob och ladda upp den till ditt Azure Storage-konto. **ResolveKeyAsync** -metoden som används returnerar en IKey.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```
---

> [!NOTE]
> Om du tittar på BlobEncryptionPolicy-konstruktorn ser du att den kan acceptera en nyckel och/eller en lösare. Tänk på att just nu kan du inte använda en lösare för kryptering eftersom den inte har stöd för en standard nyckel.


## <a name="decrypt-blob-and-download"></a>Dekryptera blob och hämta

Dekryptering är egentligen när du använder matchare-klasser. ID: t för den nyckel som används för kryptering är associerat med blobben i dess metadata, så det finns ingen anledning att hämta nyckeln och komma ihåg associationen mellan nyckel och blob. Du behöver bara se till att nyckeln finns kvar i Key Vault.   

Den privata nyckeln för en RSA-nyckel finns kvar i Key Vault, så för dekryptering skickas den krypterade nyckeln från BLOB-metadata som innehåller CEK till Key Vault för dekryptering.

Lägg till följande för att dekryptera blobben som du just har laddat upp.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```
---

> [!NOTE]
> Det finns ett par andra typer av lösningar för att förenkla nyckel hanteringen, inklusive: AggregateKeyResolver och CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Använd Key Vault hemligheter

Sättet att använda en hemlighet med kryptering på klient sidan är via klassen SymmetricKey eftersom en hemlighet i grunden är en symmetrisk nyckel. Men som nämnts ovan mappar inte en hemlighet i Key Vault exakt till en SymmetricKey. Det finns några saker att förstå:

* Nyckeln i en SymmetricKey måste vara en fast längd: 128, 192, 256, 384 eller 512 bitar.
* Nyckeln i en SymmetricKey ska vara Base64-kodad.
* En Key Vault hemlighet som ska användas som SymmetricKey måste ha innehålls typen "Application/oktett-Stream" i Key Vault.

Här är ett exempel i PowerShell för att skapa en hemlighet i Key Vault som kan användas som en SymmetricKey.
Observera att det hårdkodade värdet $key är endast för demonstrations syfte. I din egen kod vill du skapa den här nyckeln.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

I konsol programmet kan du använda samma anrop som innan du hämtar hemligheten som en SymmetricKey.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
---

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Microsoft Azure Storage med C# finns i [Microsoft Azure Storage klient bibliotek för .net](/previous-versions/azure/dn261237(v=azure.100)).

Mer information om BLOB-REST API finns i [BLOB Service REST API](/rest/api/storageservices/Blob-Service-REST-API).

Den senaste informationen om Microsoft Azure Storage finns i [Microsoft Azure Storage teamets blogg](/archive/blogs/windowsazurestorage/).