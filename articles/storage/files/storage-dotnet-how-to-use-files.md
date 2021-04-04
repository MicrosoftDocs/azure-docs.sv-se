---
title: Utveckla för Azure Files med .NET | Microsoft Docs
description: Lär dig hur du utvecklar .NET-program och tjänster som använder Azure Files för att lagra data.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: e112060db4a44884d3094a939b03ff106ba72e65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96492207"
---
# <a name="develop-for-azure-files-with-net"></a>Utveckla för Azure Files med .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Lär dig grunderna i att utveckla .NET-program som använder [Azure Files](storage-files-introduction.md) för att lagra data. Den här artikeln visar hur du skapar ett enkelt konsol program för att göra följande med .NET och Azure Files:

- Hämta innehållet i en fil.
- Ange den maximala storleken eller kvoten för en fil resurs.
- Skapa en signatur för delad åtkomst (SAS) för en fil.
- Kopiera en fil till en annan fil i samma lagringskonto.
- Kopiera en fil till en blobb i samma lagringskonto.
- Skapa en ögonblicks bild av en fil resurs.
- Återställa en fil från en resurs ögonblicks bild.
- Använd Azure Storage mått för fel sökning.

Mer information om Azure Files finns i [Vad är Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Förstå .NET-API: er

Azure Files tillhandahåller två breda metoder för klientprogram: Server Message Block (SMB) och REST. I .NET är `System.IO` API: `Azure.Storage.Files.Shares` erna och abstrakta dessa metoder.

API | När du ska använda detta | Anteckningar
----|-------------|------
[System.IO](/dotnet/api/system.io) | Ditt program: <ul><li>Behöver läsa/skriva filer med hjälp av SMB</li><li>Körs på en enhet som har åtkomst via port 445 till ditt Azure Files-konto</li><li>Behöver inte hantera några av de administrativa inställningarna för filresursen</li></ul> | Fil-I/O som implementeras med Azure Files över SMB är vanligt vis samma som I/O med nätverks fil resurs eller lokal lagrings enhet. En introduktion till ett antal funktioner i .NET, inklusive fil-I/O, finns i självstudier för [konsol programmet](/dotnet/csharp/tutorials/console-teleprompter) .
[Azure. Storage. files. reshares](/dotnet/api/azure.storage.files.shares) | Ditt program: <ul><li>Det går inte att komma åt Azure Files med SMB på port 445 på grund av brand Väggs-eller Internet leverantörs begränsningar</li><li>Kräver administrativa funktioner, som möjligheten att ställa in en filresurs kvot eller skapa en signatur för delad åtkomst</li></ul> | Den här artikeln visar användningen av `Azure.Storage.Files.Shares` för fil-I/O med rest i stället för SMB och hantering av fil resursen.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Skapa konsolprogrammet och hämta monteringen

Du kan använda Azure Files klient biblioteket i alla typer av .NET-appar. De här apparna omfattar Azure Cloud, Web, Desktop och Mobile Apps. I den här guiden skapar vi ett konsol program för enkelhetens skull.

Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsol program i Visual Studio 2019. Stegen är ungefär som i andra versioner av Visual Studio.

1. Starta Visual Studio och välj **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer sedan **Nästa**.
1. I **Konfigurera ditt nya projekt** anger du ett namn för appen och väljer **skapa**.

Lägg till alla kod exempel i den här artikeln till- `Program` klassen i filen *program. cs* .

## <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs

Referera till dessa paket i projektet:

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

- [Azure Core-bibliotek för .net](https://www.nuget.org/packages/Azure.Core/): det här paketet är implementeringen av Azure client pipeline.
- [Azure Storage BLOB klient bibliotek för .net](https://www.nuget.org/packages/Azure.Storage.Blobs/): det här paketet ger programmatisk åtkomst till BLOB-resurser i ditt lagrings konto.
- [Azure Storage-klient bibliotek för .net](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): det här paketet ger programmatisk åtkomst till fil resurser i ditt lagrings konto.
- [System Configuration Manager bibliotek för .net](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): det här paketet innehåller en klass som lagrar och hämtar värden i en konfigurations fil.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. I **Solution Explorer** högerklickar du på projektet och väljer **Hantera NuGet-paket**.
1. I **NuGet Package Manager** väljer du **Bläddra**. Sök sedan efter och välj **Azure. Core** och välj sedan **Installera**.

   Det här steget installerar paketet och dess beroenden.

1. Sök efter och installera dessa paket:

   - **Azure. Storage. blobar**
   - **Azure. Storage. files. reshares**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

- [Microsoft Azure Storage gemensamt bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): det här paketet ger programmatisk åtkomst till vanliga resurser i ditt lagrings konto.
- [Microsoft Azure Storage BLOB-bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): det här paketet ger programmatisk åtkomst till BLOB-resurser i ditt lagrings konto.
- [Microsoft Azure Storage fil bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): det här paketet ger programmatisk åtkomst till fil resurser i ditt lagrings konto.
- [Microsoft Azure Configuration Manager bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): det här paketet innehåller en klass för parsning av en anslutnings sträng i en konfigurations fil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. I **Solution Explorer** högerklickar du på projektet och väljer **Hantera NuGet-paket**.
1. I **NuGet Package Manager** väljer du **Bläddra**. Sök sedan efter och välj **Microsoft. Azure. Storage. blob** och välj sedan **Installera**.

   Det här steget installerar paketet och dess beroenden.
1. Sök efter och installera dessa paket:

   - **Microsoft. Azure. Storage. common**
   - **Microsoft. Azure. Storage. File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Spara autentiseringsuppgifterna för ditt lagrings konto i App.config-filen

Spara sedan dina autentiseringsuppgifter i projektets *App.config* -fil. I **Solution Explorer** dubbelklickar du på `App.config` och redigerar filen så att den liknar följande exempel.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

Ersätt `myaccount` med ditt lagrings konto namn och `mykey` med din lagrings konto nyckel.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Ersätt `myaccount` med ditt lagrings konto namn och `StorageAccountKeyEndingIn==` med din lagrings konto nyckel.

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
> Azurite Storage-emulatorn stöder för närvarande inte Azure Files. Anslutnings strängen måste vara riktad mot ett Azure Storage-konto i molnet för att fungera med Azure Files.

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Öppna filen *program. cs* i **Solution Explorer** och Lägg till följande med hjälp av direktiv överst i filen.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Ansluta till filresursen via programmering

I filen *program. cs* lägger du till följande kod för att få åtkomst till fil resursen program mässigt.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

Följande metod skapar en fil resurs om den inte redan finns. Metoden börjar genom att skapa ett [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) -objekt från en anslutnings sträng. Exemplet försöker sedan ladda ned en fil som vi skapade tidigare. Anropa den här metoden från `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Lägg sedan till följande innehåll till `Main()` -metoden, efter den kod som visas ovan, för att hämta anslutnings strängen. Den här koden hämtar en referens till filen som vi skapade tidigare och matar ut innehållet.

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

Från och med version 5. x av Azure Files klient biblioteket kan du ange kvoten (maximal storlek) för en fil resurs. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Om du anger kvoten för en resurs begränsas den totala storleken på filerna som lagras på resursen. Om den totala storleken på filer på resursen överskrider kvoten kan inte klienterna öka storleken på befintliga filer. Klienter kan inte heller skapa nya filer, såvida inte dessa filer är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

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

Från och med version 5. x av Azure Files klient biblioteket kan du generera en signatur för delad åtkomst (SAS) för en fil resurs eller en enskild fil.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

Följande exempel metod returnerar en SAS på en fil i den angivna resursen.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Du kan också skapa en lagrad åtkomst princip på en fil resurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en lagrad åtkomst princip eftersom du kan återkalla SAS om den blir komprometterad. I följande exempel skapas en lagrad åtkomst princip på en resurs. I exemplet används principen för att tillhandahålla begränsningar för en SAS på en fil i resursen.

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

Mer information om hur du skapar och använder signaturer för delad åtkomst finns i [så här fungerar en signatur för delad åtkomst](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopiera filer

Från och med version 5. x av Azure Files klient biblioteket kan du kopiera en fil till en annan fil, en fil till en BLOB eller en blob till en fil.

Du kan också använda AzCopy för att kopiera en fil till en annan eller kopiera en blob till en fil eller till ett annat sätt runt. Se [Kom igång med AZCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Om du kopierar en blobb till en fil eller en fil till en blobb måste du använda en signatur för delad åtkomst (SAS) för att auktorisera åtkomst till källobjektet, även om du kopierar inom samma lagringskonto.

### <a name="copy-a-file-to-another-file"></a>Kopiera en fil till en annan fil

I följande exempel kopierar vi en fil till en annan fil i samma resurs. Du kan använda [autentisering med delad nyckel](/rest/api/storageservices/authorize-with-shared-key) för att göra kopian eftersom den här åtgärden kopierar filer inom samma lagrings konto.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

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

Från och med version 8,5 av Azure Files klient biblioteket kan du skapa en resurs ögonblicks bild. Du kan också visa eller bläddra bland resursögonblicksbilder och ta bort resursögonblicksbilder. Dela ögonblicks bilder är skrivskyddade när de har skapats.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder

I följande exempel skapas en ögonblicksbild av en filresurs.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder

I följande exempel visas en lista över ögonblicks bilder på en resurs.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Visa en lista över filer och kataloger i resurs ögonblicks bilder

I följande exempel bläddrar filer och kataloger i resurs ögonblicks bilder.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Återställa fil resurser eller filer från resurs ögonblicks bilder

Genom att ta en ögonblicks bild av en fil resurs kan du återställa enskilda filer eller hela fil resursen.

Du kan återställa en fil från en ögonblicksbild av en filresurs genom att skicka en fråga till ögonblicksbilderna av en filresurs. Sedan kan du hämta en fil som tillhör en viss resurs ögonblicks bild. Använd den här versionen för att läsa eller återställa filen direkt.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Felsöka Azure Files med hjälp av mått<a name="troubleshooting-azure-files-using-metrics"></a>

Azure-lagringsanalys stöder mått för Azure Files. Med hjälp av mätvärdesdata kan du spåra begäranden och diagnostisera problem.

Du kan aktivera mått för Azure Files från [Azure Portal](https://portal.azure.com). Du kan också aktivera mått genom programmering genom att anropa åtgärden [Ange fil tjänst egenskaper](/rest/api/storageservices/set-file-service-properties) med REST API eller en av dess analoga objekt i Azure Files klient biblioteket.

Följande kod exempel visar hur du använder .NET-klient biblioteket för att aktivera mått för Azure Files.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Lägg först till följande `using` direktiv i filen *program. cs* , tillsammans med de som du lade till ovan:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Även om Azure-blobbar, Azure-tabeller och Azure-köer använder den delade `ServiceProperties` typen i `Microsoft.Azure.Storage.Shared.Protocol` namn området, Azure Files använder sin egen typ, `FileServiceProperties` typen i `Microsoft.Azure.Storage.File.Protocol` namn området. Du måste referera till båda namn områdena från din kod, men för att kunna kompilera i följande kod.

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

Om du stöter på problem kan du referera till [fel sökning Azure Files problem i Windows](storage-troubleshoot-windows-file-connection-problems.md).

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