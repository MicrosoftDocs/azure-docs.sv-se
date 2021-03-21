---
title: Azure Blob Storage som Event Grid källa
description: Beskriver de egenskaper som har angetts för Blob Storage-händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 893e86ecf220ceb327eed9c6f95be4c7ed1afb1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363652"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Azure Blob Storage som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Blob Storage-händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure Blob Storage som en händelse källa.


>[!NOTE]
> Endast lagrings konton av typen **StorageV2 (generell användning v2)**, **BlockBlobStorage** och **BlobStorage** stöder händelse integrering. **Lagring (generell användning v1)** stöder *inte* integrering med event Grid.

## <a name="available-event-types"></a>Tillgängliga händelse typer

### <a name="list-of-events-for-blob-rest-apis"></a>Lista över händelser för BLOB REST-API: er

Dessa händelser utlöses när en klient skapar, ersätter eller tar bort en BLOB genom att anropa BLOB REST-API: er.

> [!NOTE]
> `$logs`-Och `$blobchangefeed` -behållarna är inte integrerade med event Grid, så aktiviteten i dessa behållare genererar inte händelser. När du använder DFS-slutpunkten *`(abfss://URI) `* för icke hierarkiskt bevarade namn för icke hierarkiskt namn genereras händelser, men BLOB-slutpunkten *`(wasb:// URI)`* genererar händelser.

 |Händelse namn |Beskrivning|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Utlöses när en BLOB skapas eller ersätts. <br>Mer specifikt utlöses den här händelsen när klienter använder de `PutBlob` -, `PutBlockList` -eller- `CopyBlob` åtgärder som är tillgängliga i BLOB-REST API.   |
 |**Microsoft. Storage. BlobDeleted** |Utlöses när en BLOB tas bort. <br>Mer specifikt utlöses den här händelsen när klienterna anropar den `DeleteBlob` åtgärd som är tillgänglig i BLOB-REST API. |

> [!NOTE]
> För **Azure Blob Storage**, om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad, filtrera händelsen för-, `CopyBlob` `PutBlob` -och `PutBlockList` REST API-anrop. Dessa API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista över händelser för Azure Data Lake Storage gen 2 REST-API: er

Dessa händelser utlöses om du aktiverar ett hierarkiskt namn område på lagrings kontot och klienter använder Azure Data Lake Storage Gen2 REST-API: er. Mer information formation Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Händelse namn|Beskrivning|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Utlöses när en BLOB skapas eller ersätts. <br>Mer specifikt utlöses den här händelsen när klienter använder de `CreateFile` och- `FlushWithClose` åtgärder som är tillgängliga i Azure Data Lake Storage Gen2 REST API. |
|**Microsoft. Storage. BlobDeleted** |Utlöses när en BLOB tas bort. <br>Mer specifikt utlöses den här händelsen när klienterna anropar den `DeleteFile` åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API. |
|**Microsoft. Storage. BlobRenamed**|Utlöses när en BLOB får ett nytt namn. <br>Mer specifikt utlöses den här händelsen när klienter använder den `RenameFile` åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
|**Microsoft. Storage. DirectoryCreated**|Utlöses när en katalog skapas. <br>Mer specifikt utlöses den här händelsen när klienter använder den `CreateDirectory` åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
|**Microsoft. Storage. DirectoryRenamed**|Utlöses när en katalog får ett nytt namn. <br>Mer specifikt utlöses den här händelsen när klienter använder den `RenameDirectory` åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
|**Microsoft. Storage. DirectoryDeleted**|Utlöses när en katalog tas bort. <br>Mer specifikt utlöses den här händelsen när klienter använder den `DeleteDirectory` åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> För **Azure Data Lake Storage Gen2**, om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad, filtrera händelsen för REST API- `FlushWithClose` anropet. Detta API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](./how-to-filter-events.md).

## <a name="example-event"></a>Exempel händelse
När en händelse utlöses skickar Event Grid-tjänsten data om händelsen för att prenumerera på slut punkten. Det här avsnittet innehåller ett exempel på hur data ska se ut för varje Blob Storage-händelse.

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel, med undantag för dessa ändringar:

* `dataVersion`Nyckeln har angetts till värdet `2` .

* `data.api`Nyckeln anges till strängen `CreateFile` eller `FlushWithClose` .

* `contentOffset`Nyckeln ingår i data uppsättningen.

> [!NOTE]
> Om program använder `PutBlockList` åtgärden för att överföra en ny blob till kontot kommer data inte att innehålla dessa ändringar.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel, med undantag för dessa ändringar:

* `dataVersion`Nyckeln har angetts till värdet `2` .

* `data.api`Nyckeln har angetts till strängen `DeleteFile` .

* `url`Nyckeln innehåller sökvägen `dfs.core.windows.net` .

