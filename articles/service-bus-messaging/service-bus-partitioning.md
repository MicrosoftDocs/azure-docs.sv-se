---
title: Skapa partitionerade Azure Service Bus köer och ämnen | Microsoft Docs
description: Beskriver hur du partitionerar Service Bus köer och ämnen med hjälp av flera a brokers för meddelanden.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc41bcf31102b19dd35f62452b956faf4f029551
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750922"
---
# <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Azure Service Bus flera a brokers för att bearbeta meddelanden och flera meddelandearkiv för att lagra meddelanden. En konventionell kö eller ett ämne hanteras av en enda meddelandekoordinator och lagras i ett meddelandearkiv. Service Bus *partitioner gör* det möjligt för köer och ämnen, eller meddelandeentiteter, att partitioneras över flera a brokers och meddelandearkiv. Partitionering innebär att det övergripande dataflödet för en partitionerad entitet inte längre begränsas av prestanda för en enskild a broker eller meddelandearkiv. Dessutom gör ett tillfälligt avbrott i ett meddelandearkiv inte att en partitionerad kö eller ett ämne inte är tillgängligt. Partitionerade köer och ämnen kan innehålla alla Service Bus funktioner, till exempel stöd för transaktioner och sessioner.

> [!NOTE]
> Partitionering är tillgängligt när entiteten skapas för alla köer och ämnen i Basic- eller Standard-SKU:er. Den är inte tillgänglig för Premium-meddelande-SKU:n, men alla tidigare partitionerade entiteter i Premium-namnområden fortsätter att fungera som förväntat.
 
Det går inte att ändra partitioneringsalternativet för en befintlig kö eller ett ämne. du kan bara ange alternativet när du skapar entiteten.

## <a name="how-it-works"></a>Så här fungerar det

Varje partitionerad kö eller ämne består av flera partitioner. Varje partition lagras i olika meddelandearkiv och hanteras av en annan a broker. När ett meddelande skickas till en partitionerad kö eller ett Service Bus tilldelar meddelandet till en av partitionerna. Valet görs slumpmässigt genom att Service Bus eller med hjälp av en partitionsnyckel som avsändaren kan ange.

När en klient vill ta emot ett meddelande från en partitionerad kö, eller från en prenumeration till ett partitionerat ämne, frågar Service Bus alla partitioner efter meddelanden och returnerar sedan det första meddelandet som hämtas från något av meddelandearkiven till mottagaren. Service Bus cachelagrar de andra meddelandena och returnerar dem när den tar emot ytterligare mottagningsbegäranden. En mottagande klient känner inte till partitionering; det klientriktade beteendet för en partitionerad kö eller ett ämne (till exempel läsa, slutföra, skjuta upp, deadletter, förfetching) är identiskt med beteendet för en vanlig entitet.

Peek-åtgärden för en icke-partitionerad entitet returnerar alltid det äldsta meddelandet, men inte på en partitionerad entitet. I stället returneras det äldsta meddelandet i en av de partitioner vars meddelandeutjämning svarade först. Det finns ingen garanti för att det returnerade meddelandet är det äldsta i alla partitioner. 

Det kostar inget extra att skicka ett meddelande till eller ta emot ett meddelande från en partitionerad kö eller ett ämne.

> [!NOTE]
> Åtgärden peek returnerar det äldsta meddelandet från partitionen baserat på dess sekvensnummer. För partitionerade entiteter utfärdas sekvensnumret i förhållande till partitionen. Mer information finns i [Meddelandesekvensering och tidsstämplar.](../service-bus-messaging/message-sequencing.md)

## <a name="enable-partitioning"></a>Aktivera partitionering

Om du vill använda partitionerade köer och ämnen med Azure Service Bus använder du Azure SDK version 2.2 eller senare, eller anger eller senare `api-version=2013-10` i dina HTTP-begäranden.

### <a name="standard"></a>Standard

På meddelandenivån Standard kan du skapa Service Bus köer och ämnen i 1, 2, 3, 4 eller 5 GB (standardvärdet är 1 GB). När partitionering är aktiverat Service Bus 16 kopior (16 partitioner) av entiteten, var och en av samma angivna storlek. Om du därför skapar en kö som är 5 GB stor, med 16 partitioner blir den maximala köstorleken (5 \* 16) = 80 GB. Du kan se den maximala storleken för din partitionerade kö eller ditt ämne genom att titta på dess [post på Azure Portal][Azure portal], på **bladet** Översikt för den entiteten.

