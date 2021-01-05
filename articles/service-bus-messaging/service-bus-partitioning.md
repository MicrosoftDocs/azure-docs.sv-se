---
title: Skapa partitionerade Azure Service Bus köer och ämnen | Microsoft Docs
description: Beskriver hur du partitionerar Service Bus köer och ämnen med hjälp av flera meddelande hanterare.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c500a69f853b11437a0dcaa48213fe3a84da53b
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796643"
---
# <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Azure Service Bus använder flera meddelande hanterare för att bearbeta meddelanden och flera meddelande arkiv för att lagra meddelanden. En konventionell kö eller ett ämne hanteras av en enskild meddelande tjänst och lagras i ett meddelande arkiv. Service Bus *partitioner* aktiverar köer och ämnen, eller *meddelande enheter*, som ska partitioneras över flera meddelande hanterare och meddelande arkiv. Partitionering innebär att det totala data flödet för en partitionerad enhet inte längre begränsas av prestandan för en enskild meddelande utjämning eller meddelande arkiv. Dessutom återges inte en partitionerad kö eller ett ämne som inte är tillgängligt i ett tillfälligt avbrott i ett meddelande arkiv. Partitionerade köer och ämnen kan innehålla alla avancerade Service Bus funktioner, till exempel stöd för transaktioner och sessioner.

> [!NOTE]
> Partitionering är tillgängligt när entiteten skapas för alla köer och ämnen i Basic-eller standard-SKU: er. Den är inte tillgänglig för Premium meddelande-SKU: n, men alla tidigare partitionerade entiteter i Premium-namnområden fortsätter att fungera som förväntat.
 
Det går inte att ändra partitionerings alternativet för alla befintliga köer eller ämnen. Du kan bara ange alternativet när du skapar entiteten.

## <a name="how-it-works"></a>Så här fungerar det

Varje partitionerad kö eller ett ämne består av flera partitioner. Varje partition lagras i ett annat meddelande arkiv och hanteras av en annan meddelande koordinator. När ett meddelande skickas till en partitionerad kö eller ett ämne tilldelar Service Bus meddelandet till en av partitionerna. Valet görs slumpmässigt genom Service Bus eller med hjälp av en partitionsnyckel som avsändaren kan ange.

När en klient vill ta emot ett meddelande från en partitionerad kö, eller från en prenumeration till ett partitionerat ämne, kan Service Bus fråga alla partitioner efter meddelanden och sedan returnera det första meddelandet som hämtas från alla meddelande arkiv till mottagaren. Service Bus cachelagrar de andra meddelandena och returnerar dem när de får ytterligare mottagnings begär Anden. En mottagar klient känner inte till partitionering. Klientens beteende för en partitionerad kö eller ett ämne (till exempel läsa, slutför, skjut, obeställbara meddelanden kön, för hämtning) är identiskt med beteendet för en vanlig entitet.

Gransknings åtgärden på en icke-partitionerad enhet returnerar alltid det äldsta meddelandet, men inte på en partitionerad enhet. I stället returnerar den det äldsta meddelandet i en av de partitioner vars Message Broker svarade först. Det finns ingen garanti för att det returnerade meddelandet är den äldsta i alla partitioner. 

Det kostar inget extra att skicka ett meddelande till eller ta emot ett meddelande från en partitionerad kö eller ett ämne.

