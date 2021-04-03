---
title: Azure Service Bus meddelande överföring, lås och kvittning
description: Den här artikeln innehåller en översikt över Azure Service Bus meddelande överföring, lås och kvittnings åtgärder.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fd71edd12e478bcd5f14815c105c14482cf7e2bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89020039"
---
# <a name="message-transfers-locks-and-settlement"></a>Överföringar av meddelanden, lås och uppgörelser

Den centrala funktionen hos en meddelande koordinator, till exempel Service Bus, är att ta emot meddelanden i en kö eller ett ämne och hålla dem tillgängliga för senare hämtning. *Send* är den term som används ofta för överföring av ett meddelande till meddelande utjämningen. *Receive* är den term som används ofta för överföring av ett meddelande till en hämtnings klient.

När en klient skickar ett meddelande vill det vanligt vis veta om meddelandet har överförts korrekt till och godkänts av Service Broker eller om någon sorts fel uppstod. Med den här positiva eller negativa bekräftelsen kvittas klienten och Service Broker-överenskommelsen om överförings tillstånd för meddelandet och kallas därför för *kvittning*.

När Service Broker överför ett meddelande till en klient vill dessutom koordinatorn och klienten skapa en förståelse för huruvida meddelandet har bearbetats och kan därför tas bort, eller om meddelande leveransen eller bearbetningen misslyckades, och därför kan meddelandet behöva levereras igen.

## <a name="settling-send-operations"></a>Lösar sändnings åtgärder

Genom att använda någon av de Service Bus API-klienter som stöds, kommer sändnings åtgärder till Service Bus alltid att lösas uttryckligen, vilket innebär att API-åtgärden väntar på ett godkännande resultat från Service Bus till anlända och slutför sedan åtgärden skicka.

Om meddelandet avvisas av Service Bus innehåller avvisningen en fel indikator och text med ett "spårnings-ID" inuti. Avvisningen innehåller också information om huruvida åtgärden kan utföras på nytt med väntande resultat. I klienten inaktive ras den här informationen i ett undantag och utlöses till anroparen för sändnings åtgärden. Om meddelandet har accepterats slutförs åtgärden tyst.

När du använder AMQP-protokollet, som är det exklusiva protokollet för .NET standard-klienten och Java-klienten och [som är ett alternativ för den .NET Framework klienten](service-bus-amqp-dotnet.md), pipelines överföring och kvittningar och är helt asynkrona, och vi rekommenderar att du använder API-varianterna för asynkron programmerings modell.

En avsändare kan placera flera meddelanden i kabeln i snabb följd utan att behöva vänta på att varje meddelande ska bekräftas, vilket annars skulle vara fallet med SBMP-protokollet eller med HTTP 1,1. De asynkrona sändnings åtgärderna slutförs eftersom respektive meddelanden godkänns och lagras, på partitionerade entiteter eller när en skicka-åtgärd till olika enheter överlappar varandra. Slutförandet kan också uppstå från den ursprungliga sändnings ordningen.

Strategin för att hantera resultatet av sändnings åtgärder kan ha omedelbar och betydande prestanda påverkan för ditt program. Exemplen i det här avsnittet är skrivna i C# och gäller motsvarande för Java-framtida.

Om programmet genererar burst-meddelanden, illustreras här med en enkel loop och var på att vänta på att varje sändnings åtgärd skulle skickas innan nästa meddelande, synkrona eller asynkrona API-former, som skickar 10 meddelanden bara slutförs efter 10 löpande resor för kvittning.

Med ett värde som antas 70 millisekunder för TCP tur och retur från en lokal plats till Service Bus och som ger dig bara 10 ms för att Service Bus att acceptera och lagra varje meddelande, tar följande loop upp minst 8 sekunder, inte räknar överförings tiden för nytto laster eller potentiella vägar för belastnings belastning:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Om programmet startar de 10 asynkrona sändnings åtgärderna i omedelbar följd och väntar på att de ska slutföras separat, överlappar den fördröjnings tiden för de tio sändnings åtgärderna. 10 meddelanden överförs i omedelbar följd, vilket kan vara till och med dela TCP-ramar och den övergripande överförings tiden beror på den nätverks-relaterade tiden det tar att hämta meddelanden som överförs till Service Broker.

Genom att göra samma antaganden som för föregående slinga kan den totala överlappande körnings tiden för följande slinga vara i drift under en sekund:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Det är viktigt att Observera att alla asynkrona programmerings modeller använder någon form av minnesallokering-baserad, Dold arbetskö som innehåller väntande åtgärder. När [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) eller **send** (Java) returnerar, köas sändnings uppgiften i den arbets kön, men protokoll-gesten börjar bara när det är dags att köra uppgiften. För kod som är försiktig för att skicka meddelanden till och var tillförlitlighet är ett problem, bör du tänka på att inte för många meddelanden placeras i flygningen på samma gång, eftersom alla skickade meddelanden tar upp minnet tills de faktiskt har placerats i kabeln.

Semaforer, som du ser i följande kodfragment i C#, är synkroniseringsobjekt som aktiverar sådan begränsning på program nivå vid behov. Den här användningen av en semafor gör att högst 10 meddelanden kan finnas i flygningen samtidigt. En av de 10 tillgängliga semafors låsen tas före sändningen och släpps när sändningen är klar. Det elfte passet i slingan väntar tills minst ett av de tidigare överföringarna har slutförts och gör sedan dess lås tillgängligt:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Program bör **aldrig** initiera en asynkron sändnings åtgärd i "Fire and glömma"-sätt utan att hämta resultatet av åtgärden. Detta kan läsa in den interna och osynliga uppgifts kön upp till minnes överbelastning och hindra programmet från att identifiera sändnings fel:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Med en AMQP-klient med låg nivå accepterar Service Bus även "förbetalda" överföringar. En förbetald överföring är en brand-och-glöm-åtgärd för vilken resultatet, oavsett hur, inte rapporteras tillbaka till klienten och meddelandet betraktas som kvittat när det skickas. Bristen på feedback till klienten innebär också att det inte finns några åtgärds bara data tillgängliga för diagnostik, vilket innebär att det här läget inte är kvalificerat för hjälp via supporten för Azure.

