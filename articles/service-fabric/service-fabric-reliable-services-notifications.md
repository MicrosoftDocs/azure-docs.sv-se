---
title: Reliable Services meddelanden
description: Konceptuell dokumentation för Service Fabric Reliable Services meddelanden för Reliable State Manager och tillförlitlig ord lista
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784587"
---
# <a name="reliable-services-notifications"></a>Reliable Services meddelanden
Meddelanden låter klienter spåra ändringar som görs i ett objekt som de är intresserade av. Två typer av objekt har stöd för meddelanden: *tillförlitlig tillstånds hanterare* och *tillförlitlig ord lista*.

Vanliga orsaker till att använda meddelanden är:

* Skapa materialiserade vyer, till exempel sekundära index eller sammanställda, filtrerade vyer av replikens tillstånd. Ett exempel är ett sorterat index över alla nycklar i en tillförlitlig ord lista.
* Skicka övervaknings data, till exempel antalet användare som lagts till den senaste timmen.

Meddelanden utlöses som en del av att tillämpa åtgärder. Därför bör aviseringar hanteras så snabbt som möjligt och synkrona händelser bör inte innehålla dyra åtgärder.

## <a name="reliable-state-manager-notifications"></a>Reliable State Manager-meddelanden
Reliable State Manager tillhandahåller meddelanden för följande händelser:

* Transaktion
  * Checka in
* Tillstånds hanterare
  * Återskapa
  * Tillägg av ett tillförlitligt tillstånd
  * Borttagning av ett tillförlitligt tillstånd

Reliable State Manager spårar aktuella synlighetssekvensnummer-transaktioner. Den enda ändringen i transaktions tillstånd som gör att ett meddelande utlöses är att en transaktion genomförs.

En tillförlitlig tillstånds hanterare har en samling pålitliga tillstånd som en tillförlitlig ord lista och en tillförlitlig kö. Reliable State Manager utlöser meddelanden när den här samlingen ändras: ett tillförlitligt tillstånd läggs till eller tas bort eller hela samlingen återskapas.
Den Reliable State Manager-samlingen återskapas i tre fall:

* Återställning: när en replik startar återställer den sitt tidigare tillstånd från disken. Vid slutet av återställningen använder den **NotifyStateManagerChangedEventArgs** för att utlösa en händelse som innehåller en uppsättning återställda Reliable-tillstånd.
* Fullständig kopia: innan en replik kan anslutas till konfigurations uppsättningen måste den skapas. Ibland kräver detta en fullständig kopia av tillförlitlig tillstånds hanterarens tillstånd från den primära repliken som ska tillämpas på den sekundära sekundära repliken. Reliable State Manager på den sekundära repliken använder **NotifyStateManagerChangedEventArgs** för att utlösa en händelse som innehåller en uppsättning pålitliga tillstånd som den har köpt från den primära repliken.
* Återställning: i scenarier med haveri beredskap kan replikens tillstånd återställas från en säkerhets kopia via **RestoreAsync**. I sådana fall använder Reliable State Manager på den primära repliken **NotifyStateManagerChangedEventArgs** för att utlösa en händelse som innehåller en uppsättning pålitliga tillstånd som den återställs från säkerhets kopian.

Om du vill registrera dig för transaktions meddelanden och/eller tillstånds hanterarens meddelanden måste du registrera dig för **TransactionChanged** -eller **StateManagerChanged** -händelserna i Reliable State Manager. En gemensam plats för att registrera med dessa händelse hanterare är konstruktören för din tillstånds känsliga tjänst. När du registrerar på konstruktorn saknar du inga meddelanden som orsakas av en ändring under livs längden för **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Händelse hanteraren för **TransactionChanged** använder **NotifyTransactionChangedEventArgs** för att ange information om händelsen. Den innehåller åtgärds egenskapen (till exempel **NotifyTransactionChangedAction. commit**) som anger typen av ändring. Den innehåller också transaktions egenskapen som innehåller en referens till den transaktion som ändrades.

> [!NOTE]
> I dag höjs **TransactionChanged** -händelser bara om transaktionen har genomförts. Åtgärden är sedan lika med **NotifyTransactionChangedAction. commit**. Men i framtiden kan händelser aktive ras för andra typer av transaktions status ändringar. Vi rekommenderar att du bara kontrollerar åtgärden och bearbetar händelsen om det är en som du förväntar dig.
> 
> 

