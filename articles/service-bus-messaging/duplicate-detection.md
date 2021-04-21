---
title: Azure Service Bus dubblettmeddelandeidentifiering | Microsoft Docs
description: Den här artikeln förklarar hur du kan identifiera dubbletter i Azure Service Bus meddelanden. Dupliceringsmeddelandet kan ignoreras och ignoreras.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751282"
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


## <a name="duplicate-detection-window-size"></a>Fönsterstorlek för dubblettidentifiering

Förutom att bara aktivera dubblettidentifiering kan du också konfigurera storleken på tidsperioden för dubblettidentifieringshistoriken då meddelande-ID:n bevaras.
Det här värdet är som standard 10 minuter för köer och ämnen, med ett minsta värde på 20 sekunder till det högsta värdet på 7 dagar.

Om du aktiverar dubblettidentifiering och fönstrets storlek påverkas köns (och ämnets) dataflöde direkt, eftersom alla registrerade meddelande-ID:n måste matchas mot den nyligen skickade meddelandeidentifieraren.

Att hålla fönstret litet innebär att färre meddelande-ID:n måste behållas och matchas, och dataflödet påverkas mindre. För entiteter med högt dataflöde som kräver dubblettidentifiering bör du hålla fönstret så litet som möjligt.

## <a name="next-steps"></a>Nästa steg
Du kan aktivera identifiering av duplicerade meddelanden med Azure Portal, PowerShell, CLI, Resource Manager mall, .NET, Java, Python och JavaScript. Mer information finns i Aktivera [identifiering av duplicerade meddelanden.](enable-duplicate-detection.md) 

I scenarier där klientkoden inte kan skicka ett meddelande på nytt med samma *MessageId* som tidigare är det viktigt att utforma meddelanden som kan bearbetas på ett säkert sätt. Det [här blogginlägget om idempotens](https://particular.net/blog/what-does-idempotent-mean) beskriver olika tekniker för hur du gör det.

Prova exemplen på det språk du väljer för att utforska Azure Service Bus funktioner. 

- [Azure Service Bus klientbiblioteksexempel för Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus klientbiblioteksexempel för Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus klientbiblioteksexempel för JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus klientbiblioteksexempel för TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Hitta exempel för äldre .NET- och Java-klientbibliotek nedan:
- [Microsoft.Azure.ServiceBus-exempel för .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-exempel för Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

