---
title: Azure Service Bus identifiering av dubbla | Microsoft Docs
description: Den här artikeln förklarar hur du kan identifiera dubbletter i Azure Service Bus meddelanden. Dupliceringsmeddelandet kan ignoreras och ignoreras.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499486"
---
# <a name="duplicate-detection"></a>Dubblettidentifiering

Om ett program misslyckas på grund av ett allvarligt fel omedelbart efter att det har skickat ett meddelande och den omstartade programinstansen felaktigt tror att den tidigare meddelandeleveransen inte har inträffat, kommer samma meddelande att visas två gånger i ett efterföljande meddelande i systemet.

Det är också möjligt att ett fel på klient- eller nätverksnivå inträffar en stund tidigare och att ett skickat meddelande skickas till kön, där bekräftelsen inte har returnerats till klienten. Det här scenariot lämnar klienten osäker på resultatet av skicka-åtgärden.

Dubblettidentifiering tar bort tveksamheterna från dessa situationer genom att avsändaren skickar samma meddelande igen, och kön eller ämnet tar bort eventuella dubbletter.

> [!NOTE]
> Basic-nivån för Service Bus stöder inte dubblettidentifiering. Standard- och Premium-nivåerna stöder dubblettidentifiering. Skillnader mellan dessa nivåer finns i [Service Bus priser.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Hur fungerar det? 
Genom att aktivera dubblettidentifiering kan du hålla reda på det programkontrollerade *MessageId* för alla meddelanden som skickas till en kö eller ett ämne under en angiven tidsperiod. Om ett nytt meddelande skickas med *MessageId* som loggades under tidsperioden rapporteras meddelandet som godkänt (åtgärden för att skicka lyckas), men det nyligen skickade meddelandet ignoreras och ignoreras omedelbart. Inga andra delar av meddelandet än *MessageId* beaktas.

Programkontroll av identifieraren är viktig, eftersom det bara gör att programmet kan koppla *MessageId* till en affärsprocesskontext från vilken det förutsägbart kan rekonstrueras när ett fel inträffar.

För en affärsprocess där flera meddelanden skickas vid hantering av viss programkontext kan *MessageId* vara en sammansatt kontextidentifierare på programnivå, till exempel ett inköpsordernummer och meddelandets ämne, till exempel **12345.2017/payment**.

MessageId *kan* alltid vara något GUID, men att fästa identifieraren för affärsprocessen ger förutsägbar repeterbarhet, vilket önskas för att använda funktionen för dubblettidentifiering effektivt.

> [!IMPORTANT]
>- När **partitionering** är **aktiverat** används `MessageId+PartitionKey` för att fastställa unikhet. När sessioner är aktiverade måste partitionsnyckeln och sessions-ID:t vara samma. 
>- När **partitionering** är **inaktiverat** (standard) `MessageId` används endast för att fastställa unikhet.
>- Information om SessionId, PartitionKey och MessageId finns i [Använda partitionsnycklar.](service-bus-partitioning.md#use-of-partition-keys)
>- [Premier-nivån](service-bus-premium-messaging.md) stöder inte partitionering, så vi rekommenderar att du använder unika meddelande-ID:er i dina program och inte förlitar dig på partitionsnycklar för dubblettidentifiering. 


## <a name="enable-duplicate-detection"></a>Aktivera dubblettidentifiering

Förutom att bara aktivera dubblettidentifiering kan du också konfigurera storleken på tidsperioden för dubblettidentifieringshistoriken då meddelande-ID:n bevaras.
Det här värdet är som standard 10 minuter för köer och ämnen, med ett minsta värde på 20 sekunder till det högsta värdet på 7 dagar.

Om du aktiverar dubblettidentifiering och fönstrets storlek påverkas köns (och ämnets) dataflöde direkt, eftersom alla registrerade meddelande-ID:n måste matchas mot den nyligen skickade meddelandeidentifieraren.

Att hålla fönstret litet innebär att färre meddelande-ID:n måste behållas och matchas, och dataflödet påverkas mindre. För entiteter med högt dataflöde som kräver dubblettidentifiering bör du hålla fönstret så litet som möjligt.

### <a name="using-the-portal"></a>Använda portalen

I portalen aktiveras funktionen för dubblettidentifiering när  entiteten skapas med kryssrutan Aktivera dubblettidentifiering, som är inaktiverad som standard. Inställningen för att skapa nya ämnen är likvärdig.

![Skärmbild av dialogrutan Skapa kö med alternativet Aktivera dubblettidentifiering markerat och markerat i rött.][1]

> [!IMPORTANT]
> Du kan inte aktivera/inaktivera dubblettidentifiering när kön har skapats. Du kan bara göra det när du skapar kön. 

Tidsfönstret för dubblettidentifieringshistoriken kan ändras i fönstret för egenskaper för kö och ämne i Azure Portal.

![Skärmbild av Service Bus med inställningen Egenskaper markerad och alternativet Dubblettidentifieringshistorik markerat i rött.][2]

### <a name="using-sdks"></a>Använda SDK:er

Du kan använda någon av våra SDK:er i .NET, Java, JavaScript, Python och Go för att aktivera funktionen för dubblettidentifiering när du skapar köer och ämnen. Du kan också ändra tidsperioden för dubblettidentifieringshistorik.
Egenskaperna som ska uppdateras när du skapar köer och ämnen för att uppnå detta är:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Observera att även om egenskapsnamnen anges i Pascal-höljet här, använder JavaScript- och Python-SDK:er kamelhölje respektive hölje.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

I scenarier där klientkoden inte kan skicka ett meddelande på nytt med samma *MessageId* som tidigare är det viktigt att utforma meddelanden som kan bearbetas på ett säkert sätt. Det [här blogginlägget om idempotens](https://particular.net/blog/what-does-idempotent-mean) beskriver olika tekniker för hur du gör det.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
