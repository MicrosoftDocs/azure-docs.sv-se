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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455926"
---
BLOB-utlösaren innehåller flera egenskaper för metadata. Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Dessa värden har samma semantik som [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) -typen.

|Egenskap  |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den Utlös ande blobben.|
|`Uri`|`System.Uri`|Blobens URI för den primära platsen.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens system egenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blobben.|

Till exempel loggar följande C#-skript och JavaScript-exempel sökvägen till den Utlös ande blobben, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```