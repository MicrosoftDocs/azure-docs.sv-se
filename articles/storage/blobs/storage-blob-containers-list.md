---
title: Lista BLOB-behållare med .NET-Azure Storage
description: Lär dig hur du listar BLOB-behållare i ditt Azure Storage-konto med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96937261"
---
# <a name="list-blob-containers-with-net"></a>Lista BLOB-behållare med .NET

När du listar behållarna i ett Azure Storage konto från din kod kan du ange ett antal alternativ för att hantera hur resultaten returneras från Azure Storage. Den här artikeln visar hur du visar behållare med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Förstå alternativ för behållar lista

Om du vill lista behållare i ditt lagrings konto, anropa någon av följande metoder:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Överlagringarna för dessa metoder ger ytterligare alternativ för att hantera hur behållare returneras av list åtgärden. De här alternativen beskrivs i följande avsnitt.

### <a name="manage-how-many-results-are-returned"></a>Hantera hur många resultat som returneras

Som standard returnerar en List åtgärd upp till 5000 resultat i taget. Om du vill returnera en mindre uppsättning resultat anger du ett värde som inte är noll för storleken på sidan med resultat som ska returneras.

Om ditt lagrings konto innehåller fler än 5000 behållare, eller om du har angett en sid storlek som visar en del av behållare i lagrings kontot, Azure Storage returnerar en *fortsättnings-token* med listan över behållare. En fortsättnings-token är ett ogenomskinligt värde som du kan använda för att hämta nästa uppsättning resultat från Azure Storage.

I din kod kontrollerar du värdet för fortsättnings-token för att avgöra om det är tomt (för .NET V12) eller null (för .NET V11 och tidigare). När tilläggs-token är null slutförs uppsättningen av resultat. Om tilläggs-token inte är null anropar du List metoden igen och skickar i fortsättnings-token för att hämta nästa uppsättning resultat, tills den fortsatta token är null.

### <a name="filter-results-with-a-prefix"></a>Filtrera resultat med ett prefix

Om du vill filtrera listan över behållare anger du en sträng för `prefix` parametern. Prefixlängden kan innehålla ett eller flera tecken. Azure Storage returnerar sedan bara de behållare vars namn börjar med prefixet.

### <a name="return-metadata"></a>Returnera metadata

Om du vill returnera metadata för behållare med resultaten anger du värdet för **metadata** för [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) Enum (för .net V12) eller [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Enum (för .net V11 och tidigare). Azure Storage innehåller metadata för varje behållare som returneras, så du behöver inte också hämta containerns metadata.

## <a name="example-list-containers"></a>Exempel: list behållare

I följande exempel visas en asynkron lista över behållare i ett lagrings konto som börjar med ett angivet prefix. I exemplet visas behållare som börjar med det angivna prefixet och returnerar det angivna antalet resultat per anrop till List åtgärden. Sedan används en fortsättnings-token för att hämta nästa resultat segment. Exemplet returnerar även containerns metadata med resultaten.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Lista behållare](/rest/api/storageservices/list-containers2)
- [Räkna upp BLOB-resurser](/rest/api/storageservices/enumerating-blob-resources)
