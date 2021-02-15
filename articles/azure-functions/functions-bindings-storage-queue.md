---
title: Azure Queue Storage-utlösare och bindningar för Azure Functions översikt
description: Lär dig hur du använder Azure Queue Storage-utlösare och utgående bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381485"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue Storage-utlösare och bindningar för Azure Functions översikt

Azure Functions kan köras som nya Azure Queue Storage-meddelanden skapas och kan skriva Kömeddelanden i en funktion.

| Action | Typ |
|---------|---------|
| Kör en funktion som ändringar i Queue Storage-data | [Utlösare](./functions-bindings-storage-queue-trigger.md) |
| Skriva kö lagrings meddelanden |[Utgående bindning](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

#### <a name="storage-extension-5x-and-higher"></a>Lagrings tillägg 5. x och högre

En ny version av tillägget lagrings bindningar är tillgänglig som ett [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)för för hands versioner. I den här för hands versionen introduceras möjligheten att [ansluta med en identitet i stället för en hemlighet](./functions-reference.md#configure-an-identity-based-connection). För .NET-program ändras även de typer som du kan binda till, vilket ersätter typerna från `WindowsAzure.Storage` och `Microsoft.Azure.Storage` med nyare typer från [Azure. Storage.](/dotnet/api/azure.storage.queues)queues.

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jspå Inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet *host.jspå* filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i versionerna 2. x finns i [host.jsreferens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.jspå i functions 1. x finns i [host.jsreferens för Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Det maximala intervallet mellan Queue-avsökningar. Minimum är 00:00:00.100 (100 MS) och ökar till 00:01:00 (1 min).  I functions 2. x och högre är data typen `TimeSpan` , medan den är i millisekunder i version 1. x.|
|visibilityTimeout|00:00:00|Tidsintervall mellan återförsök vid bearbetning av ett meddelande Miss lyckas. |
|batchSize|16|Antalet köa meddelanden som funktions körningen hämtar samtidigt och processer parallellt. När antalet som bearbetas går ned till `newBatchThreshold` Kör körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold` . Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning av meddelanden som tas emot i en kö kan du ange `batchSize` 1. Den här inställningen eliminerar dock samtidighet så länge som funktions programmet bara körs på en enda virtuell dator (VM). Om Function-appen skalar ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje funktion som utlöses av kön.<br><br>Det maximala värdet `batchSize` är 32. |
|maxDequeueCount|5|Antal försök att bearbeta ett meddelande innan det flyttas till en Poison-kö.|
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt går till det här talet, hämtar körningen en annan batch.|
|messageEncoding|base64| Den här inställningen är endast tillgänglig i [tillägg version 5.0.0 och högre](#storage-extension-5x-and-higher). Den representerar kodnings formatet för meddelanden. Giltiga värden är `base64` och `none` .|

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion som ändringar i Queue Storage-data (utlösare)](./functions-bindings-storage-queue-trigger.md)
- [Skriva kö lagrings meddelanden (utgående bindning)](./functions-bindings-storage-queue-output.md)