> [!NOTE]
> Gransknings åtgärden returnerar det äldsta meddelandet från partitionen baserat på dess ordnings nummer. För partitionerade enheter utfärdas sekvensnumret i förhållande till partitionen. Mer information finns i [ordningsföljd och tidsstämpel för meddelanden](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Aktivera partitionering

Om du vill använda partitionerade köer och ämnen med Azure Service Bus använder du Azure SDK version 2,2 eller senare, eller anger `api-version=2013-10` eller senare i HTTP-begärandena.

### <a name="standard"></a>Standard

På standard-meddelande nivån kan du skapa Service Bus köer och ämnen i 1, 2, 3, 4 eller 5 GB storlekar (Standardvärdet är 1 GB). När partitionering har Aktiver ATS skapar Service Bus 16 kopior (16 partitioner) för entiteten, var och en av de samma storlek som anges. Om du skapar en kö som är 5 GB stor, med 16 partitioner, blir den maximala kös Tor lek (5 \* 16) = 80 GB. Du kan se den maximala storleken på din partitionerade kö eller ämne genom att titta på posten på [Azure Portal][Azure portal]på bladet **Översikt** för entiteten.

### <a name="premium"></a>Premium

Partitionering av entiteter stöds inte på Premium-nivåns namnrymd. Du kan dock fortfarande skapa Service Bus köer och ämnen i 1, 2, 3, 4, 5, 10, 20, 40 eller 80 GB-storlekar (Standardvärdet är 1 GB). Du kan se storleken på din kö eller ett ämne genom att titta på posten på den [Azure Portal][Azure portal]på bladet **Översikt** för den entiteten.

### <a name="create-a-partitioned-entity"></a>Skapa en partitionerad entitet

Det finns flera sätt att skapa en partitionerad kö eller ett ämne. När du skapar en kö eller ett ämne från ditt program, kan du aktivera partitionering för kön eller ämnet genom att ställa in egenskapen [QueueDescription. EnablePartitioning][QueueDescription.EnablePartitioning] eller [TopicDescription. EnablePartitioning][TopicDescription.EnablePartitioning] på **True**. Dessa egenskaper måste anges när kön eller ämnet skapas och är bara tillgängliga i det äldre [windowsazure. Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -biblioteket. Som tidigare nämnts är det inte möjligt att ändra dessa egenskaper i en befintlig kö eller ett ämne. Exempel:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Du kan också skapa en partitionerad kö eller ett ämne i [Azure Portal][Azure portal]. När du skapar en kö eller ett ämne i portalen, är alternativet **Aktivera partitionering** i dialog rutan för kö eller ämne **skapa** markerat som standard. Du kan bara inaktivera det här alternativet på en entitet med standard nivå. Det finns inte stöd för partitionering på Premium-nivå och kryss rutan har ingen påverkan. 

## <a name="use-of-partition-keys"></a>Användning av partitionsnyckel

När ett meddelande har placerats i kö i en partitionerad kö eller ett ämne, Service Bus söka efter en partitionsnyckel. Om den hittar en, väljs den partition som baseras på nyckeln. Om den inte hittar någon partitionsnyckel väljer den partitionen baserat på en intern algoritm.

### <a name="using-a-partition-key"></a>Använda en partitionsnyckel

Vissa scenarier, till exempel sessioner eller transaktioner, kräver att meddelanden lagras i en viss partition. Alla dessa scenarier kräver att du använder en partitionsnyckel. Alla meddelanden som använder samma partitionsnyckel tilldelas samma partition. Om partitionen är tillfälligt otillgänglig returnerar Service Bus ett fel.

Beroende på scenariot används olika meddelande egenskaper som partitionsnyckel:

**SessionID**: om ett meddelande har angetts för egenskapen [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus använder **SessionID** som partitionsnyckel. På så sätt hanteras alla meddelanden som tillhör samma session av samma meddelande Broker. Sessioner gör det möjligt för Service Bus att garantera meddelande beställningar samt konsekvens för sessionstillstånd.

**PartitionKey**: om ett meddelande har egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) men inte egenskapen [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) anger Service Bus egenskap svärdet [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) som partitionsnyckel. Om meddelandet har både [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) och [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) -egenskaperna måste båda egenskaperna vara identiska. Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) är inställd på ett annat värde än egenskapen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , returnerar Service Bus ett ogiltigt åtgärds undantag. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) -egenskapen ska användas om en avsändare skickar transaktionella meddelanden som inte är sessions medveten om. Partitionsnyckel ser till att alla meddelanden som skickas i en transaktion hanteras av samma meddelande utjämnare.

**Messageid**: om [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) -egenskapen har angetts till **True** för kön eller ämnet, och egenskaperna [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) eller [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) inte har angetts, fungerar egenskapen [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid) som partitionsnyckel. (Microsoft .NET-och AMQP-bibliotek tilldelar automatiskt ett meddelande-ID om det inte går att skicka programmet.) I det här fallet hanteras alla kopior av samma meddelande av samma meddelande Broker. Detta ID gör det möjligt för Service Bus att identifiera och eliminera dubbletter av meddelanden. Om egenskapen [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) inte har angetts till **true**, anser Service Bus inte egenskapen [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid) som en partitionsnyckel.

