---
title: Utveckla för Azure Files med .NET | Microsoft Docs
description: Lär dig hur du utvecklar .NET-program och -tjänster som använder Azure Files för att lagra data.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817385"
---
# <a name="develop-for-azure-files-with-net"></a>Utveckla för Azure Files med .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna för att utveckla .NET-program som använder [Azure Files](storage-files-introduction.md) för att lagra data. Den här artikeln visar hur du skapar ett enkelt konsolprogram för att göra följande med .NET och Azure Files:

- Hämta innehållet i en fil.
- Ange maximal storlek, eller kvot, för en filresurs.
- Skapa en signatur för delad åtkomst (SAS) för en fil.
- Kopiera en fil till en annan fil i samma lagringskonto.
- Kopiera en fil till en blobb i samma lagringskonto.
- Skapa en ögonblicksbild av en filresurs.
- Återställa en fil från en ögonblicksbild av en resurs.
- Använd Azure Storage mått för felsökning.

Mer information om Azure Files finns i [Vad är Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Förstå .NET-API: er

Azure Files tillhandahåller två breda metoder för klientprogram: Server Message Block (SMB) och REST. I .NET `System.IO` abstraherar `Azure.Storage.Files.Shares` API:erna och dessa metoder.

API | När du ska använda detta | Anteckningar
----|-------------|------
[System.IO](/dotnet/api/system.io) | Ditt program: <ul><li>Behöver läsa/skriva filer med hjälp av SMB</li><li>Körs på en enhet som har åtkomst via port 445 till ditt Azure Files-konto</li><li>Behöver inte hantera några av de administrativa inställningarna för filresursen</li></ul> | Fil-I/O som implementeras med Azure Files över SMB är vanligtvis samma som I/O med alla nätverksfilresurser eller lokala lagringsenheter. En introduktion till ett antal funktioner i .NET, inklusive fil-I/O, finns i [självstudien Konsolprogram.](/dotnet/csharp/tutorials/console-teleprompter)
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | Ditt program: <ul><li>Det går inte att komma Azure Files med hjälp av SMB på port 445 på grund av brandväggs- eller Internetleverantörsbegränsningar</li><li>Kräver administrativa funktioner, som möjligheten att ställa in en filresurs kvot eller skapa en signatur för delad åtkomst</li></ul> | Den här artikeln visar hur du använder `Azure.Storage.Files.Shares` för fil-I/O med REST i stället för SMB och hantering av filresursen.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Skapa konsolprogrammet och hämta monteringen

Du kan använda Azure Files klientbibliotek i alla typer av .NET-appar. Dessa appar omfattar Azure-moln-, webb-, skrivbords- och mobilappar. I den här guiden skapar vi ett konsolprogram för enkelhetens skull.

Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsolprogram i Visual Studio 2019. Stegen är ungefär som i andra versioner av Visual Studio.

1. Starta Visual Studio och välj **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt** väljer du **Konsolapp (.NET Framework)** för C# och sedan **Nästa.**
1. I **Konfigurera det nya projektet** anger du ett namn för appen och väljer **Skapa.**

Lägg till alla kodexempel i den här artikeln `Program` i klassen i filen *Program.cs.*

## <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs

Se de här paketen i projektet:

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

- [Azure-kärnbiblioteket för .NET:](https://www.nuget.org/packages/Azure.Core/)Det här paketet är implementeringen av Azure-klientpipelinen.
- [Azure Storage Blob klientbibliotek för .NET:](https://www.nuget.org/packages/Azure.Storage.Blobs/)Det här paketet ger programmatisk åtkomst till blobresurser i ditt lagringskonto.
- [Azure Storage Files-klientbiblioteket för .NET:](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)Det här paketet ger programmatisk åtkomst till filresurser i ditt lagringskonto.
- [System Konfigurationshanteraren för .NET:](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)Det här paketet innehåller en klass som lagrar och hämtar värden i en konfigurationsfil.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. I **Solution Explorer** högerklickar du på projektet och väljer Hantera **NuGet-paket.**
1. I **NuGet Package Manager** väljer du **Bläddra**. Sök sedan efter och välj **Azure.Core** och välj sedan **Installera**.

   Det här steget installerar paketet och dess beroenden.

1. Sök efter och installera dessa paket:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

- [Microsoft Azure Storage gemensamt bibliotek för .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)Det här paketet ger programmatisk åtkomst till vanliga resurser i ditt lagringskonto.
- [Microsoft Azure Storage Blob-bibliotek för .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)Det här paketet ger programmatisk åtkomst till blobresurser i ditt lagringskonto.
- [Microsoft Azure Storage filbibliotek för .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)Det här paketet ger programmatisk åtkomst till filresurser i ditt lagringskonto.
- [Microsoft Azure Konfigurationshanteraren för .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)Det här paketet innehåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var programmet körs.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. I **Solution Explorer** högerklickar du på projektet och väljer Hantera **NuGet-paket.**
1. I **NuGet Package Manager väljer** du **Bläddra.** Sök sedan efter och välj **Microsoft.Azure.Storage.Blob** och välj sedan **Installera.**

   Det här steget installerar paketet och dess beroenden.
1. Sök efter och installera dessa paket:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Spara autentiseringsuppgifterna för ditt lagringskonto i App.config filen

Spara sedan dina autentiseringsuppgifter i projektets *App.config* fil. I **Solution Explorer** dubbelklickar du `App.config` på och redigerar filen så att den liknar följande exempel.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Ersätt `myaccount` med namnet på ditt lagringskonto och med din `mykey` lagringskontonyckel.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Ersätt `myaccount` med namnet på ditt lagringskonto och med din `StorageAccountKeyEndingIn==` lagringskontonyckel.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Azurite-lagringsemulatorn stöder för närvarande inte Azure Files. Anslutningssträngen måste ha ett Azure Storage-konto i molnet som mål för att fungera med Azure Files.

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

I **Solution Explorer** öppnar du *filen Program.cs* och lägger till följande using-direktiv överst i filen.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Ansluta till filresursen via programmering

I filen *Program.cs* lägger du till följande kod för att komma åt filresursen programmatiskt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Följande metod skapar en filresurs om den inte redan finns. Metoden börjar med att skapa ett [ShareClient-objekt](/dotnet/api/azure.storage.files.shares.shareclient) från en anslutningssträng. Exemplet försöker sedan ladda ned en fil som vi skapade tidigare. Anropa den här metoden från `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Lägg sedan till följande innehåll i -metoden `Main()` efter koden som visas ovan för att hämta anslutningssträngen. Den här koden hämtar en referens till filen som vi skapade tidigare och matar ut dess innehåll.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Visa resultatet genom att köra konsolprogrammet.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Ange den största storleken för en filresurs

Från och med version 5.x Azure Files klientbiblioteket kan du ange kvoten (maximal storlek) för en filresurs. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Om du anger kvoten för en resurs begränsas den totala storleken på filerna som lagras på resursen. Om den totala storleken på filerna på resursen överskrider kvoten kan klienterna inte öka storleken på befintliga filer. Klienter kan inte heller skapa nya filer, såvida inte dessa filer är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generera en signatur för delad åtkomst för en fil eller filresurs

Från och med version 5.x Azure Files klientbiblioteket kan du generera en signatur för delad åtkomst (SAS) för en filresurs eller för en enskild fil.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Följande exempelmetod returnerar en SAS på en fil i den angivna resursen.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Du kan också skapa en lagrad åtkomstprincip på en filresurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en lagrad åtkomstprincip eftersom den gör att du kan återkalla SAS om den komprometteras. I följande exempel skapas en lagrad åtkomstprincip på en resurs. I exemplet används den principen för att ange begränsningar för en SAS på en fil i resursen.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Mer information om hur du skapar och använder signaturer för delad åtkomst finns [i Så här fungerar en signatur för delad åtkomst.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Kopiera filer

Från och med version 5.x Azure Files klientbiblioteket kan du kopiera en fil till en annan fil, en fil till en blob eller en blob till en fil.

Du kan också använda AzCopy för att kopiera en fil till en annan eller för att kopiera en blob till en fil eller tvärtom. Se [Kom igång med AzCopy.](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

> [!NOTE]
> Om du kopierar en blobb till en fil eller en fil till en blobb måste du använda en signatur för delad åtkomst (SAS) för att auktorisera åtkomst till källobjektet, även om du kopierar inom samma lagringskonto.

### <a name="copy-a-file-to-another-file"></a>Kopiera en fil till en annan fil

I följande exempel kopierar vi en fil till en annan fil i samma resurs. Du kan använda [autentisering med delad nyckel](/rest/api/storageservices/authorize-with-shared-key) för att göra kopieringen eftersom den här åtgärden kopierar filer inom samma lagringskonto.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Kopiera en fil till en blobb

I följande exempel skapar vi en fil och kopierar den till en blobb inom samma lagringskonto. I exemplet skapas en SAS för källfilen, som tjänsten använder för att auktorisera åtkomsten till källfilen under kopieringen.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Du kan kopiera en blobb till en fil på samma sätt. Om källobjektet är en blobb skapar du en SAS för att auktorisera åtkomsten till blobben under kopieringen.

## <a name="share-snapshots"></a>Resursögonblicksbilder

Från och med version 8.5 av Azure Files klientbiblioteket kan du skapa en resursögonblicksbild. Du kan också visa eller bläddra bland resursögonblicksbilder och ta bort resursögonblicksbilder. När resursögonblicksbilder har skapats är de skrivskyddade.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder

I följande exempel skapas en ögonblicksbild av en filresurs.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder

I följande exempel visas ögonblicksbilderna på en resurs.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Lista filer och kataloger i resursögonblicksbilder

I följande exempel bläddras filer och kataloger i resursögonblicksbilder.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Återställa filresurser eller filer från resursögonblicksbilder

Genom att ta en ögonblicksbild av en filresurs kan du återställa enskilda filer eller hela filresursen.

Du kan återställa en fil från en ögonblicksbild av en filresurs genom att skicka en fråga till ögonblicksbilderna av en filresurs. Du kan sedan hämta en fil som tillhör en viss resursögonblicksbild. Använd den versionen för att läsa eller återställa filen direkt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Ta bort resursögonblicksbilder

Följande exempel tar bort en ögonblicksbild av en filresurs.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Felsöka Azure Files med hjälp av mått<a name="troubleshooting-azure-files-using-metrics"></a>

Azure-lagringsanalys har stöd för Azure Files. Med hjälp av mätvärdesdata kan du spåra begäranden och diagnostisera problem.

Du kan aktivera mått för Azure Files från [Azure Portal](https://portal.azure.com). Du kan också aktivera mått programmatiskt genom att anropa åtgärden Ange egenskaper för filtjänst med REST API eller någon av dess motsvarigheter i Azure Files klientbiblioteket. [](/rest/api/storageservices/set-file-service-properties)

I följande kodexempel visas hur du använder .NET-klientbiblioteket för att aktivera mått för Azure Files.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Lägg först till följande `using` -direktiv i filen *Program.cs,* tillsammans med de som du lade till ovan:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Även om Azure-blobar, Azure-tabeller och Azure-köer använder den delade typen i namnområdet, använder Azure Files sin egen typ, typen `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` i `FileServiceProperties` `Microsoft.Azure.Storage.File.Protocol` namnområdet. Du måste dock referera till båda namnrymderna från koden för att följande kod ska kompileras.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Om du stöter på problem kan du gå till [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files finns i följande resurser:

### <a name="conceptual-articles-and-videos"></a>Begreppsrelaterade artiklar och videoklipp

- [Azure Files: ett smidigt SMB-filsystem i molnet för Windows och Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Använda Azure Files med Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Verktygsstöd för File Storage

- [Kom igång med AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Felsöka Azure Files-problem i Windows](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Referens

- [API:er för .NET i Azure Storage](/dotnet/api/overview/azure/storage)
- [Fil-tjänstens REST-API](/rest/api/storageservices/File-Service-REST-API)