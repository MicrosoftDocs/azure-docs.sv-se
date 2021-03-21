---
title: Azure Service Bus-meddelande bläddring
description: Bläddra och granska Service Bus meddelanden gör det möjligt för en Azure Service Bus klient att räkna upp alla meddelanden i en kö eller prenumeration.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553649"
---
# <a name="message-browsing"></a>Bläddra i meddelanden

Genom att söka efter meddelanden eller granska, kan en Service Bus-klient räkna upp alla meddelanden i en kö eller en prenumeration för diagnostik-och fel söknings syfte.

Gransknings åtgärden i en kö returnerar alla meddelanden i kön, inte bara de som är tillgängliga för omedelbar förvärv med `Receive()` eller- `OnMessage()` slingan. `State`Egenskapen för varje meddelande anger om meddelandet är aktivt (tillgängligt för mottagning), [uppskjutet](message-deferral.md)eller [schemalagt](message-sequencing.md). Gransknings åtgärden för en prenumeration returnerar alla meddelanden utom schemalagda meddelanden i prenumerations meddelande loggen. 

Förbrukade och förfallna meddelanden rensas av en asynkron "skräp insamling"-körning. Det här steget kanske inte alltid inträffar omedelbart efter att meddelanden har gått ut. Det är därför, `Peek` kan returnera meddelanden som redan har upphört att gälla. De här meddelandena tas bort eller tas bort från kön när en mottagnings åtgärd anropas i kön eller prenumerationen nästa gång. Tänk på detta när du försöker återställa uppskjutna meddelanden från kön. Ett utgånget meddelande är inte längre tillgängligt för vanlig hämtning på något annat sätt, även när det returneras av Peek. Att returnera dessa meddelanden är avsiktligt som Peek är ett diagnos verktyg som återspeglar loggens aktuella status.

Peek returnerar även meddelanden som var låsta och som för närvarande bearbetas av andra mottagare. Men eftersom Peek returnerar en frånkopplad ögonblicks bild kan inte lås statusen för ett meddelande observeras på granskade meddelanden. Egenskaperna [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) och [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) genererar en [InvalidOperationException](/dotnet/api/system.invalidoperationexception) när programmet försöker läsa dem.

## <a name="peek-apis"></a>Granska API: er

Metoderna [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) och [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) finns i .net-och Java-klient bibliotek och på mottagar objekt: **MessageReceiver**, **MessageSession**. Peek fungerar på köer, prenumerationer och deras respektive köer för obeställbara meddelanden.

Vid anrop upprepas **granskningen** av alla meddelanden i kön eller i prenumerations loggen i ordning från det lägsta tillgängliga sekvensnumret till högsta. Det är ordningen i vilken meddelanden har placerats i kö, inte i den ordning som meddelanden kan komma att hämtas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) hämtar flera meddelanden och returnerar dem som en uppräkning. Om inga meddelanden är tillgängliga, är uppräknings objektet tomt, inte null.

Du kan också använda en överlagring av metoden med en [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som ska startas och sedan anropa den parameter lös metoden överbelastning för att räkna upp ytterligare. **PeekBatch** -funktioner likvärdigt, men hämtar en uppsättning meddelanden samtidigt.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