## <a name="settling-receive-operations"></a>Lösa mottagnings åtgärder

För Receive-åtgärder möjliggör Service Bus-API-klienter två olika explicita lägen: *Receive-och-Delete* och *Peek-lock*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

Läget för att [ta emot och ta bort](/dotnet/api/microsoft.servicebus.messaging.receivemode) instruerar koordinatorn att ta hänsyn till alla meddelanden som skickas till den mottagande klienten som kvittas när de skickas. Det innebär att meddelandet anses förbrukat så snart som Service Broker har placerat det på kabeln. Om meddelande överföringen Miss lyckas går meddelandet förlorat.

Inifrån i det här läget är att mottagaren inte behöver vidta ytterligare åtgärder i meddelandet och inte heller är långsam genom att vänta på resultatet av kvittningen. Om data i de enskilda meddelandena har ett lågt värde och/eller bara är meningsfulla för en mycket kort stund, är det här läget ett rimligt val.

### <a name="peeklock"></a>PeekLock

Läget [Peek-lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) visar att den mottagande klienten vill lösa mottagna meddelanden explicit. Meddelandet görs tillgängligt för att mottagaren ska kunna bearbeta, medan den hålls under ett exklusivt lås i tjänsten så att andra konkurrerande mottagare inte kan se det. Längden på låset definieras ursprungligen på kö-eller prenumerations nivå och kan utökas av klienten som äger låset, via [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) -åtgärden.

När ett meddelande är låst kan andra klienter som tar emot från samma kö eller prenumeration ta på Lås och hämta nästa tillgängliga meddelanden som inte är aktiva i det aktiva låset. När låset på ett meddelande har frigjorts eller när låset upphör att gälla, kommer meddelandet att öppnas på eller närmast början av hämtnings ordningen för omleverans.

När meddelandet har frigjorts upprepade gånger av mottagare eller om de tillåter att låset förflyter för ett definierat antal gånger ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), tas meddelandet bort automatiskt från kön eller prenumerationen och placeras i den associerade kön för obeställbara meddelanden.

Den mottagande klienten initierar en kvittning av ett mottaget meddelande med en positiv bekräftelse när den anropar [fullständig](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) på API-nivå. Detta anger att utjämningen av meddelandet har bearbetats och att meddelandet tas bort från kön eller prenumerationen. Service Broker svarar på mottagarens kvittnings avsikt med ett svar som anger om kvittningen kan utföras.

När den mottagande klienten Miss lyckas med att bearbeta ett meddelande, men vill att meddelandet ska skickas igen, kan du uttryckligen be om att meddelandet släpps och låses upp direkt genom att anropa [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) eller så kan det göra ingenting och låta låset gå ut.

Om en mottagar klient inte kan bearbeta ett meddelande och vet att det inte går att komma åt meddelandet och försöka utföra åtgärden igen, kan det avvisa meddelandet, som flyttar det till kön för obeställbara meddelanden genom att anropa [obeställbara meddelanden kön](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), vilket innebär att du också kan ange en anpassad egenskap inklusive en orsaks kod som kan hämtas med meddelandet från kön för obeställbara meddelanden.

Ett specialfall av kvittning är uppskjutande, som beskrivs i en separat artikel.

Åtgärderna **Complete** eller **obeställbara meddelanden kön** och **RenewLock** kan Miss lyckas på grund av nätverks problem, om det kvarhållna låset har upphört att gälla eller om det finns andra villkor för service sidan som förhindrar kvittning. I ett av de sistnämnda fallen skickar tjänsten en negativ bekräftelse på att det finns ett undantag i API-klienterna. Om orsaken är en bruten nätverks anslutning, släpps låset eftersom Service Bus inte stöder återställning av befintliga AMQP-länkar på en annan anslutning.

Om **detta Miss lyckas** , vilket vanligt vis sker vid mycket slut på meddelande hantering, och i vissa fall efter bearbetningen av bearbetnings tiden, kan mottagar programmet avgöra om det bevarar status för arbetet och ignorerar samma meddelande när det levereras en andra gång, eller om det tosses arbets resultatet och försöker igen när meddelandet har levererats igen.

Den typiska mekanismen för att identifiera dubbla meddelande leveranser är genom att kontrol lera meddelande-ID: t, som kan anges av avsändaren till ett unikt värde, eventuellt justerad med en identifierare från ursprungs processen. Ett jobb schema skulle troligt vis ange meddelande-ID: t till identifieraren för det jobb som det försöker tilldela till en anställd med den angivna arbetaren, och arbetaren skulle ignorera den andra förekomsten av jobb tilldelningen om jobbet redan har slutförts.

> [!IMPORTANT]
> Det är viktigt att notera att låset som PeekLock får i meddelandet är temporärt och kan gå förlorat under följande förhållanden
>   * Tjänst uppdatering
>   * Uppdatering av operativ system
>   * Ändra egenskaperna för entiteten (kö, ämne, prenumeration) medan låset hålls.
>
> När låset förloras genererar Azure Service Bus en LockLostException som kommer att visas på klient program koden. I det här fallet bör klientens standard omprövnings logik automatiskt starta och försöka utföra åtgärden igen.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
