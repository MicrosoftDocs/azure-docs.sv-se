---
title: Service Bus köer för obeställbara meddelanden | Microsoft Docs
description: Beskriver köer för obeställbara meddelanden i Azure Service Bus. Service Bus köer och ämnes prenumerationer tillhandahåller en sekundär underkö som kallas kö för obeställbara meddelanden.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630106"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus köer för obeställbara meddelanden

Azure Service Bus köer och ämnes prenumerationer tillhandahåller en sekundär underkö som kallas för en *kö för obeställbara meddelanden* (DLQ). Kön för obeställbara meddelanden behöver inte skapas explicit och kan inte tas bort eller hanteras på annat sätt oberoende av huvud enheten.

I den här artikeln beskrivs köer för obeställbara meddelanden i Service Bus. En stor del av diskussionen illustreras av [exemplet för köer för obeställbara meddelanden](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kön för obeställbara meddelanden

Syftet med kön för obeställbara meddelanden är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och granskas. Ett program kan, med hjälp av en operatör, korrigera problem och skicka meddelandet på nytt, logga det faktum att det uppstod ett fel och vidta lämpliga åtgärder. 

Från ett API-och protokoll perspektiv är DLQ mest likt andra köer, förutom att meddelanden bara kan skickas via den överordnade entitetens obeställbara åtgärder. Dessutom observeras inte Time-to-Live och du kan inte obeställbara ett meddelande från en DLQ. Kön för obeställbara meddelanden har fullt stöd för gransknings-och transaktions åtgärder.

Det finns ingen automatisk rensning av DLQ. Meddelanden finns kvar i DLQ tills du uttryckligen hämtar dem från DLQ och anropar [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) i meddelandet om obeställbara meddelanden.

## <a name="dlq-message-count"></a>Antal DLQ-meddelanden
Det går inte att hämta antal meddelanden i kön för obeställbara meddelanden på ämnes nivå. Det beror på att meddelanden inte finns på ämnes nivån om Service Bus genererar ett internt fel. När en avsändare skickar ett meddelande till ett ämne, vidarebefordras meddelandet till prenumerationer för ämnet inom millisekunder och därför finns det inte längre på ämnes nivå. Så du kan se meddelanden i DLQ som är kopplade till prenumerationen för ämnet. I följande exempel visar **Service Bus Explorer** att det finns 62 meddelanden för närvarande i DLQ för prenumerationen "TEST1". 

![Antal DLQ-meddelanden](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Du kan också få antalet DLQ-meddelanden med hjälp av Azure CLI-kommandot: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ

Det finns flera aktiviteter i Service Bus som gör att meddelanden skickas till DLQ inifrån själva meddelande motorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. 

När meddelandet flyttas av utjämningen läggs två egenskaper till i meddelandet eftersom Broker anropar den interna versionen av [obeställbara meddelanden kön](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) -metoden i meddelandet: `DeadLetterReason` och `DeadLetterErrorDescription` .

Program kan definiera egna koder för `DeadLetterReason` egenskapen, men systemet anger följande värden.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |Storlekskvoten för dataströmmen har överskridits. |
|TTLExpiredException |Meddelandet har gått ut och blev obeställbart. Mer information finns i avsnittet om att [överskrida TimeToLive](#exceeding-timetolive) . |
|Sessions-ID är null. |Sessionsaktiverad entitet tillåter inte ett meddelande vars sessions-ID är null. |
|MaxTransferHopCountExceeded | Maximalt antal tillåtna hopp vid vidarebefordran mellan köer. Värdet är inställt på 4. |
| MaxDeliveryCountExceededExceptionMessage | Det gick inte att utnyttja meddelandet efter maximalt antal leverans försök. Mer information finns i avsnittet om att [överskrida MaxDeliveryCount](#exceeding-maxdeliverycount) . |

## <a name="exceeding-maxdeliverycount"></a>Överskrider MaxDeliveryCount

Köer och prenumerationer har var och en har egenskapen [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) respektive [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) . Standardvärdet är 10. När ett meddelande har levererats under ett lås ([PeekLock ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), men har antingen uttryckligen övergivits eller om låset har upphört att gälla, ökar meddelandet [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . När [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) överskrider [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)flyttas meddelandet till DLQ och anger `MaxDeliveryCountExceeded` orsaks koden.

Det går inte att inaktivera det här beteendet, men du kan ange [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) till ett stort antal.

## <a name="exceeding-timetolive"></a>Överskrider TimeToLive

När egenskapen [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) har angetts till **True** (Standardvärdet är **false**) flyttas alla utgående meddelanden till DLQ och anger  `TTLExpiredException` orsaks koden.

Förfallna meddelanden rensas och flyttas bara till DLQ när det finns minst en aktiv mottagare från huvud kön eller prenumerationen, och [uppskjutna meddelanden](./message-deferral.md) tas inte heller bort och flyttas till kön för obeställbara meddelanden när de har gått ut. Dessa beteenden är avsiktliga.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerations regler

När egenskapen [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) har Aktiver ATS för en prenumeration, registreras eventuella fel som inträffar när en prenumerations SQL filter-regel körs i DLQ tillsammans med det felaktiga meddelandet. Använd inte det här alternativet i en produktions miljö där inte alla meddelande typer har prenumeranter.

## <a name="application-level-dead-lettering"></a>Obeställbara meddelanden på program nivå

Förutom de funktioner för obeställbara meddelanden som har angetts kan program använda DLQ för att uttryckligen avvisa meddelanden som inte accepteras. De kan innehålla meddelanden som inte kan bearbetas korrekt på grund av eventuella sorters system problem, meddelanden som innehåller felaktiga nytto laster eller meddelanden som inte kan autentiseras när vissa säkerhets scheman på meddelande nivå används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obeställbara meddelanden i scenarier med ForwardTo eller SendVia

Meddelanden skickas till kön för överföring av obeställbara meddelanden under följande omständigheter:

- Ett meddelande passerar mer än fyra köer eller avsnitt [som är](service-bus-auto-forwarding.md)sammankopplade.
- Målkön eller avsnittet har inaktiverats eller tagits bort.
- Målkön eller avsnittet överskrider den maximala enhets storleken.

Om du vill hämta dessa meddelanden med obeställbara meddelanden kan du skapa en mottagare med hjälp av [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) -verktygs metoden.

## <a name="example"></a>Exempel

Följande kodfragment skapar en meddelande mottagare. I mottagnings-loopen för huvud kön hämtar koden meddelandet med [receive (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), som uppmanar koordinatorn att omedelbart returnera eventuella meddelanden som är tillgängliga eller returnera utan resultat. Om koden tar emot ett meddelande överges det omedelbart, vilket ökar  `DeliveryCount` . När systemet flyttar meddelandet till DLQ är huvud kön Tom och loopen avslutas, så som [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) returnerar **Null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Sökväg till kön för obeställbara meddelanden
Du kan komma åt kön för obeställbara meddelanden med hjälp av följande syntax:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Om du använder .NET SDK kan du hämta sökvägen till kön för obeställbara meddelanden med hjälp av metoden SubscriptionClient. FormatDeadLetterPath (). Den här metoden tar ämnets namn/prenumerations namn och suffix med **/$DeadLetterQueue**.


## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om Service Bus köer:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Azure-köer och Service Bus köer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

