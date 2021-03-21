---
title: ReliableConcurrentQueue i Azure Service Fabric
description: ReliableConcurrentQueue är en kö med hög data flöde som tillåter parallella köer och köer.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784299"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introduktion till ReliableConcurrentQueue i Azure Service Fabric
Reliable samtidig kö är en asynkron, transaktionell och replikerad kö som innehåller hög samtidighet för att köa och ta bort åtgärder. Den är utformad för att leverera högt data flöde och låg latens genom att slappa den strikta FIFO-ordningen som tillhandahålls av en [tillförlitlig kö](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) och i stället tillhandahålla en ordning för bästa ansträngningar.

## <a name="apis"></a>API:er

|Samtidig kö                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| void-kö (T-objekt)           | Uppgift EnqueueAsync (ITransaction TX, T-objekt)                       |
| bool-TryDequeue (ut T-resultat)  | Uppgift< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| int-antal ()                    | långt antal ()                                                     |

## <a name="comparison-with-reliable-queue"></a>Jämförelse med [tillförlitlig kö](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)

Tillförlitlig samtidiga köer erbjuds som ett alternativ till [tillförlitlig kö](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1). Den bör användas i de fall där det inte krävs någon strikt FIFO-beställning, eftersom det krävs en kompromiss med samtidigheten för att garantera FIFO.  I [Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) används lås för att genomdriva FIFO-ordning, med högst en transaktion som tillåts att köa och högst en transaktion som tillåts att ta ur kö i taget. I jämförelsen sänker Reliable-kön ordnings begränsningen och tillåter att alla samtidiga transaktioner översätts till sina åtgärder i kön och ur kö. Sortering efter bästa ansträngningar tillhandahålls, men den relativa ordningen av två värden i en tillförlitlig, tillförlitlig kö kan aldrig garanteras.

En tillförlitlig gemensam kö ger högre genomflöde och lägre latens än en [tillförlitlig kö](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) när det finns flera samtidiga transaktioner som utför köer och/eller-köer.