### <a name="premium"></a>Premium

Partitioneringsentiteter stöds inte i ett premiumnivånamnområde. Du kan dock fortfarande skapa Service Bus-köer och ämnen i 1, 2, 3, 4, 5, 10, 20, 40 eller 80 GB (standardvärdet är 1 GB). Du kan se storleken på din kö eller ditt ämne genom att titta på dess [post på Azure Portal][Azure portal], på **bladet Översikt** för den entiteten.

### <a name="create-a-partitioned-entity"></a>Skapa en partitionerad entitet

Det finns flera sätt att skapa en partitionerad kö eller ett ämne. När du skapar kön eller ämnet från ditt program kan du aktivera partitionering för kön eller ämnet genom att ange egenskapen [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] eller [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] till **true**. Dessa egenskaper måste anges när kön eller ämnet skapas och är endast tillgängliga i det äldre [WindowsAzure.ServiceBus-biblioteket.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Som tidigare nämnts går det inte att ändra dessa egenskaper i en befintlig kö eller ett ämne. Exempel:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Du kan också skapa en partitionerad kö eller ett ämne i [Azure Portal][Azure portal]. När du skapar en kö eller ett ämne i portalen är alternativet Aktivera **partitionering** i kön eller **ämnets** dialogruta Skapa markerat som standard. Du kan bara inaktivera det här alternativet i en entitet på standardnivå. partitionering på Premium-nivå stöds inte och kryssrutan har ingen effekt. 

## <a name="use-of-partition-keys"></a>Användning av partitionsnycklar

När ett meddelande är i kö i en partitionerad kö eller ett ämne Service Bus efter förekomsten av en partitionsnyckel. Om den hittar en väljer den partitionen baserat på den nyckeln. Om den inte hittar en partitionsnyckel väljer den partitionen baserat på en intern algoritm.

### <a name="using-a-partition-key"></a>Använda en partitionsnyckel

Vissa scenarier, till exempel sessioner eller transaktioner, kräver att meddelanden lagras i en specifik partition. Alla dessa scenarier kräver användning av en partitionsnyckel. Alla meddelanden som använder samma partitionsnyckel tilldelas till samma partition. Om partitionen inte är tillgänglig för tillfället Service Bus returnerar ett fel.

Beroende på scenariot används olika meddelandeegenskaper som en partitionsnyckel:

**SessionId:** Om ett meddelande har [egenskapen SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) inställd använder Service Bus **SessionID** som partitionsnyckel. På så sätt hanteras alla meddelanden som tillhör samma session av samma meddelandekö. Sessioner gör Service Bus att garantera meddelandeordning och konsekvens för sessions tillstånd.

**PartitionKey:** Om ett meddelande har [egenskapen PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) men inte [sessions-ID-egenskapsuppsättningen](/dotnet/api/microsoft.azure.servicebus.message.sessionid) använder Service Bus [partitionsnyckelns](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) egenskapsvärde som partitionsnyckel. Om meddelandet har både egenskaperna [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) och [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) inställda måste båda egenskaperna vara identiska. Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) har angetts till ett annat värde än [egenskapen SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) returnerar Service Bus ett ogiltigt åtgärdsfel. Egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ska användas om en avsändare skickar icke-sessionsmedvetna transaktionsmeddelanden. Partitionsnyckeln säkerställer att alla meddelanden som skickas inom en transaktion hanteras av samma meddelandekö.

**MessageId:** Om kön eller ämnet har egenskapen [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) inställd på **true** och egenskaperna [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) eller [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) inte har angetts, fungerar [egenskapen MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) som partitionsnyckel. (Biblioteken Microsoft .NET AMQP tilldelar automatiskt ett meddelande-ID om det sändande programmet inte gör det.) I det här fallet hanteras alla kopior av samma meddelande av samma meddelandekö. Det här ID:t Service Bus att identifiera och eliminera dubblettmeddelanden. Om egenskapen [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) inte har angetts till **true** Service Bus inte egenskapen [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) som en partitionsnyckel.

