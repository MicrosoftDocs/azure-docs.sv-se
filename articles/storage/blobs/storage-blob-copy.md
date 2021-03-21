---
title: 'Kopiera en blob med Azure Storage-API: er'
description: Lär dig hur du kopierar en blob med hjälp av Azure Storage klient bibliotek.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059049"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Kopiera en blob med Azure Storage klient bibliotek

Den här artikeln visar hur du kopierar en BLOB i ett Azure Storage-konto. Det visar också hur du avbryter en asynkron kopierings åtgärd. I exempel koden används klient biblioteken för Azure Storage.

## <a name="about-copying-blobs"></a>Om att kopiera blobar

När du kopierar en BLOB inom samma lagrings konto är det en synkron åtgärd. När du kopierar mellan-konton är det en asynkron åtgärd.

Käll-bloben för en kopierings åtgärd kan vara en Block-Blob, en tilläggs-BLOB, en sid-BLOB eller en ögonblicks bild. Om det redan finns en mål-BLOB måste den vara av samma Blob-typ som käll-bloben. En befintlig mål-BLOB skrivs över.

Det går inte att ändra mål-bloben medan en kopierings åtgärd pågår. En mål-BLOB kan bara ha en utestående kopierings åtgärd. Med andra ord kan en BLOB inte vara målet för flera väntande kopierings åtgärder.

Hela käll-bloben eller filen kopieras alltid. Det finns inte stöd för att kopiera en mängd byte eller uppsättning block.

När en BLOB kopieras kopieras system egenskaperna till mål-bloben med samma värden.

En kopierings åtgärd kan ha följande format:

- Kopiera en käll-blob till en mål-BLOB med ett annat namn. Mål-bloben kan vara en befintlig blob av samma Blob-typ (block, tillägg eller sida) eller kan vara en ny blob som skapats av kopierings åtgärden.
- Kopiera en käll-blob till en mål-BLOB med samma namn, och ersätt mål-bloben på ett effektivt sätt. En sådan kopierings åtgärd tar bort alla icke allokerade block och skriver över målets BLOB-metadata.
- Kopiera en källfil i Azure File Service till en mål-blob. Mål-bloben kan vara en befintlig Block-Blob eller en ny Block-Blob som skapats av kopierings åtgärden. Det finns inte stöd för att kopiera från filer till Page blobbar eller bifogade blobbar.
- Kopiera en ögonblicks bild över dess bas-blob. Genom att befordra en ögonblicks bild till positionen för bas-bloben kan du återställa en tidigare version av en blob.
- Kopiera en ögonblicks bild till en mål-BLOB med ett annat namn. Den resulterande mål-bloben är en skrivbar blob och inte en ögonblicks bild.

## <a name="copy-a-blob"></a>Kopiera en BLOB

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill kopiera en BLOB anropar du någon av följande metoder:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri`Metoderna och `StartCopyFromUriAsync` returnerar ett [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) -objekt som innehåller information om kopierings åtgärden.

Följande kod exempel hämtar en [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) som representerar en tidigare skapad blob och kopierar den till en ny BLOB i samma behållare:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill kopiera en BLOB anropar du någon av följande metoder:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy`Metoderna och `StartCopyAsync` returnerar ett kopierings-ID-värde som används för att kontrol lera status eller avbryta kopierings åtgärden.

I följande kod exempel hämtas en referens till en tidigare skapad blob och kopieras till en ny BLOB i samma behållare:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Om du vill kopiera en BLOB anropar du metoden [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) . `start_copy_from_url`Metoden returnerar en ord lista som innehåller information om kopierings åtgärden.

Följande kod exempel hämtar en [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) som representerar en tidigare skapad blob och kopierar den till en ny BLOB i samma behållare:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Avbryt en kopierings åtgärd

Om du avbryter en kopierings åtgärd i en mål-BLOB med längden noll. Metadata för mål-bloben kommer dock att ha de nya värdena som kopieras från käll-bloben eller uttryckligen anges under kopierings åtgärden. Om du vill behålla de ursprungliga metadata från före kopian ska du skapa en ögonblicks bild av mål-bloben innan du anropar någon av kopierings metoderna.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Kontrol lera egenskapen [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) på mål-bloben för att hämta status för kopierings åtgärden. Den sista blobben kommer att allokeras när kopieringen är klar.

När du avbryter en kopierings åtgärd anges mål-blobens kopierings status till [CopyStatus. avbruten](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metoderna [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) och [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) avbryter en pågående kopierings åtgärd.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Kontrol lera egenskapen [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) på mål-bloben för att hämta status för kopierings åtgärden. Den sista blobben kommer att allokeras när kopieringen är klar.

När du avbryter en kopierings åtgärd anges mål-blobens kopierings status till [CopyStatus. avbruten](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metoderna [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) och [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) avbryter en pågående kopierings åtgärd.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Kontrol lera "status"-posten i [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) -ordlistan som returnerades av [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) metod för att hämta status för kopierings åtgärden. Den sista blobben kommer att allokeras när kopieringen är klar.

När du avbryter en kopierings åtgärd anges [statusen](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) "avbruten".

Metoden [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) avbryter en pågående kopierings åtgärd.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure-SDK:er

Få mer information om Azure-SDK: er:

 - [Azure SDK för .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK för Java](https://github.com/azure/azure-sdk-for-java)
 - [Azure SDK för Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure SDK för Java Script](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Nästa steg

Följande avsnitt innehåller information om hur du kopierar blobbar och avbryter pågående kopierings åtgärder med hjälp av Azure REST-API: er.

- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Avbryt kopiering av BLOB](/rest/api/storageservices/abort-copy-blob)