Följande är ett exempel på en händelse hanterare för **TransactionChanged** .

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Händelse hanteraren för **StateManagerChanged** använder **NotifyStateManagerChangedEventArgs** för att ange information om händelsen.
**NotifyStateManagerChangedEventArgs** har två underklasser: **NotifyStateManagerRebuildEventArgs** och **NotifyStateManagerSingleEntityChangedEventArgs**.
Du använder egenskapen åtgärd i **NotifyStateManagerChangedEventArgs** för att omvandla **NotifyStateManagerChangedEventArgs** till rätt underklass:

* **NotifyStateManagerChangedAction. Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. Add** och **NotifyStateManagerChangedAction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Följande är ett exempel på en **StateManagerChanged** meddelande hanterare.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Reliable Dictionary-meddelanden
En tillförlitlig ord lista innehåller meddelanden för följande händelser:

* Återskapa: anropas när **ReliableDictionary** har återställt sitt tillstånd från ett återställt eller kopierat lokalt tillstånd eller en säkerhets kopia.
* Clear: anropas när status för **ReliableDictionary** har rensats via **ClearAsync** -metoden.
* Lägg till: anropas när ett objekt har lagts till i **ReliableDictionary**.
* Uppdatera: anropas när ett objekt i **IReliableDictionary** har uppdaterats.
* Ta bort: anropas när ett objekt i **IReliableDictionary** har tagits bort.

För att få Reliable Dictionary-meddelanden måste du registrera dig med händelse hanteraren **DictionaryChanged** på **IReliableDictionary**. En gemensam plats för att registrera med dessa händelse hanterare finns i **ReliableStateManager. StateManagerChanged** Lägg till meddelande.
Om du registrerar när **IReliableDictionary** läggs till i **IReliableStateManager** ser du till att du inte får några meddelanden.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** är exempel metoden som föregående **OnStateManagerChangedHandler** -exempel anropar.
> 
> 

Föregående kod anger **IReliableNotificationAsyncCallback** -gränssnittet, tillsammans med **DictionaryChanged**. Eftersom **NotifyDictionaryRebuildEventArgs** innehåller ett **IAsyncEnumerable** -gränssnitt – som måste räknas upp asynkront återbyggs aviseringar utlöses genom **RebuildNotificationAsyncCallback** i stället för **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> I föregående kod, som en del av bearbetningen av återställnings aviseringen, rensas först det underhållna sammanlagda läget. Eftersom den tillförlitliga samlingen återskapas med ett nytt tillstånd är alla tidigare meddelanden irrelevanta.
> 
> 

Händelse hanteraren för **DictionaryChanged** använder **NotifyDictionaryChangedEventArgs** för att ange information om händelsen.
**NotifyDictionaryChangedEventArgs** har fem underklasser. Använd egenskapen Action i **NotifyDictionaryChangedEventArgs** för att omvandla **NotifyDictionaryChangedEventArgs** till rätt underklass:

* **NotifyDictionaryChangedAction. Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction. Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction. Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction. Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Rekommendationer
* *Slutför* aviserings händelser så snabbt som möjligt.
* Kör *inte* några dyra åtgärder (t. ex. i/O-åtgärder) som en del av synkrona händelser.
* *Kontrol lera* åtgärds typen innan du bearbetar händelsen. Nya åtgärds typer kan läggas till i framtiden.

Här är några saker att tänka på:

* Meddelanden utlöses som en del av körningen av en åtgärd. Till exempel utlöses ett återställnings meddelande som det sista steget i en återställnings åtgärd. En återställning slutförs inte förrän meddelande händelsen har bearbetats.
* Eftersom meddelanden utlöses som en del av tillämpningen, ser klienterna endast meddelanden för lokalt allokerade åtgärder. Eftersom åtgärder endast garanterar att de är lokalt allokerade (med andra ord som loggats) kan de eventuellt eller inte tas bort i framtiden.
* I återställnings Sök vägen utlöses ett enskilt meddelande för varje tillämpad åtgärd. Det innebär att om transaktionen T1 inkluderar Create (X), Delete (X) och Create (X) får du ett meddelande om att skapa X, en för borttagningen och en för att skapa igen, i den ordningen.
* För transaktioner som innehåller flera åtgärder tillämpas åtgärder i den ordning som de togs emot på den primära repliken från användaren.
* Som en del av bearbetningen av falskt förlopp kan en del åtgärder återställas. Meddelanden har Aktiver ATS för sådana åtgärder för att ångra, så att repliken återställs till en stabil plats. En viktig skillnad för att ångra meddelanden är att händelser som har dubbla nycklar sammanställs. Om transaktionen T1 till exempel är ångrad visas ett enda meddelande om att ta bort (X).

## <a name="next-steps"></a>Nästa steg
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
* [Reliable Services säkerhets kopiering och återställning (haveri beredskap)](service-fabric-reliable-services-backup-restore.md)
* [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