### <a name="not-using-a-partition-key"></a>Använder inte en partitionsnyckel

Om det inte finns någon partitionsnyckel, Service Bus distribuera meddelanden på ett resursallokerings sätt till alla partitioner i den partitionerade kön eller ämnet. Om den valda partitionen inte är tillgänglig tilldelar Service Bus meddelandet till en annan partition. På så sätt slutförs sändnings åtgärden trots att det inte går att få till gång till ett meddelande arkiv. Du kommer dock inte att uppnå den garanterade ordningen som en partitionsnyckel tillhandahåller.

En mer djupgående Beskrivning av kompromissen mellan tillgänglighet (ingen partitionsnyckel) och konsekvens (med hjälp av en partitionsnyckel) finns i [den här artikeln](../event-hubs/event-hubs-availability-and-consistency.md). Den här informationen gäller både partitionerade Service Bus entiteter.

För att ge Service Bus tillräckligt med tid för att köa meddelandet i en annan partition måste det [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) -värde som anges av klienten som skickar meddelandet vara större än 15 sekunder. Vi rekommenderar att du ställer in egenskapen [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) på standardvärdet 60 sekunder.

En partitionsnyckel "fäster" ett meddelande till en speciell partition. Om meddelande arkivet som innehåller den här partitionen inte är tillgängligt returnerar Service Bus ett fel. Om det inte finns någon partitionsnyckel kan Service Bus välja en annan partition och åtgärden lyckas. Därför rekommenderar vi att du inte anger någon partitionsnyckel om det inte krävs.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Avancerade ämnen: använda transaktioner med partitionerade entiteter

Meddelanden som skickas som del i en transaktion måste ange en partitionsnyckel. Nyckeln kan vara någon av följande egenskaper: [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid). Alla meddelanden som skickas som en del av samma transaktion måste ange samma partitionsnyckel. Om du försöker skicka ett meddelande utan en partitionsnyckel i en transaktion returnerar Service Bus ett ogiltigt åtgärds undantag. Om du försöker skicka flera meddelanden inom samma transaktion som har olika partitionsalternativ, returnerar Service Bus ett ogiltigt åtgärds undantag. Exempel:

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

Om någon av egenskaperna som fungerar som en partitionsnyckel anges Service Bus att meddelandet fästs till en speciell partition. Detta händer om en transaktion används eller inte. Vi rekommenderar att du inte anger någon partitionsnyckel om det inte behövs.

## <a name="using-sessions-with-partitioned-entities"></a>Använda sessioner med partitionerade entiteter

