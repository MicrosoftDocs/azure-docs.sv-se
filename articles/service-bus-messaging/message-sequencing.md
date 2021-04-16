---
title: Azure Service Bus ordningsföljd och tidsstämplar för meddelanden | Microsoft Docs
description: Den här artikeln förklarar hur du bevarar sekvensering och ordningsföljd (med tidsstämplar) för Azure Service Bus meddelanden.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497106"
---
# <a name="message-sequencing-and-timestamps"></a>Ordningsföljd och tidsstämplar för meddelanden

Sekvensering och tidsstämplar är två funktioner som alltid är aktiverade på alla Service Bus entiteter och visar egenskaperna och för mottagna `SequenceNumber` `EnqueuedTimeUtc` eller bläddrade meddelanden.

I de fall där absolut ordning för meddelanden är betydande och/eller där en konsument behöver en tillförlitlig unik identifierare för meddelanden, stämplar koordinatorn meddelanden med ett mellanrumsfritt, ökande sekvensnummer i förhållande till kön eller ämnet. För partitionerade entiteter utfärdas sekvensnumret i förhållande till partitionen.

**SequenceNumber-värdet** är ett unikt 64-bitars heltal som tilldelas till ett meddelande eftersom det accepteras och lagras av den a broker och fungerar som dess interna identifierare. För partitionerade entiteter återspeglar de översta 16 bitarna partitionsidentifieraren. Sekvensnumren rullas över till noll när 48/64-bitarsintervallet är slut.

Sekvensnumret kan vara betrott som en unik identifierare eftersom det tilldelas av en central och neutral auktoritet och inte av klienter. Det representerar också den verkliga ankomstordningen och är mer exakt än en tidsstämpel som ett orderkriterium, eftersom tidsstämplar kanske inte har en tillräckligt hög upplösning vid extrema meddelandefrekvenser och kan vara föremål för (dock minimal) klockförnedställning i situationer där autens ägarskap övergår mellan noder.

Den absoluta ankomstordningen är viktig, till exempel i affärsscenarier där ett begränsat antal erbjudna varor betjänas enligt principen först-till-först-till-till-leverans medan leveranserna är de sista; försäljning av biljetter är ett exempel.

Tidsstämplar-funktionen fungerar som en neutral och tillförlitlig auktoritet som korrekt avbildar UTC-tiden för ett meddelandes ankomst, vilket återspeglas i egenskapen **EnqueuedTimeUtc.** Värdet är användbart om ett affärsscenario är beroende av tidsgränser, till exempel om ett arbetsobjekt skickades ett visst datum före midnatt, men bearbetningen ligger långt efter köloggen.

## <a name="scheduled-messages"></a>Schemalagda meddelanden

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning, till exempel för att schemalägga ett jobb som ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt. Den här funktionen realiserar en tillförlitlig distribuerad tidsbaserad schemaläggare.

Schemalagda meddelanden materialiseras inte i kön förrän den definierade kötiden. Innan dess kan schemalagda meddelanden avbrytas. Annulleringen tar bort meddelandet.

Du kan schemalägga meddelanden med någon av våra klienter på två sätt:
- Använd det vanliga API:et för att skicka, `ScheduledEnqueueTimeUtc` men ange egenskapen för meddelandet innan det skickas.
- Använd API:et för schemameddelandet och skicka både det normala meddelandet och den schemalagda tiden. Detta returnerar det schemalagda meddelandet **SequenceNumber**, som du senare kan använda för att avbryta det schemalagda meddelandet om det behövs. 

Schemalagda meddelanden och deras sekvensnummer kan också identifieras med hjälp av [bläddring av meddelanden.](message-browsing.md)

**SequenceNumber för** ett schemalagt meddelande är endast giltigt när meddelandet är i det här tillståndet. När meddelandet övergår till det aktiva tillståndet läggs meddelandet till i kön som om det hade varit i kö i det aktuella läget, vilket innefattar att tilldela ett nytt **SequenceNumber**.

Eftersom funktionen är fäst vid enskilda meddelanden och meddelanden bara kan tas i Service Bus stöder inte återkommande scheman för meddelanden.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
