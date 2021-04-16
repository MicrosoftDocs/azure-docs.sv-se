---
title: Azure Service Bus – meddelandesurfning
description: Bläddra och granska Service Bus-meddelanden gör att Azure Service Bus klient kan räkna upp alla meddelanden i en kö eller prenumeration.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520130"
---
# <a name="message-browsing"></a>Bläddra i meddelanden
Genom att bläddra eller titta på meddelanden kan en Service Bus-klient räkna upp alla meddelanden i en kö eller i en prenumeration för diagnostik- och felsökningsändamål.

Åtgärden Granska i en kö eller en prenumeration returnerar högst det begärda antalet meddelanden. I följande tabell visas de typer av meddelanden som returneras av åtgärden Peek. 

| Typ av meddelanden | Ingår? | 
| ---------------- | ----- | 
| Aktiva meddelanden | Yes |
| Meddelanden med inkommande meddelanden | No | 
| Låsta meddelanden | Yes |
| Meddelanden som har upphört att gälla |  Kan vara (innan de är inbokade) |
| Schemalagda meddelanden | Ja för köer. Nej för prenumerationer |

## <a name="dead-lettered-messages"></a>Meddelanden med inkommande meddelanden
Om du vill titta på meddelanden med dead letter i en kö eller prenumeration ska **peek-åtgärden** köras på kön för dead letter som är associerad med kön eller prenumerationen. Mer information finns i komma [åt köer för dead letter](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue).

## <a name="expired-messages"></a>Meddelanden som har upphört att gälla
Meddelanden som har upphört att gälla kan ingå i resultaten som returneras från åtgärden Peek. Förbrukade och utgångna meddelanden rensas av en asynkron "skräpinsamlingskörning". Det här steget kanske inte nödvändigtvis sker omedelbart efter att meddelanden upphör att gälla. Därför kan en snabbåtgärd returnera meddelanden som redan har upphört att gälla. Dessa meddelanden tas bort eller skickas utan bokstav när en mottagningsåtgärd anropas i kön eller prenumerationen nästa gång. Tänk på det här beteendet när du försöker återställa uppskjutna meddelanden från kön. 

Ett meddelande som har upphört att gälla är inte längre berättigat till vanlig hämtning på något annat sätt, även när det returneras av Peek. Det är design att returnera dessa meddelanden eftersom Peek är ett diagnostikverktyg som återspeglar loggens aktuella tillstånd.

## <a name="locked-messages"></a>Låsta meddelanden
Peek returnerar även meddelanden som **har låsts** och som för närvarande bearbetas av andra mottagare. Men eftersom Peek returnerar en frånkopplad ögonblicksbild kan ett meddelandes låstillstånd inte observeras för granskade meddelanden.

## <a name="peek-apis"></a>Granska API:er
Peek fungerar på köer, prenumerationer och deras köer med dead letter. 

När åtgärden anropas upprepade gånger räknar den upp alla meddelanden i kön eller prenumerationen i ordning från det lägsta tillgängliga sekvensnumret till det högsta. Det är i vilken ordning meddelandena har förts iqueued, inte den ordning i vilken meddelanden så småningom kan hämtas.

Du kan också skicka en SequenceNumber till en peek-åtgärd. Den används för att avgöra var du ska börja titta från. Du kan göra efterföljande anrop till peek-åtgärden utan att ange parametern för att räkna upp ytterligare.

## <a name="next-steps"></a>Nästa steg
Prova exemplen på det språk du väljer för att utforska funktionen för att granska eller bläddra i meddelanden:

- [Azure Service Bus klientbiblioteksexempel för Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Titta på ett meddelandeexempel**
- [Azure Service Bus exempel på klientbibliotek för Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py-exempel**
- [Azure Service Bus klientbiblioteksexempel för JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** exempel
- [Azure Service Bus klientbiblioteksexempel för TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **browseMessages.ts-exempel**
- [Azure.Messaging.ServiceBus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – Se granska metoder för mottagarklasser i [referensdokumentationen](/dotnet/api/azure.messaging.servicebus).

Hitta exempel för äldre .NET- och Java-klientbibliotek nedan:
- [Microsoft.Azure.ServiceBus-exempel för .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Exempel på meddelandesurfning (peek)** 
- [azure-servicebus-exempel för Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Exempel på bläddring** av meddelande. 
