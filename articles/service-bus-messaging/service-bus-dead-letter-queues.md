---
title: Service Bus köer för | Microsoft Docs
description: Beskriver köer med död bokstav i Azure Service Bus. Service Bus köer och ämnesprenumerationer tillhandahåller en sekundär underkö, som kallas kö för oställbara meddelanden.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6293a3a9a760ece137644578d8ee7dccebc63d95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812380"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus köer med dead letter

Azure Service Bus köer och ämnesprenumerationer tillhandahåller en sekundär underkö som kallas för en kö *med oställbara meddelanden* (DLQ). Kön för oställbara meddelanden behöver inte skapas explicit och kan inte tas bort eller hanteras oberoende av huvudentiteten.

I den här artikeln beskrivs köer för dead-letter i Service Bus. En stor del av diskussionen illustreras av exemplet [på köer](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) för dead letter på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kön för dead-letter

Syftet med kön för dead-letter är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och inspekteras. Ett program kan, med hjälp av en operatör, korrigera problem och skicka meddelandet igen, logga det faktum att det uppstod ett fel och vidta lämpliga åtgärder. 

Ur ett API- och protokollperspektiv liknar DLQ i stort sett alla andra köer, förutom att meddelanden endast kan skickas via åtgärden dead-letter för den överordnade entiteten. Dessutom observeras inte time-to-live och du kan inte skicka ett meddelande i dead-letter-meddelande från en DLQ. Kön för dead-letter har fullständigt stöd för peek-lock-leverans och transaktionella åtgärder.

Det finns ingen automatisk rensning av DLQ. Meddelanden finns kvar i DLQ tills du uttryckligen hämtar dem från DLQ och slutför meddelandet med obokstav.


## <a name="dlq-message-count"></a>ANTAL DLQ-meddelanden
Det går inte att hämta antalet meddelanden i kön för dead-letter på ämnesnivå. Det beror på att meddelanden inte finns på ämnesnivå om inte Service Bus ett internt fel. När en avsändare i stället skickar ett meddelande till ett ämne vidarebefordras meddelandet till prenumerationer för ämnet inom millisekunder och finns därför inte längre på ämnesnivå. Därför kan du se meddelanden i den DLQ som är associerad med prenumerationen för ämnet. I följande exempel visar **Service Bus Explorer** att det för närvarande finns 62 meddelanden i DLQ för prenumerationen "test1". 

![ANTAL DLQ-meddelanden](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Du kan också hämta antalet DLQ-meddelanden med hjälp av Azure CLI-kommandot: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ
Det finns flera aktiviteter i Service Bus som gör att meddelanden skickas till DLQ från själva meddelandemotorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. Följande två egenskaper (orsak till dead letter och beskrivning av utan bokstav) läggs till i meddelanden utan bokstav. Program kan definiera sina egna koder för orsaksegenskapen med dead letter, men systemet anger följande värden.

| Orsak till att ett inskrivet brev har för avsikt | Felbeskrivning utan bokstav |
| --- | --- |
|HeaderSizeExceeded |Storlekskvoten för dataströmmen har överskridits. |
|TTLExpiredException |Meddelandet har gått ut och blev obeställbart. Mer information [finns i avsnittet Time to live.](#time-to-live) |
|Sessions-ID är null. |Sessionsaktiverad entitet tillåter inte ett meddelande vars sessions-ID är null. |
|MaxTransferHopCountExceeded | Det maximala antalet tillåtna hopp vid vidarebefordran mellan köer. Värdet är inställt på 4. |
| MaxDeliveryCountExceededExceptionMessage | Det gick inte att förbruka meddelandet efter maximalt antal leveransförsök. Mer information [finns i avsnittet Maximalt](#maximum-delivery-count) antal leveranser. |

## <a name="maximum-delivery-count"></a>Maximalt antal leveranser
Det finns en gräns för antalet försök att leverera meddelanden för Service Bus köer och prenumerationer. Standardvärdet är 10. När ett meddelande har levererats under ett peek-lock, men antingen uttryckligen har övergivits eller om låset har upphört att gälla, ökas leveransantalet för meddelandet. När leveransantalet överskrider gränsen flyttas meddelandet till DLQ. Orsaken till den obefintliga bokstaven för meddelandet i DLQ är inställd på: MaxDeliveryCountExceeded. Det här beteendet kan inte inaktiveras, men du kan ange det maximala antalet leveranser till ett stort antal.

## <a name="time-to-live"></a>Time to live
När du aktiverar dead-lettering för köer eller prenumerationer flyttas alla utgående meddelanden till DLQ. Orsakskoden för obefintliga bokstäver är inställd på: TTLExpiredException.

Utgångna meddelanden rensas bara och flyttas till DLQ när det finns minst en aktiv mottagare som tar emot från huvudkön eller prenumerationen. De uppskjutna meddelandena rensas inte heller och flyttas inte till kön för dead-letter efter att de har upphört att gälla. Det här beteendet är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerationsregler
Om du aktiverar dead lettering vid undantag i filterutvärderingen, fångas eventuella fel som inträffar när en prenumerations SQL-filterregel körs i DLQ tillsammans med det felaktiga meddelandet. Använd inte det här alternativet i en produktionsmiljö där inte alla meddelandetyper har prenumeranter.

## <a name="application-level-dead-lettering"></a>Dead lettering på programnivå
Förutom de systemtilldeade funktionerna för dead lettering kan program använda DLQ för att uttryckligen avvisa oacceptabla meddelanden. De kan innehålla meddelanden som inte kan bearbetas korrekt på grund av någon typ av systemproblem, meddelanden som innehåller felaktiga nyttolaster eller meddelanden som misslyckas med autentisering när vissa säkerhetsscheman på meddelandenivå används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering i ForwardTo- eller SendVia-scenarier
Meddelanden skickas till kön för överföring av dead-letter under följande villkor:

- Ett meddelande passerar genom fler än fyra köer eller ämnen som [är sammankedjade.](service-bus-auto-forwarding.md)
- Målkön eller ämnet är inaktiverat eller borttagna.
- Målkön eller ämnet överskrider den maximala entitetsstorleken.

## <a name="path-to-the-dead-letter-queue"></a>Sökväg till kön för dead-letter
Du kan komma åt kön för dead-letter med hjälp av följande syntax:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Nästa steg
Se [Aktivera dead lettering för en kö eller prenumeration om du](enable-dead-letter.md) vill veta mer om olika sätt att konfigurera inställningen för förfallotid för meddelanden med hjälp av dead **lettering.**
