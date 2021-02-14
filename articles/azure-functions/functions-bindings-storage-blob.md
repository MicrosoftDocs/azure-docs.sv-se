---
title: Azure Blob Storage-utlösare och bindningar för Azure Functions
description: Lär dig hur du använder Azure Blob Storage-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381536"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob Storage-bindningar för Azure Functions översikt

Azure Functions integreras med [Azure Storage](../storage/index.yml) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Blob Storage kan du skapa funktioner som reagerar på ändringar i BLOB-data samt läsa och skriva värden.

| Action | Typ |
|---------|---------|
| Kör en funktion som data ändringar i Blob Storage | [Utlösare](./functions-bindings-storage-blob-trigger.md) |
| Läsa Blob Storage-data i en funktion | [Binda in](./functions-bindings-storage-blob-input.md) |
| Tillåt en funktion att skriva BLOB Storage-data |[Utgående bindning](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

#### <a name="storage-extension-5x-and-higher"></a>Lagrings tillägg 5. x och högre

En ny version av tillägget lagrings bindningar är tillgänglig som ett [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)för för hands versioner. I den här för hands versionen introduceras möjligheten att [ansluta med en identitet i stället för en hemlighet](./functions-reference.md#configure-an-identity-based-connection). För .NET-program ändras även de typer som du kan binda till, vilket ersätter typerna från `WindowsAzure.Storage` och `Microsoft.Azure.Storage` med nyare typer från [Azure. Storage. blob](/dotnet/api/azure.storage.blobs).

> [!NOTE]
> För hands versionen ingår inte i ett tilläggs paket och måste installeras manuellt. Lägg till en referens till paketet för .NET-appar. För alla andra typer av appar, se [Uppdatera dina tillägg].

[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.jspå Inställningar

> [!NOTE]
> Det här avsnittet gäller inte när du använder tilläggs versioner före 5.0.0. För dessa versioner finns det inga globala konfigurations inställningar för blobbar.

I det här avsnittet beskrivs globala konfigurations inställningar som är tillgängliga för den här bindningen när du använder [tilläggs version 5.0.0 och högre](#storage-extension-5x-and-higher). Exemplet *host.jspå* filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i funktions versionerna 2. x och mer finns i [host.jsom referens för Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (antalet tillgängliga kärnor)|Heltals antalet samtidiga anrop som tillåts för varje BLOB-utlöst funktion. Det minsta tillåtna värdet är 1.|

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när Blob Storage-data ändras](./functions-bindings-storage-blob-trigger.md)
- [Läsa Blob Storage-data när en funktion körs](./functions-bindings-storage-blob-input.md)
- [Skriv Blob Storage-data från en funktion](./functions-bindings-storage-blob-output.md)