### <a name="not-using-a-partition-key"></a>Använder inte en partitionsnyckel

Om det inte finns någon partitionsnyckel distribuerar Service Bus meddelanden med resursallokering till alla partitioner i den partitionerade kön eller ämnet. Om den valda partitionen inte är Service Bus tilldelar meddelandet till en annan partition. På så sätt lyckas skicka-åtgärden trots att ett meddelandearkiv är tillfälligt otillgängligt. Du kommer dock inte att uppnå den garanterade ordningen som en partitionsnyckel tillhandahåller.

En mer detaljerad diskussion om kompromissen mellan tillgänglighet (ingen partitionsnyckel) och konsekvens (med hjälp av en partitionsnyckel) finns i den [här artikeln.](../event-hubs/event-hubs-availability-and-consistency.md) Den här informationen gäller lika för partitionerade Service Bus entiteter.

För att Service Bus tillräckligt med tid för att lägga meddelandet i en annan partition måste [operationTimeout-värdet](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) som anges av klienten som skickar meddelandet vara större än 15 sekunder. Vi rekommenderar att du anger egenskapen [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) till standardvärdet 60 sekunder.

En partitionsnyckel "fäster" ett meddelande på en specifik partition. Om meddelandearkivet som innehåller den här partitionen inte är tillgängligt Service Bus returnerar ett fel. Om det inte finns någon partitionsnyckel Service Bus du välja en annan partition så lyckas åtgärden. Därför rekommenderar vi att du inte tillhandahåller en partitionsnyckel om det inte krävs.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Avancerade ämnen: Använda transaktioner med partitionerade entiteter

Meddelanden som skickas som del i en transaktion måste ange en partitionsnyckel. Nyckeln kan vara någon av följande egenskaper: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Alla meddelanden som skickas som en del av samma transaktion måste ange samma partitionsnyckel. Om du försöker skicka ett meddelande utan en partitionsnyckel inom en transaktion Service Bus returnerar ett ogiltigt åtgärdsfel. Om du försöker skicka flera meddelanden inom samma transaktion som har olika partitionsnycklar returneras Service Bus ett ogiltigt åtgärdsfel. Exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Om någon av de egenskaper som fungerar som en partitionsnyckel har angetts Service Bus meddelandet till en specifik partition. Det här inträffar oavsett om en transaktion används eller inte. Vi rekommenderar att du inte anger en partitionsnyckel om det inte behövs.

## <a name="using-sessions-with-partitioned-entities"></a>Använda sessioner med partitionerade entiteter

Om du vill skicka ett transaktionsmeddelande till ett sessionsmedvetet ämne eller en kö måste meddelandet ha [egenskapen SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) inställd. Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) också anges måste den vara identisk med egenskapen [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Om de skiljer sig åt Service Bus returnerar ett ogiltigt åtgärdsfel.

Till skillnad från vanliga (icke-partitionerade) köer eller ämnen går det inte att använda en enda transaktion för att skicka flera meddelanden till olika sessioner. Om ett försök görs Service Bus ett ogiltigt åtgärdsfel. Exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisk vidarebefordran av meddelanden med partitionerade entiteter

Service Bus har stöd för automatisk vidarebefordran av meddelanden från, till eller mellan partitionerade entiteter. Om du vill aktivera automatisk vidarebefordran av meddelanden anger du [egenskapen QueueDescription.ForwardTo][QueueDescription.ForwardTo] för källkön eller prenumerationen. Om meddelandet anger en partitionsnyckel ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) används partitionsnyckeln för målentiteten.

