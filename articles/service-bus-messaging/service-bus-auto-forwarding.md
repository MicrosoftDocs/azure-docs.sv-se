---
title: Vidarebefordra Azure Service Bus meddelande enheter automatiskt
description: I den här artikeln beskrivs hur du kedjar en Azure Service Bus kö eller en prenumeration till en annan kö eller ett ämne.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678996"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Kedja Service Bus entiteter med vidarebefordran

Med funktionen Service Bus *autoforwarding* kan du kedja en kö eller en prenumeration till en annan kö eller ett ämne som ingår i samma namnrymd. När automatisk vidarebefordran har Aktiver ATS tar Service Bus automatiskt bort meddelanden som placeras i den första kön eller prenumerationen (källa) och placerar dem i den andra kön eller ämnet (målet). Det är fortfarande möjligt att skicka ett meddelande till målentiteten direkt.

> [!NOTE]
> Den grundläggande Service Buss nivån stöder inte funktionen för autovidarebefordran. Standard-och Premium-nivån stöder funktionen. För skillnader mellan dessa nivåer, se [Service Bus prissättning](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-autoforwarding"></a>Använda vidarebefordran

Du kan aktivera vidarebefordran genom att ange egenskaperna [QueueDescription. ForwardTo][QueueDescription.ForwardTo] eller [SubscriptionDescription. ForwardTo][SubscriptionDescription.ForwardTo] i [QueueDescription][QueueDescription] -eller [SubscriptionDescription][SubscriptionDescription] -objekten för källan, som i följande exempel:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Målentiteten måste finnas när källentiteten skapas. Om målentiteten inte finns returnerar Service Bus ett undantag när du uppmanas att skapa käll enheten.

Du kan använda vidarebefordran för att skala ut ett enskilt ämne. Service Bus begränsar [antalet prenumerationer för ett angivet ämne](service-bus-quotas.md) till 2 000. Du kan hantera ytterligare prenumerationer genom att skapa avsnitt på andra nivån. Även om du inte är kopplad till Service Bus begränsningen för antalet prenumerationer kan du förbättra det totala data flödet i ditt ämne genom att lägga till en andra nivå med avsnitt.

![Diagram över ett scenario för autovidarebefordran som visar ett meddelande som bearbetas via ett order-avsnitt som kan gå till någon av tre artiklar på andra nivån.][0]

Du kan också använda vidarebefordran för att koppla ifrån meddelande avsändare från mottagare. Överväg till exempel ett ERP-system som består av tre moduler: order bearbetning, inventerings hantering och kund Relations hantering. Var och en av dessa moduler genererar meddelanden som är i kö i ett motsvarande ämne. Alice och Robert är säljare som är intresserade av alla meddelanden som är relaterade till sina kunder. För att ta emot dessa meddelanden, Alice och Bob var och en skapa en personlig kö och en prenumeration på var och en av ERP-ämnen som automatiskt vidarebefordrar alla meddelanden till kön.

![Diagram över ett scenario för autovidarebefordran som visar tre bearbetnings moduler som skickar meddelanden via tre motsvarande ämnen i två separata köer.][1]

Om Alice går på semester, hennes personliga kö i stället för ERP-ämnet, fylls. I det här scenariot, eftersom en försäljnings representant inte har tagit emot några meddelanden, når inget av ERP-ämnena kvoten.

> [!NOTE]
> När automatisk vidarebefordran konfigureras, ställs värdet för AutoDeleteOnIdle på **både källan och målet** automatiskt till det maximala värdet för data typen.
> 
>   - På käll sidan fungerar vidarebefordran som en mottagnings åtgärd. Källan som har installations programmet för autoforward är aldrig i själva verket "inaktiv".
>   - På mål sidan görs detta för att säkerställa att det alltid finns ett mål för att vidarebefordra meddelandet till.

## <a name="autoforwarding-considerations"></a>Att tänka på vid vidarebefordran

Om målentiteten samlar in för många meddelanden och överskrider kvoten, eller om målentiteten är inaktive rad, lägger källentiteten till meddelandena i [kön för obeställbara](service-bus-dead-letter-queues.md) meddelanden tills det finns utrymme i målet (eller så har entiteten återaktiverats). Dessa meddelanden fortsätter att finnas i kön för obeställbara meddelanden, så du måste uttryckligen ta emot och bearbeta dem från kön för obeställbara meddelanden.

När du sammanfogar enskilda ämnen för att få ett sammansatt ämne med många prenumerationer, rekommenderar vi att du har ett stort antal prenumerationer på den första nivån och många prenumerationer på de andra nivå avsnitten. Till exempel ett ämne på första nivån med 20 prenumerationer, var och en av dem som är länkad till ett avsnitt på en andra nivå med 200-prenumerationer, gör det möjligt att använda mer data flöde än ett ämne med 200-prenumerationer, varje länkat till ett ämne med 20 prenumerationer på andra nivån.

Service Bus räkningar en åtgärd för varje vidarebefordrat meddelande. Om du till exempel skickar ett meddelande till ett ämne med 20 prenumerationer, var och en av dem konfigurerade att vidarebefordra meddelanden till en annan kö eller ämne, faktureras som 21 åtgärder om alla prenumerationer på första nivån får en kopia av meddelandet.

Om du vill skapa en prenumeration som är länkad till en annan kö eller ett ämne måste den som skapat prenumerationen ha behörighet att **Hantera** både käll-och målentiteten. Att skicka meddelanden till käll avsnittet kräver bara behörigheterna **Skicka** i käll avsnittet.

Skapa inte en kedja som överskrider 4 hopp. Meddelanden som överskrider 4 hopp är förlorade.

## <a name="next-steps"></a>Nästa steg

Detaljerad information om vidarebefordran finns i följande referens avsnitt:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Mer information om Service Bus prestanda förbättringar finns i 

* [Bra metoder för att öka prestanda med hjälp av meddelanden i Service Bus](service-bus-performance-improvements.md)
* [Partitionerade meddelande enheter][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
