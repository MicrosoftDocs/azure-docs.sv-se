---
title: 'Snabbstart: Azure Blob Storage-klientbibliotek för .NET'
description: I den här snabbstarten lär du dig hur du använder Azure Blob Storage-klientbiblioteket för .NET för att skapa en container och en blob i bloblagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 631b01dadacf81d66c3f42dc1401d2cf492316b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869247"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Snabbstart: Azure Blob Storage-klientbiblioteket v11 för .NET

Kom igång med Azure Blob Storage-klientbiblioteket v11 för .NET. Azure Blob Storage är Microsofts objektlagringslösning för molnet. Följ stegen för att installera paketet och prova exempelkod för grundläggande uppgifter. Blob Storage är optimerad för lagring av enorma mängder ostrukturerade data.

> [!NOTE]
> I den här snabbstarten används en äldre version av Azure Blob Storage-klientbiblioteket. Information om hur du kommer igång med den senaste versionen [finns i Snabbstart: Azure Blob Storage-klientbiblioteket v12 för .NET.](storage-quickstart-blobs-dotnet.md)

Använd Azure Blob Storage för .NET för att:

* Skapa en container
* Ange behörigheter för en container
* Skapa en blob i Azure Storage
* Ladda ned bloben till din lokala dator
* Lista alla blobar i en container
* Ta bort en container

Ytterligare resurser:

* [Referensdokumentation för API](/dotnet/api/overview/azure/storage)
* [Bibliotekskällkod](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* Azure Storage konto – skapa [ett lagringskonto](../common/storage-account-create.md)
* Aktuella [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Se till att hämta SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

I det här avsnittet går vi igenom hur du förbereder ett projekt för att Azure Blob Storage med klientbiblioteket för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa först ett .NET Core-program med namnet *blob-quickstart*.

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en ny konsolapp med namnet `dotnet new` *blob-quickstart*. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Växla till den nyligen skapade *mappen blob-quickstart* och skapa appen för att kontrollera att allt fungerar som det ska.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Förväntade utdata från bygget bör se ut ungefär så här:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i programkatalogen installerar du Azure Blob Storage för .NET-paketet med hjälp av `dotnet add package` kommandot .

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Från projektkatalogen:

1. Öppna filen *Program.cs* i redigeringsprogrammet
2. Ta bort `Console.WriteLine` instruktionen
3. Lägga till `using` direktiv
4. Skapa en `ProcessAsync` metod där huvudkoden för exemplet ska finnas
5. Anropa metoden asynkront `ProcessAsync` från `Main`

Här är koden:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static async Task Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            await ProcessAsync();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

När exempelprogrammet gör en begäran till Azure Storage måste det auktoriserats. Om du vill auktorisera en begäran lägger du till autentiseringsuppgifterna för ditt lagringskonto i programmet som en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot genom att följa dessa steg:

1. Gå till [Azure Portal](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Här kan du visa åtkomstnycklarna för kontot och den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att välja **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

När du har kopierat anslutningssträngen ska du skriva den till en ny miljövariabel på den lokala dator där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `<yourconnectionstring>` med den faktiska anslutningssträngen.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

När du har lagt till miljövariabeln i Windows måste du starta en ny instans av kommandofönstret.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

När du har lagt till miljövariabeln startar du om alla program som körs och behöver läsa miljövariabeln. Starta till exempel om utvecklingsmiljön eller redigeringsredigeraren innan du fortsätter.

## <a name="object-model"></a>Objektmodell

Azure Blob Storage är optimerat för att lagra stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot.
* En container på lagringskontot
* En blobb i en container

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount)Klassen `CloudStorageAccount` representerar ditt Azure-lagringskonto. Använd den här klassen för att ge åtkomst till Blob Storage med åtkomstnycklarna för ditt konto.
* [CloudBlobClient:](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) `CloudBlobClient` Klassen ger en åtkomstpunkt till Blob Service i din kod.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)Klassen `CloudBlobContainer` representerar en blobcontainer i koden.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)Objektet `CloudBlockBlob` representerar en blockblob i koden. Blockblobbar består av datablock som kan hanteras individuellt.

