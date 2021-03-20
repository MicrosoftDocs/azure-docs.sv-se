---
title: Aktivera och hantera mjuk borttagning för blobbar
titleSuffix: Azure Storage
description: Aktivera mjuk borttagning för blobbar för att enklare återställa data när de ändras felaktigt eller tas bort.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995309"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Aktivera och hantera mjuk borttagning för blobbar

BLOB-mjuk borttagning skyddar dina data från att oavsiktligt eller felaktigt ändras eller tas bort. När BLOB Soft Delete är aktiverat för ett lagrings konto kan blobbar, BLOB-versioner och ögonblicks bilder i det lagrings kontot återställas efter att de har tagits bort, inom en kvarhållningsperiod som du anger.

Om det finns en risk att dina data av misstag kan ändras eller tas bort av ett program eller en annan lagrings konto användare rekommenderar Microsoft att du aktiverar BLOB-mjuk borttagning. Den här artikeln visar hur du aktiverar mjuk borttagning för blobbar. Mer information om BLOB-mjuk borttagning finns i [mjuk borttagning för blobbar](soft-delete-blob-overview.md).

Information om hur du också aktiverar mjuk borttagning för behållare finns i [Aktivera och hantera mjuk borttagning för behållare](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Aktivera mjuk borttagning av BLOB

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aktivera mjuk borttagning för blobbar på ditt lagrings konto med hjälp av Azure Portal:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com/).
1. Leta upp alternativet **data skydd** under **BLOB service**.
1. Ange egenskapen för **mjuk borttagning av BLOB** till *aktive rad*.
1. Under **bevarande principer** anger du hur länge borttagna blobar ska behållas genom att Azure Storage.
1. Spara ändringarna.

![Skärm bild av Azure-portalen med den valda Blob-tjänsten för data skydd.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Om du vill visa mjuka borttagna blobbar markerar du kryss rutan **Visa borttagna blobbar** .

![Skärm bild av sidan för data skydds-Blob-tjänsten med alternativet Visa borttagna blobbar markerat.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Om du vill visa mjuka borttagna ögonblicks bilder för en specifik BLOB väljer du bloben och klickar sedan på **Visa ögonblicks bilder**.

![Skärm bild av sidan Data Protection BLOB service med alternativet Visa ögonblicks bilder markerat.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Kontrol lera att kryss rutan **Visa borttagna ögonblicks bilder** är markerad.

![Skärm bild av sidan Visa ögonblicks bilder med alternativet Visa borttagna blobbar markerat.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Lägg märke till de nya blobb egenskaperna när du klickar på en mjuk borttagen BLOB eller ögonblicks bild. De anger när objektet togs bort och hur många dagar som återstår tills BLOB-eller BLOB-ögonblicksbilden har gått ut permanent. Om det mjuka borttagna objektet inte är en ögonblicks bild, kan du också välja att ta bort det.

![Skärm bild av information om ett Soft borttaget objekt.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Kom ihåg att om du tar bort en BLOB raderas även alla associerade ögonblicks bilder. Om du vill ta bort mjuka borttagna ögonblicks bilder för en aktiv BLOB, klickar du på blobben och väljer **ångra borttagning av alla ögonblicks bilder**.

![Skärm bild av information om en mjuk borttagen blob.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

När du tar bort en blobs ögonblicks bilder kan du klicka på **befordra** för att kopiera en ögonblicks bild över rot-bloben så att bloben kan återställas till ögonblicks bilden.

![Skärm bild av sidan Visa ögonblicks bilder med alternativet befordra markerat.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper. I följande exempel aktive ras mjuk borttagning för en delmängd av konton i en prenumeration:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Du kan kontrol lera att mjuk borttagning har Aktiver ATS genom att använda följande kommando:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Om du vill återställa blobar som har tagits bort av misstag anropar du **Undelete BLOB** på dessa blobbar. Kom ihåg att om du anropar **Undelete-BLOB**, både på aktiva och mjuka borttagna blobar, återställs alla tillhör ande mjuka borttagna ögonblicks bilder som aktiva. I följande exempel anropar **Undelete BLOB** för alla mjuka borttagna och aktiva blobbar i en behållare:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Använd följande kommando för att hitta den aktuella bevarande principen för mjuk borttagning:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Om du vill verifiera att mjuk borttagning är aktiverat, använder du följande kommando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Om du vill återställa blobar som tagits bort av misstag kan du anropa Undelete på dessa blobbar. Kom ihåg att om du anropar **Undelete**, både på aktiva och mjuka borttagna blobbar, återställs alla tillhör ande mjuka borttagna ögonblicks bilder som aktiva. I följande exempel anropar Undelete för alla mjuka borttagna och aktiva blobbar i en behållare:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Om du vill återställa till en angiven blob-version måste du först anropa Undelete på en blob och sedan kopiera önskad ögonblicks bild över blobben. I följande exempel återställs en Block-Blob till den senast skapade ögonblicks bilden:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Om du vill återställa blobar som har tagits bort av misstag anropar du **Undelete BLOB** på dessa blobbar. Kom ihåg att om du anropar **Undelete-BLOB**, både på aktiva och mjuka borttagna blobar, återställs alla tillhör ande mjuka borttagna ögonblicks bilder som aktiva. I följande exempel anropar **Undelete BLOB** i alla mjuk raderade och aktiva blobbar i en behållare:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa till en angiven blob-version anropar du först åtgärden **ta bort BLOB** och kopierar sedan den önskade ögonblicks bilden över blobben. I följande exempel återställs en Block-Blob till den senast skapade ögonblicks bilden:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Nästa steg

- [Mjuk borttagning för Blob Storage](./soft-delete-blob-overview.md)
- [BLOB-versioner](versioning-overview.md)