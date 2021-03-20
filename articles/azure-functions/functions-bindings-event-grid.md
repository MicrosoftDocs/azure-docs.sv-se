---
title: Azure Event Grid bindningar för Azure Functions
description: Förstå hur du hanterar Event Grid händelser i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104554"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid bindningar för Azure Functions

Den här referensen beskriver hur du hanterar [Event Grid](../event-grid/overview.md) händelser i Azure Functions. Mer information om hur du hanterar Event Grid meddelanden i en HTTP-slutpunkt finns i [ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md).

Event Grid är en Azure-tjänst som skickar HTTP-förfrågningar för att meddela dig om händelser som inträffar i *utgivare*. En utgivare är den tjänst eller resurs som har sitt ursprung i händelsen. Till exempel är ett Azure Blob Storage-konto en utgivare och [en BLOB-överföring eller borttagning är en händelse](../storage/blobs/storage-blob-event-overview.md). Vissa [Azure-tjänster har ett inbyggt stöd för att publicera händelser till Event Grid](../event-grid/overview.md#event-sources).

Händelse *hanterare* tar emot och bearbetar händelser. Azure Functions är en av flera [Azure-tjänster som har inbyggt stöd för att hantera Event Grid händelser](../event-grid/overview.md#event-handlers). I den här referensen får du lära dig att använda en Event Grid-utlösare för att anropa en funktion när en händelse tas emot från Event Grid, och för att använda utgående bindning för att skicka händelser till ett [Event Grid anpassat ämne](../event-grid/post-to-custom-topic.md).

Om du vill kan du använda en HTTP-utlösare för att hantera Event Grid händelser. Se [ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md). För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i [CloudEvents-schemat](../event-grid/cloudevents-schema.md#azure-functions). Använd i stället en HTTP-utlösare.

| Action | Typ |
|---------|---------|
| Köra en funktion när en Event Grid händelse skickas | [Utlösare](./functions-bindings-event-grid-trigger.md) |
| Skickar en Event Grid-händelse |[Utgående bindning](./functions-bindings-event-grid-output.md) |

Koden i den här referensen är standardvärdet för .NET Core-syntax, som används i functions version 2. x och högre. Information om syntaxen för 1. x finns i [mallarna för 1. x-funktioner](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 2. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

## <a name="next-steps"></a>Nästa steg
* [Köra en funktion när en Event Grid händelse skickas](./functions-bindings-event-grid-trigger.md)
* [Skicka en Event Grid-händelse](./functions-bindings-event-grid-trigger.md)