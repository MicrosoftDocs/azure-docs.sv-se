---
title: Hantera egenskaper och metadata för en blob med .NET-Azure Storage
description: Lär dig hur du ställer in och hämtar system egenskaper och lagrar anpassade metadata på blobbar i ditt Azure Storage konto med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 984fb00e163a090534da1fb41850dcfef6c5d516
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521537"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Hantera BLOB-egenskaper och metadata med .NET

Förutom de data som de innehåller, stöder blobbar system egenskaper och användardefinierade metadata. Den här artikeln visar hur du hanterar system egenskaper och användardefinierade metadata med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **System egenskaper**: system egenskaper finns på varje Blob Storage-resurs. En del av dem kan läsas eller anges, medan andra är skrivskyddade. Under försättsblad motsvarar vissa system egenskaper vissa standard-HTTP-huvuden. Azure Storage klient bibliotek för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: användardefinierade metadata består av ett eller flera namn-värdepar som du anger för en Blob Storage-resurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadata-värden är enbart för egna behov och påverkar inte hur resursen beter sig.

> [!NOTE]
> Taggar för BLOB index ger också möjlighet att lagra godtyckliga användardefinierade nyckel/värde-attribut tillsammans med en Azure Blob Storage-resurs. Medan det liknar metadata indexeras bara BLOB-taggar automatiskt och görs sökbara av den interna Blob-tjänsten. Metadata kan inte indexeras och frågas om du inte använder en separat tjänst, till exempel Azure Search.
>
> Mer information om den här funktionen finns i [Hantera och hitta data i Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Ange och hämta egenskaper

I följande kod exempel anges- `ContentType` och- `ContentLanguage` System egenskaperna för en blob.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Anropa [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) eller [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync)för att ange egenskaper för en blob. Alla egenskaper som inte uttryckligen anges rensas. I följande kod exempel får du först de befintliga egenskaperna i blobben och använder dem sedan för att fylla i de huvuden som inte uppdateras.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

Följande kod exempel hämtar en blobs system egenskaper och visar några av värdena.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Hämtning av metadata och egenskaps värden för en Blob Storage-resurs är en två stegs process. Innan du kan läsa dessa värden måste du explicit hämta dem genom att anropa `FetchAttributes` metoden eller `FetchAttributesAsync` . Undantaget till den här regeln är att `Exists` metoderna och `ExistsAsync` anropar lämplig `FetchAttributes` metod under försättsblad. När du anropar någon av dessa metoder behöver du inte också anropa `FetchAttributes` .

> [!IMPORTANT]
> Om du upptäcker att egenskaps-eller metadata-värden för en lagrings resurs inte har fyllts i kontrollerar du att koden anropar `FetchAttributes` eller- `FetchAttributesAsync` metoden.

Om du vill hämta BLOB-egenskaper anropar du- `FetchAttributes` eller- `FetchAttributesAsync` metoden på blobben för att fylla i `Properties` egenskapen.

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

Du kan ange metadata som ett eller flera namn-värdepar på en BLOB-eller container resurs. Om du vill ange metadata lägger du till namn-värdepar i `Metadata` samlingen på resursen. Anropa sedan någon av följande metoder för att skriva värdena:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

Metadata-/värdepar är giltiga HTTP-huvuden och bör följa alla begränsningar som gäller för HTTP-huvuden. Metadata-namn måste vara giltiga HTTP-huvudnamn och giltiga C#-identifierare, får bara innehålla ASCII-tecken och bör behandlas som Skift läges okänsligt. [Base64-koda](/dotnet/api/system.convert.tobase64string) eller [URL-koda metadata-](/dotnet/api/system.web.httputility.urlencode) värden som innehåller icke-ASCII-tecken.

Namnet på dina metadata måste följa namngivnings konventionerna för C#-identifierare. Metadata-namn upprätthåller när de skapades, men är Skift läges känsliga när de har angetts eller lästs. Om två eller flera metadata-huvuden med samma namn skickas för en resurs returnerar Azure Blob Storage HTTP-felkoden 400 (felaktig begäran).

I följande kod exempel anges metadata för en blob. Ett värde anges med samlings `Add` metoden. Det andra värdet anges med implicit nyckel/värde-syntax.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

Följande kod exempel läser metadata för en blob.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill hämta metadata anropar du metoden [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) eller [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) på blobben eller behållaren för att fylla i [metadata](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) -samlingen och läser sedan värdena, som visas i exemplet nedan. **GetProperties** -metoderna hämtar BLOB-egenskaper och metadata i ett enda anrop. Detta skiljer sig från REST API: er som kräver separata anrop för att [Hämta BLOB-egenskaper](/rest/api/storageservices/get-blob-properties) och [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill hämta metadata anropar du `FetchAttributes` eller- `FetchAttributesAsync` metoden på bloben eller behållaren för att fylla i `Metadata` samlingen och läser sedan värdena, som visas i exemplet nedan.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
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

- [Ange BLOB Properties-åtgärd](/rest/api/storageservices/set-blob-properties)
- [Hämta BLOB Properties-åtgärd](/rest/api/storageservices/get-blob-properties)
- [Ange åtgärd för BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta BLOB metadata-åtgärd](/rest/api/storageservices/get-blob-metadata)