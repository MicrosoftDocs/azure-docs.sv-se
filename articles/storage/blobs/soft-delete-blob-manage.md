---
title: Hantera och återställa mjuka borttagna blobar
titleSuffix: Azure Storage
description: Hantera och återställa mjuka borttagna blobbar och ögonblicks bilder med Azure Portal eller med Azure Storage klient bibliotek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556216"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Hantera och återställa mjuka borttagna blobar

BLOB-mjuk borttagning skyddar en enskild blob och dess versioner, ögonblicks bilder och metadata från oavsiktliga borttagningar eller överskrivning genom att underhålla borttagna data i systemet under en angiven tids period. Under kvarhållningsperioden kan du återställa blobben till dess tillstånd vid borttagning. När kvarhållningsperioden har gått ut tas bloben bort permanent. Mer information om mjuk borttagning av BLOB finns i [mjuk borttagning för blobbar](soft-delete-blob-overview.md).

BLOB-mjuk borttagning är en del av en omfattande data skydds strategi för BLOB-data. Mer information om Microsofts rekommendationer för data skydd finns i [Översikt över data skydd](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Hantera mjuk borttagning av blobar med Azure Portal

Du kan använda Azure Portal för att visa och återställa mjuka borttagna blobbar och ögonblicks bilder.

### <a name="view-deleted-blobs"></a>Visa borttagna blobar

När blobbar är mjuk-borttagna är de osynliga i Azure Portal som standard. Om du vill visa mjuka borttagna blobbar går du till sidan **Översikt** för behållaren och växlar inställningen **Visa borttagna blobbar** . Mjuk borttagning av blobbar visas med statusen **borttagen**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Skärm bild som visar hur du listar mjuka borttagna blobbar i Azure Portal":::

Välj sedan den borttagna blobben i listan över blobbar för att visa dess egenskaper. Under fliken **Översikt** ser du att blobens status är inställt på **borttagen**. Portalen visar också antalet dagar tills bloben tas bort permanent.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Skärm bild som visar egenskaper för mjuk borttagning av BLOB i Azure Portal":::

### <a name="view-deleted-snapshots"></a>Visa borttagna ögonblicks bilder

Om du tar bort en BLOB raderas även alla ögonblicks bilder som är associerade med blobben. Om en mjuk borttagen BLOB har ögonblicks bilder kan även de borttagna ögonblicks bilderna visas i portalen. Visa egenskaperna för den mjuk-borttagna blobben och navigera sedan till fliken **ögonblicks bilder** och växla **Visa borttagna ögonblicks bilder**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Skärm bild som visar ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Återställa mjuka borttagna objekt när versions hantering har inaktiverats

Om du vill återställa en mjuk borttagen BLOB i Azure Portal när BLOB-versionen inte är aktive rad, visar du först blobens egenskaper och väljer sedan knappen **ta bort** på fliken **Översikt** . Om du återställer en BLOB återställs även eventuella ögonblicks bilder som togs bort under perioden för att kvarhålla borttagningar.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Skärm bild som visar hur du återställer en mjuk borttagen BLOB i Azure Portal":::

Om du vill befordra en mjuk borttagen ögonblicks bild till bas-bloben ska du först kontrol lera att blobens mjuk borttagna ögonblicks bilder har återställts. Välj knappen **ta bort** för att återställa blobens borttagna ögonblicks bilder, även om själva bas-bloben inte har varit mjuk-borttagen. Välj sedan den ögonblicks bild som du vill befordra och Använd knappen **Höj ögonblicks bild** för att skriva över bas-bloben med innehållet i ögonblicks bilden.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Skärm bild som visar hur du höjer upp en ögonblicks bild till bas-bloben":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Återställ mjuka borttagna blobar när versions hantering är aktive rad

Om du vill återställa en mjuk borttagen BLOB i Azure Portal när versions hantering är aktive rad väljer du den mjuk-borttagna blobben för att visa dess egenskaper och väljer sedan fliken **versioner** . Välj den version som du vill befordra till den aktuella versionen och välj sedan **gör aktuell version**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Skärm bild som visar hur du befordrar en version för att återställa en BLOB i Azure Portal":::

Om du vill återställa borttagna versioner eller ögonblicks bilder när versions hantering har Aktiver ATS visar du egenskaperna för blobben och väljer sedan knappen **ta bort** på fliken **Översikt** .

> [!NOTE]
> När versions hantering är aktive rad återställs alla borttagnings bara versioner eller ögonblicks bilder, men inte bas-bloben om du väljer knappen **ta bort** på en borttagen blob. Om du vill återställa bas-bloben måste du uppgradera en tidigare version.

## <a name="manage-soft-deleted-blobs-with-code"></a>Hantera mjuk borttagning av blobar med kod

Du kan använda Azure Storage klient bibliotek för att återställa en mjuk borttagen BLOB eller ögonblicks bild. I följande exempel visas hur du använder .NET-klient biblioteket.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Återställa mjuka borttagna objekt när versions hantering har inaktiverats

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill återställa borttagna blobar när versions hantering inte har Aktiver ATS anropar du åtgärden [ångra borttagning av bloben](/rest/api/storageservices/undelete-blob) på dessa blobbar. **Undelete BLOB** -åtgärden återställer mjuka borttagna blobbar och eventuella borttagna ögonblicks bilder som är associerade med dessa blobbar.

Att anropa **Undelete-BLOB** i en blob som inte har tagits bort har ingen påverkan. I följande exempel anropar **Undelete BLOB** på alla blobbar i en behållare och återställer de mjuka borttagna blobarna och deras ögonblicks bilder:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Om du vill återställa en speciell version anropar du först åtgärden **ångra borttagning av BLOB** på bas-BLOB eller version och kopierar sedan den önskade versionen över bas-bloben. I följande exempel återställs en Block-Blob till den senast sparade versionen:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill återställa borttagna blobar när versions hantering inte har Aktiver ATS anropar du åtgärden [ångra borttagning av bloben](/rest/api/storageservices/undelete-blob) på dessa blobbar. **Undelete BLOB** -åtgärden återställer mjuka borttagna blobbar och eventuella borttagna ögonblicks bilder som är associerade med dessa blobbar.

Att anropa **Undelete-BLOB** i en blob som inte har tagits bort har ingen påverkan. I följande exempel anropar **Undelete BLOB** på alla blobbar i en behållare och återställer de mjuka borttagna blobarna och deras ögonblicks bilder:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa en speciell ögonblicks bild anropar du först åtgärden **ångra borttagning av BLOB** på bas-bloben och kopierar sedan den önskade ögonblicks bilden över bas-bloben. I följande exempel återställs en Block-Blob till den senast skapade ögonblicks bilden:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Återställ mjuka borttagna blobar när versions hantering är aktive rad

Om du vill återställa en mjuk borttagen BLOB när versions hantering är aktive rad, kopierar du en tidigare version över bas-bloben med en [copy-BLOB](/rest/api/storageservices/copy-blob) eller [Kopiera BLOB från URL](/rest/api/storageservices/copy-blob-from-url) -åtgärd.  

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Inte tillämpligt. BLOB-versioner stöds bara i Azure Storage klient bibliotek version 12. x och högre.

---

## <a name="next-steps"></a>Nästa steg

- [Mjuk borttagning för Blob Storage](./soft-delete-blob-overview.md)
- [Aktivera mjuk borttagning för blobar](soft-delete-blob-enable.md)
- [BLOB-versioner](versioning-overview.md)
