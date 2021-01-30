---
title: Ladda ned stora mängder slumpmässiga data från Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure SDK för att ladda ned stora mängder slumpmässiga data från ett Azure Storage-konto
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: acfaed10cf627e87691a3068ad0b8cffe9d3b2ee
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096295"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Ladda ned stora mängder slumpmässiga data från Azure Storage

Den här självstudiekursen är den tredje delen i en serie. Den här kursen visar hur du laddar ned stora mängder data från Azure Storage.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet
> * Kör programmet
> * Validera antalet anslutningar

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här kursen måste du ha slutfört den tidigare lagringskursen: [Överföra stora mängder slumpmässiga data parallellt till Azure Storage][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Fjärranslut till din virtuella dator

 Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med publicIPAddress för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Uppdatera programmet

I föregående kurs överförde du bara filer till lagringskontot. Öppna `D:\git\storage-dotnet-perf-scale-app\Program.cs` i en textredigerare. Ersätt metoden `Main` med följande exempel. Det här exemplet kommentarer ut överföringsaktiviteten och tar bort kommentarstecknen för nedladdningsaktiviteten och aktiviteten för att ta bort innehåll i lagringskontot vid slutförandet.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

När programmet har uppdaterats måste du bygga programmet på nytt. Öppna en `Command Prompt` och navigera till `D:\git\storage-dotnet-perf-scale-app`. Bygg om programmet genom att köra `dotnet build` som i följande exempel:

```console
dotnet build
```

## <a name="run-the-application"></a>Kör programmet

Nu när programmet har byggts om är det dags att köra programmet med den uppdaterade koden. Öppna en `Command Prompt`, om den inte redan är öppen, och navigera till `D:\git\storage-dotnet-perf-scale-app`.

Skriv `dotnet run` för att köra programmet.

```console
dotnet run
```

Aktiviteten `DownloadFilesAsync` visas i följande exempel:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Programmet läser de containrar i lagringskontot som anges i **storageconnectionstring**. Den itererar igenom Blobbarna med hjälp av metoden [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) och hämtar dem till den lokala datorn med hjälp av [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) -metoden.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Programmet läser de containrar i lagringskontot som anges i **storageconnectionstring**. Den itererar genom Blobbarna 10 i taget med [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) -metoden i behållarna och laddar ned dem till den lokala datorn med hjälp av [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) -metoden.

I följande tabell visas de [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) som definierats för varje BLOB när den laddas ned.

|Egenskap|Värde|Beskrivning|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Den här egenskapen inaktiverar kontrollen av MD5-hashen för innehållet som har överförts. Överföringen går snabbare om MD5-verifieringen inaktiveras. Däremot bekräftas inte giltigheten eller integriteten för de filer som överförs. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| falskt| Den här egenskapen anger om en MD5-hash beräknas och sparas.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Verifiera anslutningarna

När filerna laddas ned kan du verifiera antalet samtidiga anslutningar till lagringskontot. Öppna ett konsol fönster och skriv `netstat -a | find /c "blob:https"` . Det här kommandot visar antalet anslutningar som är öppna för tillfället. Som du kan se i följande exempel var över 280 anslutningar öppna när du hämtade filer från lagrings kontot.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Nästa steg

I del tre i serien har du lärt dig hur du laddar ned stora mängder data från ett lagrings konto, inklusive hur du:

> [!div class="checklist"]
> * Kör programmet
> * Validera antalet anslutningar

Gå till del fyra i serien för att kontrol lera data flödet och svars tiderna i portalen.

> [!div class="nextstepaction"]
> [Verifiera mått för dataflöde och svarstid i Portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
