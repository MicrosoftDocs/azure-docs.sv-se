---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/24/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce906ad62b51956cb85f854846740fa09e06895d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97665047"
---
Händelsehubben ordnar sekvenser av händelser till en eller flera partitioner. När nya händelser anländer läggs de till i slutet av den här sekvensen. En partition kan betraktas som en ”genomförandelogg”.

Partitioner innehåller händelse data som innehåller bröd texten i händelsen, en användardefinierad egenskaps uppsättning som beskriver händelsen och metadata, till exempel dess offset i partitionen, dess nummer i Stream-sekvensen och den tidsstämpel för service sidan där den accepterades.

![Diagram som visar den äldre till en nyare sekvens med händelser.](./media/event-hubs-partitions/partition.png)

Event Hubs är utformad för att hjälpa till med bearbetning av mycket stora mängder händelser och partitionering hjälper till på två sätt:

Först, även om Event Hubs är en PaaS-tjänst, finns det en fysisk verklighet under och underhåll av en logg som bevarar händelse ordningen kräver att dessa händelser hålls samman i den underliggande lagringen och dess repliker, och att de resulterar i ett data flödes tak för en sådan logg. Med partitionering kan du använda flera parallella loggar för samma Händelsehubben och därmed multiplicera den tillgängliga kapaciteten för rå IO-dataflöde.

För det andra måste dina egna program kunna fortsätta att bearbeta mängden händelser som skickas till en Event Hub. Det kan vara ganska komplicerat och kräver avsevärd, skalbar, parallell bearbetnings kapacitet. Rationellt för partitioner är detsamma som ovan: kapaciteten för en enskild process för att hantera händelser är begränsad, och därför behöver du flera processer, och partitioner är hur din lösning delar dessa processer och säkerställer att varje händelse har en klar bearbetnings ägare. 

Event Hubs behåller händelser för en konfigurerad kvarhållningsperiod som gäller för alla partitioner. Händelser tas bort automatiskt när kvarhållningsperioden har nåtts. Om du anger en kvarhållningsperiod på en dag blir händelsen otillgänglig exakt 24 timmar efter att den har accepterats. Du kan inte uttryckligen ta bort händelser. 

Den tillåtna Retentions tiden är upp till 7 dagar för Event Hubs standard och upp till 90 dagar för Event Hubs Dedicated. Om du behöver arkivera händelser utöver den tillåtna kvarhållningsperioden, kan du låta dem [lagras automatiskt i Azure Storage eller Azure Data Lake genom att aktivera funktionen för Event Hubs Capture](../articles/event-hubs/event-hubs-capture-overview.md)och om du behöver söka efter eller analysera sådana djup arkiv kan du [enkelt importera dem till Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) eller andra liknande butiker och analys plattformar. 

Orsaken till Event Hubs gräns för datakvarhållning baserat på tid är att förhindra att stora mängder historiska kund data får fångas i en djup lagring som bara indexeras av en tidsstämpel och endast tillåter sekventiell åtkomst. Arkitektur filosofin här är att historiska data behöver bättre indexering och direkt åtkomst än i real tids händelse gränssnittet som Event Hubs eller Kafka tillhandahåller. Händelse Ströms motorer är inte väl lämpade för att spela upp rollen som data sjöar eller långsiktiga Arkiv för händelse källor. 

Eftersom partitioner är oberoende av varandra och innehåller sina egna sekvenser med data, växer de ofta i olika takt. I Event Hubs är det ingen risk för att kräva administrativ åtgärd som den skulle vara, till exempel i Apache Kafka, men ojämn distribution leder till ojämn inläsning på dina efterföljande händelse processorer.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Antalet partitioner anges när det skapas och måste vara mellan 1 och 32 i Event Hubs standard. Antalet partitioner kan vara upp till 2000 partitioner per kapacitets enhet i Event Hubs Dedicated. 

Vi rekommenderar att du väljer minst så många partitioner som du förväntar dig att kräva i kontinuerliga [data flödes enheter (data flödes enheter)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) under den högsta belastningen för ditt program för just den händelsehubben. Du bör beräkna med en enda partition med en data flödes kapacitet på 1 data flödes enheter (1 MB i, 2 MB ut). Du kan skala antal i namn området eller kapacitets enheterna för klustret oberoende av antalet partitioner. En Event Hub med 32 partitioner eller en Event Hub med 1 partition innebär exakt samma kostnad när namn området är inställt på 1 data flödes enheter-kapacitet. 

Program styr mappningen av händelser till partitioner på något av tre sätt:

- Genom att ange partitionsnyckel, som är konsekvent mappad (med en hash-funktion) till en av de tillgängliga partitionerna. 
- Genom att inte ange en partitionsnyckel, som gör det möjligt att aktivera Broker slumpmässigt väljer du en partition för en specifik händelse.
- Genom att uttryckligen skicka händelser till en specifik partition.

Genom att ange en partitionsnyckel kan du hålla samman relaterade händelser i samma partition och i exakt den ordning som de skickades. Partitionsnyckel är en sträng som härleds från din program kontext och identifierar interrelationen mellan händelserna.

En sekvens med händelser som identifieras av en partitionsnyckel är en *data ström*. En partition är en multiplexad logg lagring för många sådana strömmar. 

Det går att öka antalet partitioner för en Event Hub när Händelsehubben har skapats, men distributionen av strömmar mellan partitioner kommer att ändras när den görs som mappning av partitionsnyckel till partitioner ändringar, så du bör försöka hårt för att undvika sådana ändringar om den relativa ordningen på händelse frågor i ditt program.

Att ställa in antalet partitioner på det högsta tillåtna värdet är frestande, men Tänk alltid på att dina händelse strömmar måste struktureras så att du själva kan dra nytta av flera partitioner. Om du behöver absolut bevarat bevarat bevarande av alla händelser eller bara en fåtal av under strömmar, kanske du inte kan dra nytta av många partitioner. Dessutom gör många partitioner bearbetnings sidan mer komplex. 

Partitioner kan skickas direkt, men det rekommenderas inte. I stället kan du använda konstruktioner på högre nivå som introduceras i avsnittet [händelse utfärdare](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

Mer information om partitioner och avvägningen mellan tillgänglighet och tillförlitlighet finns i [Programmeringsguide för Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) och i artikeln om [tillgänglighet och konsekvens i Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md).
