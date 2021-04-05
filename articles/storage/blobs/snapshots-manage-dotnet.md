---
title: Skapa och hantera en BLOB-ögonblicksbild i .NET
titleSuffix: Azure Storage
description: Lär dig hur du använder .NET-klient biblioteket för att skapa en skrivskyddad ögonblicks bild av en BLOB för att säkerhetskopiera BLOB-data vid en specifik tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543382"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Skapa och hantera en BLOB-ögonblicksbild i .NET

En ögonblicks bild är en skrivskyddad version av en blob som tas vid en tidpunkt. Den här artikeln visar hur du skapar och hanterar BLOB-ögonblicksbilder med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

Mer information om BLOB-ögonblicksbilder i Azure Storage finns i [BLOB-ögonblicksbilder](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Använd någon av följande metoder för att skapa en ögonblicks bild av en Block-Blob med version 12. x av Azure Storage klient biblioteket för .NET:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

I följande kod exempel visas hur du skapar en ögonblicks bild med version 12. x. Inkludera en referens till [Azure. Identity](https://www.nuget.org/packages/azure.identity) -biblioteket för att använda dina autentiseringsuppgifter för Azure AD för att auktorisera begär anden till tjänsten.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill skapa en ögonblicks bild av en Block-Blob med version 11. x av Azure Storage klient biblioteket för .NET, använder du någon av följande metoder:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

I följande kod exempel visas hur du skapar en ögonblicks bild med version 11. x. I det här exemplet anges ytterligare metadata för ögonblicks bilden när den skapas.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Ta bort ögonblicks bilder

Om du vill ta bort en BLOB måste du först ta bort alla ögonblicks bilder av denna blob. Du kan ta bort en ögonblicks bild individuellt eller ange att alla ögonblicks bilder ska tas bort när käll-bloben tas bort. Om du försöker ta bort en blob som fortfarande har ögonblicks bilder, uppstår ett fel.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill ta bort en blob och dess ögonblicks bilder med version 12. x av Azure Storage klient biblioteket för .NET, använder du någon av följande metoder och inkluderar [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) -uppräkningen:

- [Ta bort](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

I följande kod exempel visas hur du tar bort en blob och dess ögonblicks bilder i .NET, där `blobClient` är ett objekt av typen [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill ta bort en blob och dess ögonblicks bilder med version 11. x av Azure Storage klient biblioteket för .NET, använder du någon av följande BLOB-borttagnings metoder och inkluderar [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) -uppräkningen:

- [Ta bort](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

I följande kod exempel visas hur du tar bort en blob och dess ögonblicks bilder i .NET, där `blockBlob` är ett objekt av typen [CloudBlockBlob] [dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Nästa steg

- [BLOB-ögonblicksbilder](snapshots-overview.md)
- [BLOB-versioner](versioning-overview.md)
- [Mjuk borttagning för blobar](./soft-delete-blob-overview.md)