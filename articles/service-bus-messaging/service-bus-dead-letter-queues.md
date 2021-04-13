---
title: Service Bus köer för obeställbara meddelanden | Microsoft Docs
description: Beskriver köer för obeställbara meddelanden i Azure Service Bus. Service Bus köer och ämnes prenumerationer tillhandahåller en sekundär underkö som kallas kö för obeställbara meddelanden.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6459c8edd03427357810c1ad30161e87c18e059c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304332"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus köer för obeställbara meddelanden

Azure Service Bus köer och ämnes prenumerationer tillhandahåller en sekundär underkö som kallas för en *kö för obeställbara meddelanden* (DLQ). Kön för obeställbara meddelanden behöver inte skapas explicit och kan inte tas bort eller hanteras oberoende av huvud enheten.

I den här artikeln beskrivs köer för obeställbara meddelanden i Service Bus. En stor del av diskussionen illustreras av [exemplet för köer för obeställbara meddelanden](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kön för obeställbara meddelanden

Syftet med kön för obeställbara meddelanden är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och granskas. Ett program kan, med hjälp av en operatör, korrigera problem och skicka meddelandet på nytt, logga det faktum att det uppstod ett fel och vidta lämpliga åtgärder. 

Från ett API-och protokoll perspektiv är DLQ mest likt andra köer, förutom att meddelanden bara kan skickas via den överordnade entitetens obeställbara åtgärder. Dessutom observeras inte Time-to-Live och du kan inte obeställbara ett meddelande från en DLQ. Kön för obeställbara meddelanden har fullt stöd för gransknings-och transaktions åtgärder.

Det finns ingen automatisk rensning av DLQ. Meddelanden finns kvar i DLQ tills du uttryckligen hämtar dem från DLQ och fyller i meddelandet om obeställbara meddelanden.


## <a name="dlq-message-count"></a>Antal DLQ-meddelanden
Det går inte att hämta antal meddelanden i kön för obeställbara meddelanden på ämnes nivå. Det beror på att meddelanden inte finns på ämnes nivån om Service Bus genererar ett internt fel. När en avsändare skickar ett meddelande till ett ämne, vidarebefordras meddelandet till prenumerationer för ämnet inom millisekunder och därför finns det inte längre på ämnes nivå. Så du kan se meddelanden i DLQ som är kopplade till prenumerationen för ämnet. I följande exempel visar **Service Bus Explorer** att det finns 62 meddelanden för närvarande i DLQ för prenumerationen "TEST1". 

![Antal DLQ-meddelanden](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Du kan också få antalet DLQ-meddelanden med hjälp av Azure CLI-kommandot: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ
Det finns flera aktiviteter i Service Bus som gör att meddelanden skickas till DLQ inifrån själva meddelande motorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. Följande två egenskaper (följd av obeställbara meddelanden och obeställbara meddelanden) läggs till i meddelanden med obeställbara meddelanden. Program kan definiera egna koder för egenskapen för obeställbara orsaker, men systemet anger följande värden.

| Orsak till obeställbara meddelanden | Beskrivning av fel i obeställbara meddelanden |
| --- | --- |
|HeaderSizeExceeded |Storlekskvoten för dataströmmen har överskridits. |
|TTLExpiredException |Meddelandet har gått ut och blev obeställbart. Mer information finns i avsnittet [Time to Live](#time-to-live) . |
|Sessions-ID är null. |Sessionsaktiverad entitet tillåter inte ett meddelande vars sessions-ID är null. |
|MaxTransferHopCountExceeded | Maximalt antal tillåtna hopp vid vidarebefordran mellan köer. Värdet är inställt på 4. |
| MaxDeliveryCountExceededExceptionMessage | Det gick inte att utnyttja meddelandet efter maximalt antal leverans försök. Mer information finns i avsnittet om [maximalt antal leveranser](#maximum-delivery-count) . |

## <a name="maximum-delivery-count"></a>Maximalt antal leveranser
Det finns en gräns för antalet försök att leverera meddelanden för Service Bus köer och prenumerationer. Standardvärdet är 10. När ett meddelande har levererats under en Peek-lock, men har antingen uttryckligen övergivits eller låset har upphört att gälla, ökar leverans antalet i meddelandet. När leverans antalet överskrider gränsen flyttas meddelandet till DLQ. Orsaken till meddelandet i DLQ har angetts till: MaxDeliveryCountExceeded. Det går inte att inaktivera det här beteendet, men du kan ange maximalt antal leveranser till ett stort antal.

## <a name="time-to-live"></a>Time to live
När du aktiverar obeställbara meddelanden i köer eller prenumerationer flyttas alla utgående meddelanden till DLQ. Orsaks koden för obeställbara meddelanden har angetts till: TTLExpiredException.

Förfallna meddelanden rensas bara och flyttas till DLQ när det finns minst en aktiv mottagare från huvud kön eller prenumerationen. Uppskjutna meddelanden tas inte heller bort och flyttas inte till kön för obeställbara meddelanden när de har gått ut. Det här beteendet är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerations regler
Om du aktiverar filtrering av undantag för obeställbara meddelanden, registreras eventuella fel som inträffar när en prenumerations SQL filter-regel körs i DLQ tillsammans med det felaktiga meddelandet. Använd inte det här alternativet i en produktions miljö där inte alla meddelande typer har prenumeranter.

## <a name="application-level-dead-lettering"></a>Obeställbara meddelanden på program nivå
Förutom de funktioner för obeställbara meddelanden som har angetts kan program använda DLQ för att uttryckligen avvisa meddelanden som inte accepteras. De kan innehålla meddelanden som inte kan bearbetas korrekt på grund av eventuella sorters system problem, meddelanden som innehåller felaktiga nytto laster eller meddelanden som inte kan autentiseras när vissa säkerhets scheman på meddelande nivå används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obeställbara meddelanden i scenarier med ForwardTo eller SendVia
Meddelanden skickas till kön för överföring av obeställbara meddelanden under följande omständigheter:

- Ett meddelande passerar mer än fyra köer eller avsnitt [som är](service-bus-auto-forwarding.md)sammankopplade.
- Målkön eller avsnittet har inaktiverats eller tagits bort.
- Målkön eller avsnittet överskrider den maximala enhets storleken.

## <a name="path-to-the-dead-letter-queue"></a>Sökväg till kön för obeställbara meddelanden
Du kan komma åt kön för obeställbara meddelanden med hjälp av följande syntax:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus köer finns i följande artiklar:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Azure-köer och Service Bus köer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