> [!NOTE]
> Om program använder `DeleteBlob` åtgärden för att ta bort en BLOB från kontot kommer data inte att innehålla dessa ändringar.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel, med undantag för dessa ändringar:

* `data.api`Nyckeln anges till strängen `CreateFile` eller `FlushWithClose` .
* `contentOffset`Nyckeln ingår i data uppsättningen.

> [!NOTE]
> Om program använder `PutBlockList` åtgärden för att överföra en ny blob till kontot kommer data inte att innehålla dessa ändringar.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel, med undantag för dessa ändringar:


* `data.api`Nyckeln har angetts till strängen `DeleteFile` .
* `url`Nyckeln innehåller sökvägen `dfs.core.windows.net` .

> [!NOTE]
> Om program använder `DeleteBlob` åtgärden för att ta bort en BLOB från kontot kommer data inte att innehålla dessa ändringar.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted-händelse

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>Händelse egenskaper

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `eventType` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `eventTime` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Händelse data för Blob Storage. |
| `dataVersion` | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| `metadataVersion` | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `source` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| `type` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `time` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Händelse data för Blob Storage. |
| `specversion` | sträng | CloudEvents schema Specifikations version. |

---

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `api` | sträng | Den åtgärd som utlöste händelsen. |
| `clientRequestId` | sträng | ett ID för begäran som tillhandahållits för Storage API-åtgärden. Detta ID kan användas för att korrelera Azure Storage diagnostikloggar med hjälp av fältet "client-Request-ID" i loggarna och kan tillhandahållas i klient begär Anden med hjälp av huvudet "x-MS-client-Request-ID". Se [logg format](/rest/api/storageservices/storage-analytics-log-format). |
| `requestId` | sträng | Service-genererat förfrågnings-ID för Storage API-åtgärden. Kan användas för att korrelera Azure Storage diagnostikloggar som använder fältet "Request-ID-huvud" i loggarna och returneras från initiering av API-anrop i huvudet "x-MS-Request-ID". Se [logg format](/rest/api/storageservices/storage-analytics-log-format). |
| `eTag` | sträng | Det värde som du kan använda för att köra åtgärder villkorligt. |
| `contentType` | sträng | Den innehålls typ som angetts för blobben. |
| `contentLength` | heltal | Storleken på blobben i byte. |
| `blobType` | sträng | Typ av BLOB. Giltiga värden är antingen "BlockBlob" eller "PageBlob". |
| `contentOffset` | antal | Offset i byte för en Skriv åtgärd som tas vid den tidpunkt då programmet för händelse aktivering slutförde skrivning till filen. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område.|
| `destinationUrl` |sträng | URL till den fil som ska finnas när åtgärden har slutförts. Om en fil till exempel byter namn, `destinationUrl` innehåller egenskapen URL: en för det nya fil namnet. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område.|
| `sourceUrl` |sträng | URL till den fil som finns innan åtgärden slutfördes. Om en fil till exempel byter namn, `sourceUrl` innehåller URL: en för det ursprungliga fil namnet innan namnbytes åtgärden. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område. |
| `url` | sträng | Sökvägen till blobben. <br>Om klienten använder en BLOB-REST API, har URL: en den här strukturen: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>Om klienten använder en Data Lake Storage REST API, har URL: en den här strukturen: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| `recursive` | sträng | `True` köra åtgärden på alla underordnade kataloger. annars `False` . <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område. |
| `sequencer` | sträng | Ett ogenomskinligt sträng värde som representerar den logiska sekvensen av händelser för ett visst BLOB-namn.  Användare kan använda standard sträng jämförelse för att förstå den relativa sekvensen av två händelser på samma BLOB-namn. |
| `storageDiagnostics` | objekt | Diagnostikdata ingår ibland i Azure Storages tjänsten. I förekommande fall ska ignoreras av händelse konsumenter. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Blob Storage-händelser till en webhook. |
| [Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure PowerShell för att skicka Blob Storage-händelser till en webhook. |
| [Snabb start: skapa och dirigera Blob Storage-händelser med Azure Portal](blob-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka Blob Storage-händelser till en webhook. |
| [Azure CLI: prenumerera på händelser för ett Blob Storage-konto](./scripts/event-grid-cli-blob.md) | Exempel skript som prenumererar på en händelse för ett Blob Storage-konto. Den skickar händelsen till en webhook. |
| [PowerShell: prenumerera på händelser för ett Blob Storage-konto](./scripts/event-grid-powershell-blob.md) | Exempel skript som prenumererar på en händelse för ett Blob Storage-konto. Den skickar händelsen till en webhook. |
| [Resource Manager-mall: skapa blob-lagring och prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. Den skickar händelser till en webhook. |
| [Översikt: reagerar på Blob Storage-händelser](../storage/blobs/storage-blob-event-overview.md) | Översikt över integrering av Blob Storage med Event Grid. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* En introduktion till att arbeta med Blob Storage-händelser finns i [dirigera Blob Storage-händelser – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