Om du vill skicka ett transaktions meddelande till ett session-medvetet ämne eller en kö måste du ange egenskapen [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) också anges måste den vara identisk med egenskapen [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Om de skiljer sig från returnerar Service Bus ett ogiltigt åtgärds undantag.

Till skillnad från vanliga (icke-partitionerade) köer eller ämnen är det inte möjligt att använda en enskild transaktion för att skicka flera meddelanden till olika sessioner. Om försök görs returnerar Service Bus ett ogiltigt åtgärds undantag. Exempel:

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

Service Bus stöder automatisk vidarebefordran av meddelanden från, till eller mellan partitionerade entiteter. Om du vill aktivera automatisk vidarebefordring av meddelanden anger du egenskapen [QueueDescription. ForwardTo][QueueDescription.ForwardTo] i käll kön eller prenumerationen. Om meddelandet anger en partitionsnyckel ([SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid)) används den partitionsnyckel för målentiteten.

## <a name="considerations-and-guidelines"></a>Överväganden och rikt linjer
* **Funktioner för hög konsekvens**: om en entitet använder funktioner som sessioner, dubblettidentifiering eller explicit kontroll över partitionerings nyckeln dirigeras meddelande åtgärderna alltid till en specifik partition. Om någon av partitionerna upplever hög trafik eller det underliggande arkivet inte är felfri, minskas dessa åtgärder och tillgänglighet. Generellt är konsekvensen fortfarande mycket högre än icke-partitionerade enheter. endast en del av trafiken drabbas av problem, i stället för all trafik. Mer information finns i den här [diskussionen om tillgänglighet och konsekvens](../event-hubs/event-hubs-availability-and-consistency.md).
* **Hantering**: åtgärder som att skapa, uppdatera och ta bort måste utföras på alla partitionerna i entiteten. Om en partition inte är felfri kan det leda till fel för dessa åtgärder. För Get-åtgärden måste information, till exempel antal meddelanden, aggregeras från alla partitioner. Om någon partition inte är felfri rapporteras enhetens tillgänglighets status som begränsad.
* **Meddelande scenarier med låg volym**: för sådana scenarier, särskilt när du använder http-protokollet, kan du behöva utföra flera mottagnings åtgärder för att få alla meddelanden. Vid mottagnings förfrågningar utför klient delen en mottagning på alla partitioner och cachelagrar alla svar som tas emot. En efterföljande mottagnings förfrågan på samma anslutning skulle ha nytta av denna cachelagring och mottagnings fördröjningen blir lägre. Men om du har flera anslutningar eller använder HTTP, upprättar en ny anslutning för varje begäran. Därför finns det ingen garanti för att det skulle hamna på samma nod. Om alla befintliga meddelanden är låsta och cachelagrade i en annan klient del returnerar Receive-åtgärden **Null**. Meddelanden upphör att gälla och du kan ta emot dem igen. HTTP Keep-Alive rekommenderas. När du använder partitionering i scenarier med låg volym kan det ta längre tid än förväntat att ta emot åtgärder. Därför rekommenderar vi att du inte använder partitionering i dessa scenarier. Ta bort alla befintliga partitionerade entiteter och återskapa dem med partitionering inaktiverat för att förbättra prestanda.
* **Bläddra/granska meddelanden**: endast tillgängligt i det äldre [windowsazure. Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -biblioteket. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) returnerar inte alltid antalet meddelanden som anges i egenskapen [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) . Det finns två vanliga orsaker till det här beteendet. En orsak är att den sammanlagda storleken på samling av meddelanden överskrider den maximala storleken på 256 KB. Ett annat skäl är att om [EnablePartitioning-egenskapen](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) har angetts till **True** för kön eller ämnet kanske det inte finns tillräckligt med meddelanden i en partition för att slutföra det begärda antalet meddelanden. Om ett program vill ta emot ett visst antal meddelanden bör det i allmänhet anropa [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) upprepade gånger tills det får det antalet meddelanden eller så finns det inga fler meddelanden att granska. Mer information, inklusive kod exempel finns i API-dokumentationen för [QueueClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) eller [SubscriptionClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) .

## <a name="latest-added-features"></a>Senaste tillagda funktioner

* Det finns nu stöd för att lägga till eller ta bort en regel med partitionerade entiteter. En annan än partitionerade entiteter stöds inte i transaktioner. 
* AMQP stöds nu för att skicka och ta emot meddelanden till och från en partitionerad enhet.
* AMQP stöds nu för följande åtgärder: [batch skicka](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [, ta emot,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [ta emot efter sekvensnummer](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Granska](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [förnya lås](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [schema meddelande](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Avbryt schemalagt meddelande](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Lägg till regel](/dotnet/api/microsoft.azure.servicebus.ruledescription), [ta bort regel](/dotnet/api/microsoft.azure.servicebus.ruledescription), [session förnyelse lås](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Ange sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Hämta sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)och [räkna upp sessioner](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Begränsningar för partitionerade entiteter

För närvarande Service Bus tillämpar följande begränsningar för partitionerade köer och ämnen:

* Partitionerade köer och ämnen stöds inte i Premium meddelande nivån. Sessioner stöds på Premier-nivån med hjälp av SessionId. 
* Partitionerade köer och ämnen har inte stöd för att skicka meddelanden som tillhör olika sessioner i en enskild transaktion.
* Service Bus tillåter för närvarande upp till 100 partitionerade köer och ämnen per namnområde. Varje partitionerad kö eller ämne räknas mot kvoten på 10 000 entiteter per namnrymd (gäller inte Premium-nivån).

## <a name="next-steps"></a>Nästa steg

Läs om huvud begreppen i AMQP 1,0-meddelande specifikationen i [AMQP 1,0-protokoll guide](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
