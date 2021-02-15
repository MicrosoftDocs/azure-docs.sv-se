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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381670"
---
### <a name="default"></a>Standardvärde

Du kan binda till följande typer för att skriva blobbar:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>81.1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>11.2</sup>
* `CloudBlockBlob`<sup>11.2</sup>
* `CloudPageBlob`<sup>11.2</sup>
* `CloudAppendBlob`<sup>11.2</sup>

<sup>1</sup> kräver "i"-bindning `direction` i *function.jspå* eller `FileAccess.Read` i ett C#-klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller- `CloudBlockBlob` typ. Mer information finns i [samtidighets-och minnes användning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) tidigare i den här artikeln.

### <a name="additional-types"></a>Ytterligare typer

Appar som använder [5.0.0 eller en senare version av lagrings tillägget](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) kan också använda typer från [Azure SDK för .net](/dotnet/api/overview/azure/storage.blobs-readme). Den här versionen har stöd för de äldre `CloudBlobContainer` ,,,, `CloudBlobDirectory` och typerna för att `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` `CloudAppendBlob` prioritera följande typer:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> kräver "i"-bindning `direction` i *function.jspå* eller `FileAccess.Read` i ett C#-klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Exempel som använder de här typerna finns i [GitHub-lagringsplatsen för tillägget](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
