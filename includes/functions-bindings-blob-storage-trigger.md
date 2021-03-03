---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "100381620"
---
### <a name="default"></a>Standardvärde

Du kan använda följande parameter typer för den Utlös ande blobben:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>81.1</sup>
* `CloudBlockBlob`<sup>81.1</sup>
* `CloudPageBlob`<sup>81.1</sup>
* `CloudAppendBlob`<sup>81.1</sup>

<sup>1</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller- `CloudBlockBlob` typ. Mer information finns i [samtidighets-och minnes användning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) längre fram i den här artikeln.

### <a name="additional-types"></a>Ytterligare typer

Appar som använder [5.0.0 eller en senare version av lagrings tillägget](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) kan också använda typer från [Azure SDK för .net](/dotnet/api/overview/azure/storage.blobs-readme). Den här versionen har stöd för de äldre `ICloudBlob` , `CloudBlockBlob` , `CloudPageBlob` , och typerna för att `CloudAppendBlob` prioritera följande typer:

- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Exempel som använder de här typerna finns i [GitHub-lagringsplatsen för tillägget](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
