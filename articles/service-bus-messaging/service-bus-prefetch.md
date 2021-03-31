---
title: Azure Service Bus för hämtnings meddelanden | Microsoft Docs
description: Förbättra prestanda genom att för hämtning Azure Service Bus meddelanden. Meddelanden är lätt att komma åt för lokal hämtning innan program begärs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341049"
---
# <a name="prefetch-azure-service-bus-messages"></a>För hämtning Azure Service Bus meddelanden

När för *hämtning* har Aktiver ATS i någon av de officiella Service Bus-klienterna, erhåller mottagaren tyst fler meddelanden, upp till [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) -gränsen, utöver det program som ursprungligen begärdes.

Ett enda första [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) -eller [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) -anrop hämtar därför ett meddelande för omedelbar konsumtion som returneras så snart som möjligt. Klienten hämtar sedan ytterligare meddelanden i bakgrunden för att fylla i för hämtnings bufferten.

## <a name="enable-prefetch"></a>Aktivera för hämtning

Med .NET aktiverar du för hämtnings funktionen genom att ange egenskapen [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) för en **MessageReceiver**, **QueueClient** eller **SubscriptionClient** till ett tal som är större än noll. Om du anger värdet noll inaktive ras för hämtning.

Du kan enkelt lägga till den här inställningen till mottagnings sidan av inställningarna för [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) eller [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) -exempel för att se effekterna i dessa sammanhang.

Även om meddelanden är tillgängliga i prefetch-bufferten uppfylls **alla efterföljande** / **ReceiveAsync** -anrop direkt från bufferten och bufferten fylls i bakgrunden när utrymmet blir tillgängligt. Om det inte finns några meddelanden tillgängliga för leverans tömmer Receive-åtgärden bufferten och väntar sedan eller blockerar dem som förväntat.

För hämtning fungerar också på samma sätt med API: erna [motringningen OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) och [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) .

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Om det är snabbare, varför förhämtar du inte standard alternativet?

För hämtning påskyndar meddelande flödet genom att ha ett meddelande som är lättillgängligt för lokal hämtning när och innan programmet begär ett. Den här data flödes vinsten är resultatet av en kompromiss som program författaren måste göra uttryckligen:

Med [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) Receive-läge är alla meddelanden som förvärvas i för hämtnings bufferten inte längre tillgängliga i kön och endast finns i den förhämtade bufferten för InMemory tills de tas emot i programmet via **Receive**- / **ReceiveAsync** eller **motringningen OnMessage** / **OnMessageAsync** -API: er. Om programmet avslutas innan meddelandena tas emot i programmet, försvinner dessa meddelanden oåterkalleligt.

I [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) Receive-läge, hämtas meddelanden som hämtats till den förhämtade bufferten i bufferten i låst tillstånd och tids gränsen för låset för lås skalning. Om för hämtnings bufferten är stor, och bearbetningen tar så lång att meddelandets lås upphör att gälla när den finns i prefetch-bufferten eller även om programmet bearbetar meddelandet, kan det finnas några förvirrande händelser som programmet kan hantera.

Programmet kan hämta ett meddelande med ett utgånget eller överhäng ande lås. I så fall kan programmet bearbeta meddelandet, men det går inte att slutföra det på grund av ett låsnings förfallo datum. Programmet kan kontrol lera egenskapen [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (som är beroende av klock skillnaden mellan Broker och den lokala datorns klocka). Om meddelande låset har upphört att gälla måste programmet Ignorera meddelandet. inget API-anrop på eller med meddelandet bör göras. Om meddelandet inte upphör att gälla men upphör att gälla, kan låset förnyas och utökas av en annan standard lås period genom att anropa [meddelandet. RenewLock ()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Om låset förfaller tyst i för hämtnings bufferten behandlas meddelandet som överlappande och görs tillgängligt för hämtning från kön. Det kan leda till att den hämtas till för hämtnings bufferten. placeras i slutet. Om för hämtnings bufferten vanligt vis inte kan bearbetas genom att meddelandet upphör att gälla, så gör det att meddelanden hämtas upprepade gånger, men inte faktiskt levererat i ett användbart (giltigt låst) tillstånd, och flyttas till sist till kön för obeställbara meddelanden när maximalt antal leveranser har överskridits.

Om du behöver en hög Tillförlitlighets nivå för meddelande bearbetning, och bearbetningen tar betydande arbete och tid, rekommenderar vi att du använder funktionen för för hämtning försiktigt eller inte alls.

Om du behöver hög genom strömning och meddelande bearbetning ofta är billigt ger för hämtning avsevärda data flödes fördelar.

Det maximala antalet för hämtningar och den lås varaktighet som kon figurer ATS i kön eller prenumerationen måste bal anse ras så att tids gränsen för låset på minst överskrider den ackumulerade förväntade meddelande bearbetnings tiden för den maximala storleken på för hämtnings bara bufferten, plus ett meddelande. På samma gång bör tids gränsen för låset inte vara så lång att meddelanden får överskrida sina högsta [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) när de råkar släppas av misstag, vilket innebär att deras lås upphör att gälla innan den återlevereras.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