## <a name="code-examples"></a>Kodexempel

De här exempelkodfragmenten visar hur du utför följande med Azure Blob Storage-klientbiblioteket för .NET:

   * [Autentisera klienten](#authenticate-the-client)
   * [Skapa en container](#create-a-container)
   * [Ange behörigheter för en container](#set-permissions-on-a-container)
   * [Ladda upp blobar till en container](#upload-blobs-to-a-container)
   * [Visa blobar i en container](#list-the-blobs-in-a-container)
   * [Ladda ned blobbar](#download-blobs)
   * [Ta bort en container](#delete-a-container)

### <a name="authenticate-the-client"></a>Autentisera klienten

Koden nedan kontrollerar att miljövariabeln innehåller en anslutningssträng som kan parsas för att skapa ett [CloudStorageAccount-objekt](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) som pekar på lagringskontot. Kontrollera att anslutningssträngen är giltig med hjälp av [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse)-metoden. Om `TryParse` lyckas initieras variabeln `storageAccount` och returneras `true` .

Lägg till den här koden i `ProcessAsync` metoden :

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Om du vill utföra resten av åtgärderna i den här artikeln ersätter du i koden ovan med `// ADD OTHER OPERATIONS HERE` kodfragmenten i följande avsnitt.

### <a name="create-a-container"></a>Skapa en container

För att kunna skapa containern måste du först skapa en instans av objektet [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) som pekar på bloblagringen i lagringskontot. Skapa en instans av objektet [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) och skapa sedan behållaren.

I det här fallet anropar koden [metoden CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) för att skapa containern. Ett GUID-värde läggs till containerns namn så att det blir unikt. I en produktionsmiljö är det ofta bättre att använda metoden [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) för att endast skapa en container om den inte redan finns.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Ange behörigheter för en container

Ange behörigheter för containern så att alla blobar i containern är offentliga. Om en blob är offentlig kan den användas anonymt av alla klienter.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobar till en container

Följande kodfragment hämtar en referens till ett objekt genom att anropa `CloudBlockBlob` [metoden GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) för containern som skapades i föregående avsnitt. Den laddar sedan upp den valda lokala filen till bloben genom att anropa [metoden UploadFromFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i containern med hjälp av [metoden ListBlobsSegmentedAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) I det här fallet har endast en blob lagts till i containern, så liståtgärden returnerar bara den enda bloben.

Om det finns för många blobar för att returnera i ett anrop (som standard fler än 5 000) returnerar metoden ett segment av den totala `ListBlobsSegmentedAsync` resultatuppsättningen och en fortsättningstoken. Om du vill hämta nästa segment blobar anger du det fortsättningstoken som returnerades av tidigare anrop, och så vidare tills fortsättningstokenet är null. Ett fortsättningstoken med nullvärde anger att alla blobar har hämtats. Koden visar hur du använder fortsättningstoken för bästa praxis.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned bloben som skapades tidigare till ditt lokala filsystem med hjälp av [metoden DownloadToFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) Exempelkoden lägger till suffixet "_DOWNLOADED" i blobnamnet så att du kan se båda filerna i det lokala filsystemet.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som appen skapade genom att ta bort hela containern med [hjälp av CloudBlobContainer.DeleteAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) Du kan även ta bort de lokala filerna om du vill.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i din lokala *MyDocuments-mapp* och laddar upp den till Blob Storage. Exemplet visar sedan blobarna i containern och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Gå till programkatalogen och skapa och kör sedan programmet.

```console
dotnet build
```

```console
dotnet run
```

Appens utdata liknar följande exempel:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

När du trycker på **Retur**-tangenten tas lagringscontainern och filerna bort. Innan du tar bort dem bör du kontrollera att de två filerna finns i mappen *Mina dokument*. Du kan öppna dem och se att de är identiska. Kopiera blobens URL från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i bloben.

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Om du vill lära dig hur du skapar en webbapp som laddar upp en bild till Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Ladda upp och bearbeta en avbildning](storage-upload-process-images.md)

* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
* Om du vill utforska ett exempelprogram som du kan distribuera från Visual Studio för Windows går du till [.NET Photo Gallery Web Application-exempel med Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).