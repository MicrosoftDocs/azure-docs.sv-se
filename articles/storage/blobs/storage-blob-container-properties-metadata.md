---
title: Använda .NET för att hantera egenskaper och metadata för en BLOB-behållare
titleSuffix: Azure Storage
description: Lär dig hur du ställer in och hämtar system egenskaper och lagrar anpassade metadata i BLOB-behållare i ditt Azure Storage konto med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2020
ms.author: tamram
ms.custom: devx-track-csharp
ms.openlocfilehash: 9fb77179a00969da7a3dc372dc70c99cfe4220ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091006"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Hantera behållar egenskaper och metadata med .NET

BLOB-behållare stöder system egenskaper och användardefinierade metadata, förutom de data som de innehåller. Den här artikeln visar hur du hanterar system egenskaper och användardefinierade metadata med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **System egenskaper**: system egenskaper finns på varje Blob Storage-resurs. En del av dem kan läsas eller anges, medan andra är skrivskyddade. Under försättsblad motsvarar vissa system egenskaper vissa standard-HTTP-huvuden. Azure Storage klient bibliotek för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: användardefinierade metadata består av ett eller flera namn-värdepar som du anger för en Blob Storage-resurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadata-värden är enbart för egna behov och påverkar inte hur resursen beter sig.

Metadata-namn/värde-par är giltiga HTTP-huvuden och bör därför följa alla begränsningar som gäller för HTTP-huvuden. Metadata-namn måste vara giltiga HTTP-huvudnamn och giltiga C#-identifierare, får bara innehålla ASCII-tecken och bör behandlas som Skift läges okänsligt. Metadata-värden som innehåller icke-ASCII-tecken ska vara base64-kodade eller URL-kodade.

## <a name="retrieve-container-properties"></a>Hämta behållar egenskaper

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Anropa någon av följande metoder för att hämta behållar egenskaper:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)

Följande kod exempel hämtar en behållares system egenskaper och skriver några egenskaps värden till ett konsol fönster:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerProperties":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Att hämta egenskaps-och metadata-värden för en Blob Storage-resurs är en två stegs process. Innan du kan läsa dessa värden måste du explicit hämta dem genom att anropa metoden **FetchAttributes** eller **FetchAttributesAsync** . Undantaget till den här regeln är att metoderna **exists** och **ExistsAsync** anropar lämplig **FetchAttributes** -metod under försättsblad. När du anropar någon av dessa metoder behöver du inte också anropa **FetchAttributes**.

> [!IMPORTANT]
> Om du upptäcker att egenskaps-eller metadata-värden för en lagrings resurs inte har fyllts i kontrollerar du att koden anropar metoden **FetchAttributes** eller **FetchAttributesAsync** .

Anropa någon av följande metoder för att hämta behållar egenskaper:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Följande kod exempel hämtar en behållares system egenskaper och skriver några egenskaps värden till ett konsol fönster:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

## <a name="set-and-retrieve-metadata"></a>Ange och hämta metadata

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Du kan ange metadata som ett eller flera namn-värdepar på en BLOB-eller container resurs. Om du vill ange metadata lägger du till namn-värdepar till ett [IDictionary](/dotnet/api/system.collections.idictionary) -objekt och anropar sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadataasync)

Namnet på dina metadata måste följa namngivnings konventionerna för C#-identifierare. Metadata-namn bevarar det Skift läge som de skapades i, men är Skift läges okänsliga när de har angetts eller lästs. Om två eller flera metadata-huvuden med samma namn skickas för en resurs, separerar Blob Storage varandra och sammanfogar de två värdena och returnerar HTTP-svarskod 200 (OK).

I följande kod exempel anges metadata i en behållare.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddContainerMetadata":::

Anropa en av följande metoder för att hämta metadata:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync).

Läs sedan värdena, som du ser i exemplet nedan.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerMetadata":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Du kan ange metadata som ett eller flera namn-värdepar på en BLOB-eller container resurs. Om du vill ange metadata lägger du till namn-värdepar i **metadata** -samlingen på resursen och anropar sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Namnet på dina metadata måste följa namngivnings konventionerna för C#-identifierare. Metadata-namn bevarar det Skift läge som de skapades i, men är Skift läges okänsliga när de har angetts eller lästs. Om två eller flera metadata-huvuden med samma namn skickas för en resurs, separerar Blob Storage varandra och sammanfogar de två värdena och returnerar HTTP-svarskod 200 (OK).

I följande kod exempel anges metadata i en behållare. Ett värde anges med samlingens **Add** -metod. Det andra värdet anges med implicit nyckel/värde-syntax. Båda är giltiga.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Om du vill hämta metadata anropar du metoden **FetchAttributes** eller **FetchAttributesAsync** på blobben eller behållaren för att fylla i **metadata** -samlingen och läser sedan värdena, som visas i exemplet nedan.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Hämta container egenskaper-åtgärd](/rest/api/storageservices/get-container-properties)
- [Ange metadata åtgärd för container](/rest/api/storageservices/set-container-metadata)
- [Hämta metadata åtgärd för behållare](/rest/api/storageservices/get-container-metadata)
