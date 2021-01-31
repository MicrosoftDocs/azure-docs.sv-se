---
title: Azure Service Bus – undantag för meddelanden | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure Service Bus meddelande undantag och föreslagna åtgärder som vidtas när undantaget inträffar.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b56aff2635593d6cb49adbcf3784ddd5cb4fa39
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219153"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus meddelande undantag
Den här artikeln innehåller de .NET-undantag som har genererats av .NET Framework API: er. 

## <a name="exception-categories"></a>Undantags kategorier
API: er för meddelanden genererar undantag som kan ingå i följande kategorier, tillsammans med den associerade åtgärden som du kan vidta för att försöka åtgärda dem. Innebörden och orsakerna till ett undantag kan variera beroende på typen av meddelande enhet:

1. Användar kodnings fel ([system. ArgumentException](/dotnet/api/system.argumentexception), [system. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [system. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [system. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception)). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installations-/konfigurations fel ([Microsoft. Service Bus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [system. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). Allmän åtgärd: Granska konfigurationen och ändra om det behövs.
3. Tillfälliga undantag ([Microsoft. Service Bus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. Service Bus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. Service Bus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allmän åtgärd: försök utföra åtgärden på nytt eller meddela användare. - `RetryPolicy` Klassen i klient-SDK: n kan konfigureras för att hantera återförsök automatiskt. Mer information finns i [vägledning för återförsök](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Andra undantag ([system. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [system. TimeoutException](/dotnet/api/system.timeoutexception), [Microsoft. Service Bus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. Service Bus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allmän åtgärd: särskilt för undantags typen; Se tabellen i följande avsnitt: 

## <a name="exception-types"></a>Undantags typer
I följande tabell visas meddelande undantags typer och deras orsaker, samt förslag på åtgärder som du kan vidta.

| **Undantags typ** | **Beskrivning/orsak/exempel** | **Föreslagen åtgärd** | **Observera vid automatisk/omedelbar återförsök** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern kan ha slutfört den begärda åtgärden. Det kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrol lera system tillstånd för konsekvens och försök igen om det behövs. Se [timeout-undantag](#timeoutexception). |Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Mer information finns i undantags meddelandet. Till exempel, [Slutför ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genererar detta undantag om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -läge. |Kontrol lera koden och dokumentationen. Se till att den begärda åtgärden är giltig. |Det går inte att försöka igen. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |Ett försök gjordes att anropa en åtgärd för ett objekt som redan har stängts, avbrutits eller tagits bort. I sällsynta fall tas den omgivande transaktionen redan bort. |Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. |Det går inte att försöka igen. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller token inte de anspråk som krävs för att utföra åtgärden. |Kontrol lera att token-providern har skapats med rätt värden. Kontrol lera konfigurationen av Access Control Service. |Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |Ett eller flera argument som angavs för metoden är ogiltiga.<br /> URI: n som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller Sök vägs segment (n).<br /> URI-schemat som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltigt. <br />Egenskap svärdet är större än 32 KB. |Kontrol lera anrops koden och se till att argumenten är korrekta. |Det går inte att försöka igen. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entiteten som är kopplad till åtgärden finns inte eller har tagits bort. |Kontrol lera att entiteten finns. |Det går inte att försöka igen. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Försök att ta emot ett meddelande med ett visst ordnings nummer. Det här meddelandet hittades inte. |Se till att meddelandet inte har tagits emot redan. Kontrol lera obeställbara meddelanden kön-kön för att se om meddelandet har deadlettered. |Det går inte att försöka igen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Service Bus. |Kontrol lera att det angivna värd namnet är rätt och att värden kan kontaktas. <p>Om din kod körs i en miljö med en brand vägg/proxy kontrollerar du att trafiken till Service Bus domän/IP-adress och portar inte är blockerad.</p>|Försök igen kan vara till hjälp om det finns tillfälliga anslutnings problem. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Det går inte att bearbeta begäran för tillfället i tjänsten. |Klienten kan vänta en stund och sedan försöka igen. |Klienten kan försöka igen efter ett visst intervall. Om ett återförsök resulterar i ett annat undantag, kontrol lera beteendet för återförsök i detta undantag. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Allmänt meddelande undantag som kan uppstå i följande fall:<p>Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) med ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne).</p><p>Ett försök görs att skicka ett meddelande som är större än 256 KB. </p>Ett fel påträffades av servern eller tjänsten under bearbetningen av begäran. Mer information finns i undantags meddelandet. Det är vanligt vis ett tillfälligt undantag.</p><p>Begäran avbröts eftersom enheten begränsas. Felkod: 50001, 50002, 50008. </p> | Kontrol lera koden och se till att endast serialiserbara objekt används för meddelande texten (eller Använd en anpassad serialiserare). <p>Kontrol lera dokumentationen för de värde typer som stöds och Använd endast typer som stöds.</p><p> Kontrol lera egenskapen [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Om det är **Sant** kan du försöka igen. </p>| Om undantaget beror på begränsningen väntar du några sekunder och försöker sedan igen. Beteendet för återförsök är odefinierat och kanske inte bidrar till andra scenarier.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försök att skapa en entitet med ett namn som redan används av en annan entitet i tjänstens namnrymd. |Ta bort den befintliga entiteten eller Välj ett annat namn för entiteten som ska skapas. |Det går inte att försöka igen. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Meddelande enheten har uppnått maximalt tillåten storlek, eller så har det maximala antalet anslutningar till ett namn område överskridits. |Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess under köer. Se [QuotaExceededException](#quotaexceededexception). |Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus returnerar detta undantag om du försöker skapa en ogiltig regel åtgärd. Service Bus bifogar detta undantag till ett deadlettered-meddelande om ett fel inträffar när regel åtgärden för meddelandet bearbetas. |Kontrol lera om regel åtgärden är korrekt. |Det går inte att försöka igen. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus returnerar detta undantag om du försöker skapa ett ogiltigt filter. Service Bus bifogar detta undantag till ett deadlettered-meddelande om ett fel uppstod under bearbetningen av filtret för meddelandet. |Kontrol lera om filtret är korrekt. |Det går inte att försöka igen. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Försök att godkänna en session med ett angivet sessions-ID, men sessionen är för närvarande låst av en annan klient. |Kontrol lera att sessionen är olåst av andra klienter. |Försök igen kan hjälpa om sessionen har frigjorts under tiden. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |För många åtgärder ingår i transaktionen. |Minska antalet åtgärder som ingår i den här transaktionen. |Det går inte att försöka igen. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Begäran om körnings åtgärder på en inaktive rad entitet. |Aktivera entiteten. |Försök igen kan hjälpa om entiteten har Aktiver ATS i Interim. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus returnerar detta undantag om du skickar ett meddelande till ett ämne där för filtrering är aktiverat och ingen av filtren matchar. |Se till att minst ett filter matchar. |Det går inte att försöka igen. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |En meddelande nytto Last överskrider gränsen på 256 KB. Gränsen på 256 KB är den totala meddelande storleken, som kan omfatta system egenskaper och alla .NET-kostnader. |Minska storleken på meddelandets nytto last och försök sedan igen. |Det går inte att försöka igen. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |Den omgivande transaktionen (*Transaction. Current*) är ogiltig. Den kan ha slutförts eller avbrutits. Inre undantag kan ge ytterligare information. | |Det går inte att försöka igen. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |En åtgärd görs på en transaktion som är tveksam, eller ett försök görs att genomföra transaktionen och transaktionen blir tveksam. |Ditt program måste hantera detta undantag (som ett specialfall) eftersom transaktionen kanske redan har genomförts. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen är det ofta storleken på kön. Fel meddelande egenskapen innehåller mer information, som i följande exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Meddelandet anger att avsnittet överskrider storleks gränsen, i det här fallet 1 GB (standard storleks gränsen). 

### <a name="namespaces"></a>Namnrymder

[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) kan ange att ett program har överskridit det maximala antalet anslutningar till ett namn område. Exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: kön för obeställbara meddelanden och icke fungerande mottagare.

1. **[Kö för obeställbara meddelanden](service-bus-dead-letter-queues.md)** En läsare kan inte slutföra meddelanden och meddelandena returneras till kön/ämnet när låset upphör att gälla. Det kan inträffa om läsaren påträffar ett undantag som förhindrar att den anropar [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). När ett meddelande har lästs 10 gånger flyttas det till kön för obeställbara meddelanden som standard. Detta beteende styrs av egenskapen [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och har standardvärdet 10. När meddelanden sammanfattas i kön för obeställbara meddelanden tar de upp utrymme.
   
    Lös problemet genom att läsa och slutföra meddelandena från kön för obeställbara meddelanden, precis som i andra köer. Du kan använda metoden [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) för att formatera kön för obeställbara meddelanden.
2. **Mottagaren stoppades**. En mottagare har slutat ta emot meddelanden från en kö eller prenumeration. Sättet att identifiera detta är att titta på egenskapen [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , som visar den fullständiga detalj nivån av meddelandena. Om [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) -egenskapen är hög eller ökande, läses inte meddelandena som de ska när de skrivs.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](/dotnet/api/system.timeoutexception) anger att en åtgärd som initierats av användaren tar längre tid än tids gränsen för åtgärden. 

Du bör kontrol lera värdet för egenskapen [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) , så att den här gränsen kan även orsaka en [TimeoutException](/dotnet/api/system.timeoutexception).

Tids gränser förväntas inträffa under eller mellan underhålls åtgärder, till exempel Service Bus tjänst uppdateringar (eller) operativ system uppdateringar på resurser som kör tjänsten. Vid uppdateringar av operativ systemet flyttas entiteter och noderna uppdateras eller startas om, vilket kan orsaka timeout. Information om service nivå avtal (SLA) för Azure Service Bus-tjänsten finns i [SLA för Service Bus](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen anges tids gränsen antingen i egenskapen [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) som en del av anslutnings strängen, eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Själva fel meddelandet kan variera, men det innehåller alltid det timeout-värde som har angetts för den aktuella åtgärden. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Orsak

**MessageLockLostException** utlöses när ett meddelande tas emot med [PeekLock](message-transfers-locks-settlement.md#peeklock) Receive-läge och det lås som klienten äger upphör att gälla på tjänst sidan.

Låset på ett meddelande kan gå ut på grund av olika orsaker – 

  * Lås timern har upphört att gälla innan den förnyades av klient programmet.
  * Klient programmet hämtade låset, sparade det i ett beständigt arkiv och startade sedan om. När den har startats om tittar klient programmet på synlighetssekvensnummer-meddelanden och försökte slutföra dessa.

### <a name="resolution"></a>Lösning

I händelse av en **MessageLockLostException** kan klient programmet inte längre bearbeta meddelandet. Klient programmet kan eventuellt överväga att logga undantaget för analys, men klienten *måste* ta bort meddelandet.

Eftersom låset på meddelandet har upphört att gälla, kommer det att gå tillbaka i kön (eller prenumerationen) och kan bearbetas av nästa klient program som anropar Receive.

Om **MaxDeliveryCount** har överskridits kan meddelandet flyttas till **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Orsak

**SessionLockLostException** utlöses när en session godkänns och låset som innehas av klienten upphör att gälla på tjänst sidan.

Låset på en session kan gå ut på grund av olika orsaker – 

  * Lås timern har upphört att gälla innan den förnyades av klient programmet.
  * Klient programmet hämtade låset, sparade det i ett beständigt arkiv och startade sedan om. När den har startats om tittar klient programmet på synlighetssekvensnummer-sessionerna och försökte bearbeta meddelandena i dessa sessioner.

### <a name="resolution"></a>Lösning

I händelse av en **SessionLockLostException** kan klient programmet inte längre bearbeta meddelandena i sessionen. Klient programmet kan överväga att logga undantaget för analys, men klienten *måste* ta bort meddelandet.

Eftersom låset på sessionen har upphört att gälla, kommer det att gå tillbaka i kön (eller prenumerationen) och kan låsas av nästa klient program som accepterar sessionen. Eftersom sessions låset hålls av ett enda klient program vid en viss tidpunkt, garanteras bearbetningen i ordning.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Orsak

En **SocketException** genereras i nedanstående fall –
   * När ett anslutnings försök Miss lyckas på grund av att värden inte svarade korrekt efter en angiven tid (TCP-felkod 10060).
   * En upprättad anslutning misslyckades eftersom den anslutna värden inte svarar.
   * Ett fel uppstod vid bearbetning av meddelandet eller så överskreds tids gränsen av fjärrvärden.
   * Problem med underliggande nätverks resurser.

### <a name="resolution"></a>Lösning

**SocketException** -felen indikerar att den virtuella datorn som är värd för programmen inte kan konvertera namnet `<mynamespace>.servicebus.windows.net` till motsvarande IP-adress. 

Kontrol lera om nedanstående kommando har slutförts i mappning till en IP-adress.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

som ska ge utdata enligt nedan

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Om namnet ovan **inte matchas** med en IP-adress och namn rymds Ali Aset, kontrollerar du vilken nätverks administratör som ska undersöka ytterligare. Namn matchning görs via en DNS-Server vanligt vis en resurs i kund nätverket. Kontakta Azure-supporten om DNS-matchningen görs med Azure DNS.

Om namn matchningen **fungerar som förväntat** kontrollerar du om anslutningar till Azure Service Bus tillåts [här](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Orsak

**MessagingException** är ett allmänt undantag som kan uppstå av olika orsaker. Några av orsakerna visas nedan.

   * Ett försök gjordes att skapa en **QueueClient** för ett **ämne** eller en **prenumeration**.
   * Storleken på det skickade meddelandet är större än gränsen för den aktuella nivån. Läs mer om [kvoter och begränsningar](service-bus-quotas.md)för Service Bus.
   * En speciell data Plans förfrågan (skicka, ta emot, slutför, överge) avslutades på grund av begränsning.
   * Tillfälliga problem orsakade av tjänst uppgraderingar och omstarter.

> [!NOTE]
> Ovanstående lista över undantag är inte fullständig.

### <a name="resolution"></a>Lösning

Lösnings stegen beror på vad orsakade att **MessagingException** utlöstes.

   * För **tillfälliga problem** (där **_isTransient_*_ har angetts till _*_True_*_) eller för _* begränsnings problem** kan det lösa problemet genom att försöka igen. Standard principen för återförsök i SDK kan utnyttjas för detta.
   * För andra problem anger informationen i undantaget problem-och lösnings stegen som kan härledas från samma.

## <a name="next-steps"></a>Nästa steg
En fullständig Service Bus .NET API-referens finns i [referens för Azure .NET-API](/dotnet/api/overview/azure/service-bus).
Fel söknings tips finns i [fel söknings guiden](service-bus-troubleshooting-guide.md)
