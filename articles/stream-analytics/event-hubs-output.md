---
title: Event Hubs utdata från Azure Stream Analytics
description: I den här artikeln beskrivs hur du matar ut data från Azure Stream Analytics till Azure Event Hubs.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e01a4418681e0f78864eacbf70016cfb33fa6f53
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739645"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Event Hubs utdata från Azure Stream Analytics

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) -tjänsten är en mycket skalbar händelse för att publicera prenumerationer. Den kan samla in miljon tals händelser per sekund. En användning av en Event Hub som utdata är när utdata från ett Stream Analytics jobb blir indata för ett annat strömmande jobb. Information om maximal meddelande storlek och optimering av batch-storlek finns i avsnittet [utdata för batch](#output-batch-size) -storlek.

## <a name="output-configuration"></a>Konfiguration av utdata

Följande tabell innehåller de parametrar som krävs för att konfigurera data strömmar från Event Hub som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera frågeresultatet till den här händelsehubben. |
| Namnområde för händelsehubb | En behållare för en uppsättning meddelande enheter. När du skapade en ny händelsehubben, skapade du även ett namn område för Event Hub. |
| Namn på händelsehubb | Namnet på din Event Hub-utdata. |
| Princip namn för Event Hub | Principen för delad åtkomst, som du kan skapa på fliken **Konfigurera** på händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomst nycklar. |
| Princip nyckel för Event Hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Event Hub-namnområdet. |
| Nyckel kolumn för partition | Valfritt. En kolumn som innehåller partitionsnyckel för Event Hub-utdata. |
| Händelseserialiseringsformat | Serialiserings formatet för utdata. JSON, CSV och Avro stöds. |
| Kodning | För CSV och JSON är UTF-8 det enda kodnings format som stöds just nu. |
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blank steg, TABB och lodrätt fält. |
| Format | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. Om du väljer **rad avgränsad** läses ett objekt i taget av JSON. Själva hela innehållet skulle inte vara en giltig JSON. **Matris** anger att utdata är formaterad som en matris med JSON-objekt.  |
| Egenskaps kolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Partitionering

Partitionering varierar beroende på partitionens justering. När partitionsnyckel för Event Hub-utdata är jämnt justerad med föregående steg i frågan, är antalet skrivare samma som antalet partitioner i Event Hub-utdata. Varje skrivare använder [klassen EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true) för att skicka händelser till den angivna partitionen. När partitionsnyckel för utdata i Event Hub inte är justerad till föregående steg i frågan, är antalet skrivare samma som antalet partitioner i det föregående steget. Varje skrivare använder [klassen SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true) i **EventHubClient** för att skicka händelser till alla utdata-partitioner. 

## <a name="output-batch-size"></a>Batchstorlek för utdata

Den maximala meddelande storleken är 256 KB eller 1 MB per meddelande. Mer information finns i [Event Hubs gränser](../event-hubs/event-hubs-quotas.md). När indata/utdata-partitionering inte är justerad, paketeras varje händelse individuellt i `EventData` och skickas i en batch med upp till maximal meddelande storlek. Detta inträffar även om [anpassade metadata-egenskaper](#custom-metadata-properties-for-output) används. När indata/utdata-partitionering är justerad, packas flera händelser i en enda `EventData` instans, upp till den maximala meddelande storleken och skickas.

## <a name="custom-metadata-properties-for-output"></a>Anpassade egenskaper för metadata för utdata

Du kan koppla frågeegenskaper som användar egenskaper till dina utgående meddelanden. De här kolumnerna hamnar inte i nytto lasten. Egenskaperna finns i form av en ord lista i utmatnings meddelandet. *Key* är kolumnens namn och *värde* är kolumnens värde i ord listan för egenskaper. Alla Stream Analytics data typer stöds förutom post och matris.

I följande exempel `DeviceId` läggs fälten och till i `DeviceStatus` metadata.

1. Använd följande fråga:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Konfigurera `DeviceId,DeviceStatus` som egenskaps kolumner i utdata.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Egenskaps kolumner":::

Följande bild visar förväntade egenskaper för utmatnings meddelande som inspekterats i EventHub med hjälp av [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Anpassade egenskaper för händelse":::

## <a name="next-steps"></a>Nästa steg

* [Använda hanterade identiteter för att komma åt Händelsehubben från ett Azure Stream Analytics jobb (förhands granskning)](event-hubs-managed-identity.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
