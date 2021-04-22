---
title: Azure Service Bus – upp skjuta upp meddelanden
description: Den här artikeln förklarar hur du skjuter upp leverans Azure Service Bus meddelanden. Meddelandet finns kvar i kön eller prenumerationen, men det är reserverat.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869499"
---
# <a name="message-deferral"></a>Skjut upp meddelanden
När en kö- eller prenumerationsklient tar emot ett meddelande som den är villig att bearbeta, men bearbetningen för närvarande inte är möjlig på grund av särskilda omständigheter, har den möjlighet att "skjuta upp" hämtningen av meddelandet till en senare tidpunkt. Meddelandet finns kvar i kön eller prenumerationen, men det är reserverat.

> [!NOTE]
> Uppskjutna meddelanden flyttas inte automatiskt till kön för dead-letter när [de upphör att gälla.](./service-bus-dead-letter-queues.md#time-to-live) Det här beteendet är enligt designen.

## <a name="sample-scenarios"></a>Exempelscenarier
Upp skjuta upp är en funktion som skapats specifikt för arbetsflödesbearbetningsscenarier. Arbetsflödesramverk kan kräva att vissa åtgärder bearbetas i en viss ordning. De kan behöva skjuta upp bearbetningen av några mottagna meddelanden tills tidigare arbete som informeras av andra meddelanden har slutförts.

Ett enkelt illustrerande exempel är en orderbearbetningssekvens där ett betalningsmeddelande från en extern betalningsleverantör visas i ett system innan den matchande inköpsordern har spridits från butikens front till fulfillment-systemet. I så fall kan uppfyllningssystemet skjuta upp bearbetningen av betalningsmeddelandet tills det finns en order som den ska associeras med. I rendezvous-scenarier, där meddelanden från olika källor driver ett arbetsflöde framåt, kan körningsordningen i realtid vara korrekt, men meddelandena som återspeglar resultatet kan komma i fel ordning.

I slutänden kan uppskjutning hjälpa till med att ordna om meddelanden från ankomstordningen till en ordning som de kan bearbetas i, samtidigt som dessa meddelanden på ett säkert sätt lämnar det meddelandearkiv där bearbetningen måste skjutas upp.

Om ett meddelande inte kan bearbetas eftersom en viss resurs för hantering av meddelandet är tillfälligt otillgänglig, men meddelandebearbetningen inte ska pausas sammanfattningen, [](message-sequencing.md) är ett sätt att placera meddelandet på sidan under några minuter att komma ihåg sekvensnumret i ett schemalagt meddelande som ska publiceras på några minuter och hämta det uppskjutna meddelandet när det schemalagda meddelandet tas emot. Om en meddelandehanterare är beroende av en databas för alla åtgärder och databasen för tillfället inte är tillgänglig, bör den inte använda upp skjuta upp, utan i stället pausa mottagandet av meddelanden helt och hållet tills databasen är tillgänglig igen. 

## <a name="retrieving-deferred-messages"></a>Hämta uppskjutna meddelanden
Uppskjutna meddelanden finns kvar i huvudkön tillsammans med alla andra aktiva meddelanden (till skillnad från meddelanden med dead letter som finns i en underkö), men de kan inte längre tas emot med vanliga mottagningsåtgärder. Uppskjutna meddelanden kan identifieras via [meddelandesurfning](message-browsing.md) om ett program förlorar koll på dem.

För att hämta ett uppskjutet meddelande ansvarar ägaren för att komma ihåg sekvensnumret när det skjutas upp. Alla mottagare som känner till sekvensnumret för ett uppskjutet meddelande kan senare ta emot meddelandet med hjälp av mottagningsmetoder som tar sekvensnumret som en parameter. Mer information om sekvensnummer finns i [Meddelandesekvensering och tidsstämplar.](message-sequencing.md)

## <a name="next-steps"></a>Nästa steg
Prova exemplen på det språk du väljer för att utforska Azure Service Bus funktioner. 

- [Azure Service Bus exempel på klientbibliotek för Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus exempel på klientbibliotek för Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) – se **receive_deferred_message_queue.py-exemplet.** 
- [Azure Service Bus exempel på klientbibliotek för JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/) – se **exemplet advanced/deferral.js.** 
- [Azure Service Bus exempel på klientbibliotek för TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) – se **exemplet advanced/deferral.ts.** 
- [Azure.Messaging.ServiceBus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – Se **exemplet med set-meddelanden.** 

Hitta exempel för äldre .NET- och Java-klientbibliotek nedan:
- [Microsoft.Azure.ServiceBus-exempel för .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) – Se **exemplet för upp senareläggning.** 
- [azure-servicebus-exempel för Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
