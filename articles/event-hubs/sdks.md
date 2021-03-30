---
title: 'Azure-Event Hubs – klient-SDK: er | Microsoft Docs'
description: 'Den här artikeln innehåller information om klient-SDK: er för Azure Event Hubs.'
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930884"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure-Event Hubs – klient-SDK: er
Den här artikeln innehåller följande information för SDK: er som stöds av Azure Event Hubs: 

- Paketets placering som du kan använda i dina program 
- GitHub-plats där du kan hitta källkod, exempel, viktigt, ändrings logg, rapporterade problem och även öka nya problem 
- Länkar till snabb starts guider 

## <a name="client-sdks"></a>Klient-SDK: er
I följande tabell beskrivs alla Azure Event Hubs runtime-klienter som är tillgängliga för närvarande. Vissa av dessa bibliotek innehåller även begränsade hanterings funktioner, men det finns också särskilda bibliotek som är reserverade för hanterings åtgärder. Kärn fokus i dessa bibliotek är att **skicka och ta emot meddelanden** från en Event Hub.

| Språk | Paket | Referens | 
| -------- | ------- | --------------- | 
| . NET standard (**senaste** och stöder både .net Core och .NET Framework) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Självstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Självstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET standard (**bakåtkompatibelt** och stöder både .net Core och .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Självstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Självstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**gammal**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Självstudie](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [Azure-Messaging – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Självstudie](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(bakåtkompatibelt)** | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Självstudie](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Självstudie](event-hubs-python-get-started-send.md)</li></ul> |
|        | [Azure-eventhub-checkpointstoreblob – AIO](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Självstudie](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Azure/Event-hubbar](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Självstudie](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/eventhubs-checkpointstore-BLOB](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Självstudie](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-Event-Hub-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-event-hubs-go)</li><li>[Självstudie](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-Hub-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub-plats](https://github.com/Azure/azure-event-hubs-c)</li><li>[Självstudie](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Hanterings-SDK: er
I följande tabell visas alla aktuella bibliotek som är tillgängliga för hantering. Inget av dessa bibliotek innehåller körnings åtgärder och är enbart för att **hantera Event Hubs entiteter**.

| Språk | Paket | Referens | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub-plats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Självstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
