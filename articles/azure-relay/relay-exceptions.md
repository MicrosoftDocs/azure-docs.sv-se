---
title: Azure Relay undantag och hur du löser dem | Microsoft Docs
description: Lista över Azure Relay undantag och föreslagna åtgärder som du kan vidta för att lösa dem.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e3f9fe2e8aac826bd63d9842bd3380be10e1b87e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313954"
---
# <a name="azure-relay-exceptions"></a>Azure Relay undantag

Den här artikeln innehåller några undantag som kan genereras av Azure Relay API: er. Den här referensen kan komma att ändras, så kom tillbaka för uppdateringar.

## <a name="exception-categories"></a>Undantags kategorier

Relä-API: erna genererar undantag som kan ingå i följande kategorier. I listan finns även förslag på åtgärder som du kan vidta för att lösa undantagen.

*   **Användar kodnings fel**: [system. ArgumentException](/dotnet/api/system.argumentexception), [system. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [system. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [system. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Allmän åtgärd**: försök att åtgärda koden innan du fortsätter.
*   **Installations-/konfigurations fel**: [system. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Allmän åtgärd**: Granska konfigurationen. Om det behövs ändrar du konfigurationen.
*   **Tillfälliga undantag**: [Microsoft. Service Bus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. Service Bus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. Service Bus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Allmän åtgärd**: försök utföra åtgärden på nytt eller meddela användare.
*   **Andra undantag**: [system. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [system. TimeoutException](/dotnet/api/system.timeoutexception). 

    **Allmän åtgärd**: särskilt för undantags typen. Se tabellen i följande avsnitt. 

## <a name="exception-types"></a>Undantags typer

I följande tabell visas meddelande undantags typer och deras orsaker. Det noterar även föreslagna åtgärder som du kan vidta för att hjälpa till att lösa undantagen.

| **Undantagstyp** | **Beskrivning** | **Föreslagen åtgärd** | **Observera vid automatisk eller omedelbar återförsök** |
| --- | --- | --- | --- |
| [Standardvärde](/dotnet/api/system.timeoutexception) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Servern kan ha slutfört den begärda åtgärden. Detta kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrol lera systemets tillstånd för konsekvens och försök sedan igen om det behövs. Se [TimeoutException](#timeoutexception). |Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [Ogiltig åtgärd](/dotnet/api/system.invalidoperationexception) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Mer information finns i undantags meddelandet. |Kontrol lera koden och dokumentationen. Kontrol lera att den begärda åtgärden är giltig. |Det går inte att göra ett nytt försök. |
| [Åtgärden avbröts](/dotnet/api/system.operationcanceledexception) |Ett försök gjordes att anropa en åtgärd för ett objekt som redan har stängts, avbrutits eller tagits bort. I sällsynta fall tas den omgivande transaktionen redan bort. |Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. |Det går inte att göra ett nytt försök. |
| [Obehörig åtkomst](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller inte token de anspråk som krävs för att utföra åtgärden. |Kontrol lera att token-providern har skapats med rätt värden. Kontrol lera konfigurationen av Access Controls tjänsten. |Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [Argument undantag](/dotnet/api/system.argumentexception),<br /> [Argumentet null](/dotnet/api/system.argumentnullexception),<br />[Argumentet är utanför intervallet](/dotnet/api/system.argumentoutofrangeexception) |Ett eller flera av följande har inträffat:<br />Ett eller flera argument som angavs för metoden är ogiltiga.<br /> Den URI som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) innehåller ett eller flera Sök vägs segment.<br />URI-schemat som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) är ogiltigt. <br />Egenskap svärdet är större än 32 KB. |Kontrol lera anrops koden och se till att argumenten är korrekta. |Det går inte att göra ett nytt försök. |
| [Servern är upptagen](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Tjänsten kan för närvarande inte bearbeta begäran. |Klienten kan vänta en stund och sedan försöka igen. |Klienten kan försöka igen efter ett angivet intervall. Om ett återförsök resulterar i ett annat undantag, kontrol lera beteendet för återförsök i detta undantag. |
| [Kvoten överskreds](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Meddelande enheten har uppnått maximalt tillåten storlek. |Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess under köer. Se [QuotaExceededException](#quotaexceededexception). |Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [Meddelandets storlek har överskridits](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |En meddelande nytto Last överskrider gränsen på 256 KB. Observera att gränsen på 256 KB är den totala meddelande storleken. Den totala meddelande storleken kan omfatta system egenskaper och eventuella Microsoft .NET kostnader. |Minska storleken på meddelandets nytto last och försök sedan igen. |Det går inte att göra ett nytt försök. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

För vidarebefordran omsluts detta undantag av [system. ServiceModel. QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception), vilket indikerar att det maximala antalet lyssnare har överskridits för den här slut punkten. Detta anges i **MaximumListenersPerEndpoint** -värdet för undantags meddelandet.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](/dotnet/api/system.timeoutexception) anger att en åtgärd som initierats av användaren tar längre tid än tids gränsen för åtgärden. 

Kontrol lera värdet för egenskapen [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Genom att nå den här gränsen kan även en [TimeoutException](/dotnet/api/system.timeoutexception)uppstå.

För relä kan du få timeout-undantag när du först öppnar en relä-avsändare. Det finns två vanliga orsaker till det här undantaget:

*   Värdet för [opentime](/previous-versions/) kan vara för litet (om det även är en del av en sekund).
* En lokal relä lyssnare kanske inte svarar (eller kan stöta på problem med brand Väggs regler som förhindrar att lyssnare accepterar nya klient anslutningar) och värdet för [opentime](/previous-versions/) -värdet är mindre än cirka 20 sekunder.

Exempel:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet:

*   **Felaktig konfiguration**
    
    Åtgärdens tids gräns kan vara för liten för drifts villkoret. Standardvärdet för åtgärds tids gränsen i klient-SDK är 60 sekunder. Kontrol lera om värdet i din kod är för litet. Observera att CPU-användningen och villkoret för nätverket kan påverka hur lång tid det tar för en åtgärd att slutföras. Det är en bra idé att inte ange tids gränsen för åtgärden till ett mycket litet värde.
*   **Tillfälligt tjänst fel**

    I vissa fall kan relä tjänsten uppleva fördröjningar vid bearbetning av begär Anden. Detta kan inträffa, till exempel under perioder med hög trafik. Om detta inträffar kan du försöka utföra åtgärden igen efter en fördröjning tills åtgärden har slutförts. Om samma åtgärd fortfarande Miss lyckas efter flera försök kan du kontrol lera [statusen för tjänsten Azure-tjänst](https://azure.microsoft.com/status/) för att se om det finns kända tjänst avbrott.

## <a name="next-steps"></a>Nästa steg
* [Azure Relay vanliga frågor](relay-faq.yml)
* [Skapa ett relä namn område](relay-create-namespace-portal.md)
* [Kom igång med Azure Relay och .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Azure Relay och nod](relay-hybrid-connections-node-get-started.md)