Ett exempel på användnings fall för ReliableConcurrentQueue är [meddelande köns](https://en.wikipedia.org/wiki/Message_queue) scenario. I det här scenariot skapar en eller flera meddelande producenter och lägger till objekt i kön och ett eller flera meddelande konsumenter hämtar meddelanden från kön och bearbetar dem. Flera producenter och konsumenter kan arbeta oberoende av varandra, med samtidiga transaktioner för att bearbeta kön.

## <a name="usage-guidelines"></a>Användnings rikt linjer
* Kön förväntar sig att objekt i kön har en låg kvarhållningsperiod. Det vill säga att objekten inte behålls i kön under en längre tid.
* Kön garanterar inte strikt FIFO-ordning.
* Kön läser inte egna skrivningar. Om ett objekt är placerat i kö i en transaktion, kommer det inte att vara synligt för en avköhanteraren i samma transaktion.
* Köerna är inte isolerade från varandra. Om objekt *a* tas ur kö i transaktions *txnA*, även om *txnA* inte är dedikerat, visas inte objekt *a* i en samtidig transaktion *txnB*.  Om *txnA* avbryter visas *en* *txnB* omedelbart.
* *TryPeekAsync* beteende kan implementeras med hjälp av en *TryDequeueAsync* och sedan avbryta transaktionen. Ett exempel på det här beteendet finns i avsnittet programmerings mönster.
* Count är icke-transaktionell. Det kan användas för att få en uppfattning om antalet element i kön, men representerar en tidpunkt och kan inte förlitas på.
* Dyrbar bearbetning av de köade objekten bör inte utföras medan transaktionen är aktiv, för att undvika långvariga transaktioner som kan påverka systemets prestanda.

## <a name="code-snippets"></a>Kodfragment
Låt oss titta på några kodfragment och deras förväntade utdata. Undantags hanteringen ignoreras i det här avsnittet.

### <a name="instantiation"></a>Instansiering
Att skapa en instans av en tillförlitlig gemensam kö liknar en annan tillförlitlig samling.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Här följer några kodfragment för att använda EnqueueAsync följt av förväntade utdata.

- *Fall 1: enskild uppgift för att köa*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteten har slutförts och att det inte fanns några samtidiga transaktioner att ändra kön. Användaren kan vänta på att kön innehåller objekten i någon av följande ordningar:

> 10, 20
> 
> 20, 10


- *Fall 2: parallellt köa uppgift*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteterna har slutförts, att aktiviteterna kördes parallellt och att det inte fanns några andra samtidiga transaktioner som ändrade kön. Ingen härledning kan göras om ordningen för objekt i kön. För det här kodfragmentet kan objekten visas i någon av de 4! möjliga beställningar.  Kön försöker behålla objekten i den ursprungliga (köade) ordningen, men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.


### <a name="dequeueasync"></a>DequeueAsync
Här följer några kodfragment för att använda TryDequeueAsync följt av förväntade utdata. Anta att kön redan är ifylld med följande objekt i kön:
> 10, 20, 30, 40, 50, 60

- *Fall 1: enskild avköad uppgift*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Anta att aktiviteten har slutförts och att det inte fanns några samtidiga transaktioner att ändra kön. Eftersom ingen härledning kan göras om ordningen för objekten i kön kan alla tre objekten vara i kö, i vilken ordning som helst. Kön försöker behålla objekten i den ursprungliga (köade) ordningen, men kan tvingas att ändra ordning på dem på grund av samtidiga åtgärder eller fel.  

- *Fall 2: parallell deköa uppgift*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Anta att aktiviteterna har slutförts, att aktiviteterna kördes parallellt och att det inte fanns några andra samtidiga transaktioner som ändrade kön. Eftersom ingen härledning kan göras om ordningen för objekten i kön, kommer listorna *dequeue1* och *dequeue2* att innehålla två objekt, i vilken ordning som helst.

Samma objekt visas *inte* i båda listorna. Om dequeue1 har *10*, *30* skulle dequeue2 därför ha *20*, *40*.

- *Fall 3: beställning av ur kö med transaktions avbrott*

Om du avbryter en transaktion med flygbaserade köer placeras objekten tillbaka i köns huvud. Det går inte att garantera i vilken ordning objekten placeras i köns huvud. Låt oss titta på följande kod:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Anta att objekten togs i kö i följande ordning:
> 10, 20

När vi avbryter transaktionen, läggs objekten tillbaka till huvud kön i kön i någon av följande ordningar:
> 10, 20
> 
> 20, 10

Samma sak gäller för alla fall där transaktionen *inte genomfördes.*

## <a name="programming-patterns"></a>Programmerings mönster
I det här avsnittet ska vi titta på några programmerings mönster som kan vara till hjälp när du använder ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch-avköer
Ett rekommenderat programmerings mönster är att klientens avköer ska grupperas i stället för att en ur kö utförs i taget. Användaren kan välja att begränsa fördröjningar mellan varje batch eller batchstorleken. I följande kodfragment visas den här programmerings modellen. I det här exemplet är bearbetningen klar när transaktionen har genomförts, så om ett fel skulle inträffa under bearbetningen går de obearbetade objekten förlorade utan att bearbetas.  Du kan också utföra bearbetningen inom transaktions omfattningen, men den kan ha en negativ inverkan på prestanda och måste hantera de objekt som redan har bearbetats.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Best-Effort Notification-Based bearbetning
Ett annat intressant programmerings mönster använder Count-API. Här kan vi implementera en aviserings-baserad bearbetning för bästa ansträngning för kön. Antalet köer kan användas för att begränsa en kö eller en avköad aktivitet.  Observera att som i föregående exempel, eftersom bearbetningen sker utanför transaktionen, kan obearbetade objekt gå förlorade om ett fel inträffar under bearbetningen.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Best-Effort dränering
En dränering av kön kan inte garanteras på grund av data strukturens samtidighet.  Det är möjligt att även om ingen användar åtgärd i kön är i flygning, kan ett visst anrop till TryDequeueAsync inte returnera ett objekt som tidigare har placerats i kö och bekräftats.  Det köade objektet kan komma att bli synligt *för att ta* del av kön, men utan en out-of-band-kommunikations mekanism kan en oberoende konsument inte veta att kön har nått ett stabilt tillstånd även om alla tillverkare har stoppats och inga nya åtgärder i kön tillåts. Därför är tömnings åtgärden det bästa arbetet som implementeras nedan.

Användaren bör stoppa alla ytterligare producent-och konsument uppgifter och vänta på att eventuella pågående transaktioner ska genomföras eller avbrytas innan du försöker tömma kön.  Om användaren känner till det förväntade antalet objekt i kön kan de ställa in ett meddelande som signalerar att alla objekt har avplacerats i kö.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Granska
ReliableConcurrentQueue tillhandahåller inte *TryPeekAsync* -API: et. Användare kan få en Peek-semantik genom att använda en *TryDequeueAsync* och sedan avbryta transaktionen. I det här exemplet bearbetas endast köer om objektets värde är större än *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Måste läsa
* [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
* [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services meddelanden](service-fabric-reliable-services-notifications.md)
* [Reliable Services säkerhets kopiering och återställning (haveri beredskap)](service-fabric-reliable-services-backup-restore.md)
* [Tillförlitlig tillstånds hanterarens konfiguration](service-fabric-reliable-services-configuration.md)
* [Komma igång med Service Fabric webb-API-tjänster](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Avancerad användning av Reliable Services programmerings modell](./service-fabric-reliable-services-lifecycle.md)
* [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