## <a name="considerations-and-guidelines"></a>Överväganden och riktlinjer
* **Funktioner med hög konsekvens:** Om en entitet använder funktioner som sessioner, dubblettidentifiering eller explicit kontroll av partitioneringsnyckel dirigeras meddelandeåtgärderna alltid till en specifik partition. Om någon av partitionerna får hög trafik eller om det underliggande lagret är felaktigt, misslyckas dessa åtgärder och tillgängligheten minskar. Övergripande är konsekvensen fortfarande mycket högre än icke-partitionerade entiteter. Endast en delmängd av trafiken har problem, till skillnad från all trafik. Mer information finns i den här [diskussionen om tillgänglighet och konsekvens.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Hantering:** Åtgärder som Skapa, Uppdatera och Ta bort måste utföras på alla partitioner i entiteten. Om någon partition är skadad kan det leda till fel för dessa åtgärder. För get-åtgärden måste information som antal meddelanden aggregeras från alla partitioner. Om någon partition är skadad rapporteras entitetens tillgänglighetsstatus som begränsad.
* **Scenarier med låg volymmeddelande:** För sådana scenarier, särskilt när du använder HTTP-protokollet, kan du behöva utföra flera mottagningsåtgärder för att kunna hämta alla meddelanden. För mottagningsbegäranden utför fronten en mottagning på alla partitioner och cachelagrar alla svar som tas emot. En efterföljande mottagningsbegäran för samma anslutning skulle dra nytta av den här cachelagringen och svarstiderna blir lägre. Men om du har flera anslutningar eller använder HTTP, upprättas en ny anslutning för varje begäran. Därför finns det ingen garanti för att den hamnar på samma nod. Om alla befintliga meddelanden är låsta och cachelagrade i en annan frontend returnerar mottagningsåtgärden **null**. Meddelanden upphör så småningom att gälla och du kan ta emot dem igen. HTTP keep-alive rekommenderas. När du använder partitionering i scenarier med låg volym kan mottagningsåtgärder ta längre tid än förväntat. Därför rekommenderar vi att du inte använder partitionering i dessa scenarier. Ta bort alla befintliga partitionerade entiteter och återskapa dem med partitionering inaktiverat för att förbättra prestandan.
* **Bläddra/granska meddelanden:** Endast tillgängligt i det äldre [WindowsAzure.ServiceBus-biblioteket.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) returnerar inte alltid det antal meddelanden som anges i [egenskapen MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Det finns två vanliga orsaker till det här beteendet. En orsak är att den aggregerade storleken för insamlingen av meddelanden överskrider den maximala storleken på 256 kB. En annan orsak är att om kön eller ämnet har egenskapen [EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) inställd på **true** kanske en partition inte har tillräckligt med meddelanden för att slutföra det begärda antalet meddelanden. Om ett program i allmänhet vill ta emot ett visst antal meddelanden bör det anropa [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) upprepade gånger tills det antalet meddelanden visas, eller så finns det inga fler meddelanden att granska. Mer information, inklusive kodexempel, finns i api-dokumentationen [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) eller [SubscriptionClient.PeekBatch.](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch)

## <a name="latest-added-features"></a>Senaste tillagda funktioner

* Lägg till eller ta bort regel stöds nu med partitionerade entiteter. De här åtgärderna stöds inte under transaktioner och skiljer sig från icke-partitionerade entiteter. 
* AMQP stöds nu för att skicka och ta emot meddelanden till och från en partitionerad entitet.
* AMQP stöds nu för följande åtgärder: [Batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)Send , [Batch Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), Receive by [Sequence Number](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Renew Lock](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Schedule Message](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), Cancel Scheduled [Message](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Add Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription), Remove [Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription), Session [Renew Lock](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), Set Session [State](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), Get [Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)och [Enumerate Sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Begränsningar för partitionerade entiteter

För Service Bus har följande begränsningar för partitionerade köer och ämnen:

* Partitionerade köer och ämnen stöds inte på Premium-meddelandenivån. Sessioner stöds på den högsta nivån med hjälp av SessionId. 
* Partitionerade köer och ämnen har inte stöd för att skicka meddelanden som tillhör olika sessioner i en enda transaktion.
* Service Bus tillåter för närvarande upp till 100 partitionerade köer och ämnen per namnområde. Varje partitionerad kö eller ett ämne räknas mot kvoten på 10 000 entiteter per namnområde (gäller inte för Premium-nivån).

## <a name="next-steps"></a>Nästa steg
Du kan aktivera partitionering med Azure Portal, PowerShell, CLI, Resource Manager mall, .NET, Java, Python och JavaScript. Mer information finns i [Aktivera partitionering.](enable-partitions.md) 

Läs om huvudbegreppen i AMQP 1.0-meddelandespecifikationen i [AMQP 1.0-protokollguiden.](service-bus-amqp-protocol-guide.md)

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
