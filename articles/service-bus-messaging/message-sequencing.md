---
title: Azure Service Bus sekvenser och tidsstämpel för meddelanden | Microsoft Docs
description: Den här artikeln förklarar hur du bevarar sekvenseringen och ordningen (med tidsstämplar) för Azure Service Bus meddelanden.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fdb18802e576ad114fd3f783d5efd7bb826a5f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341170"
---
# <a name="message-sequencing-and-timestamps"></a>Ordningsföljd och tidsstämplar för meddelanden

Sekvenseringen och tids stämpling är två funktioner som alltid är aktiverade på alla Service Bus entiteter och-yta via egenskaperna [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) och [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) för mottagna eller bläddrade meddelanden.

För de fall där den absoluta ordningen av meddelanden är betydande och/eller där en konsument behöver en tillförlitlig unik identifierare för meddelanden, stämplar Service Broker meddelanden med ett mellanrum utan mellanrum, vilket ökar sekvensnumret i förhållande till kön eller ämnet. För partitionerade enheter utfärdas sekvensnumret i förhållande till partitionen.

**SequenceNumber** -värdet är ett unikt 64-bitars heltal som tilldelas ett meddelande, eftersom det godkänns och lagras av Broker och fungerar som dess interna identifierare. För partitionerade entiteter återspeglar de översta 16 bitarna partitions-ID. Sekvensnummer slås över till noll när 48/64-bitars intervallet är förbrukat.

Sekvensnumret kan vara betrott som en unik identifierare eftersom det tilldelas av en central och neutral auktoritet och inte av klienter. Den representerar även den faktiska ordningen på ankomsten och är mer exakt än en tidsstämpel som ett order kriterium, eftersom tidsstämplar inte har en hög nog hög upplösning vid extrema meddelande frekvenser och kan vara föremål för (dock minimal) klock skevning i situationer där Broker-ägarskapet övergår mellan noderna.

Den absoluta ingångs ordningen, till exempel i affärs scenarier där ett begränsat antal erbjudna varor betjänas enligt först-först-Betjänad och levererar sist. konsert biljett försäljning är ett exempel.

Funktionen för tidsstämpel fungerar som en neutral och betrodd myndighet som korrekt fångar UTC-tiden då ett meddelande anländer, vilket visas i **EnqueuedTimeUtc** -egenskapen. Värdet är användbart om ett affärs scenario är beroende av deadlines, till exempel om en arbets uppgift har skickats ett visst datum före midnatt, men bearbetningen ligger långt bakom kön efter släpning.

## <a name="scheduled-messages"></a>Schemalagda meddelanden

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning, till exempel för att schemalägga ett jobb som ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt. Den här funktionen försäkrar en tillförlitlig distribuerad tidsbaserad schemaläggare.

Schemalagda meddelanden materialiseras inte i kön förrän den definierade tids kön. Före den tiden kan schemalagda meddelanden avbrytas. Annullering tar bort meddelandet.

Du kan schemalägga meddelanden antingen genom att ange egenskapen [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) när du skickar ett meddelande via den vanliga sändnings vägen eller uttryckligen med [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) -API: et. Den senare returnerar omedelbart det schemalagda meddelandets **SequenceNumber**, som du senare kan använda för att avbryta det schemalagda meddelandet vid behov. Schemalagda meddelanden och deras serie nummer kan också identifieras med hjälp av [meddelande bläddring](message-browsing.md).

**SequenceNumber** för ett schemalagt meddelande är endast giltigt när meddelandet är i det här läget. När meddelandet övergår till aktivt tillstånd läggs meddelandet till i kön som om det hade placerats i kö vid den aktuella snabben, vilket innefattar att tilldela en ny **SequenceNumber**.

Eftersom funktionen är förankrad på enskilda meddelanden och meddelanden bara kan köas en gång, kan Service Bus inte hantera återkommande scheman för meddelanden.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)