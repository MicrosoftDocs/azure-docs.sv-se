---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2cb203a00bb00767126f95e1fdc2f5aff8990f01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601289"
---
Event Hubs ordnar sekvenser av händelser som skickas till en händelsehubben till en eller flera partitioner. När nya händelser anländer läggs de till i slutet av den här sekvensen. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

En partition kan betraktas som en "inchecknings logg". Partitioner innehåller händelse data som innehåller bröd texten i händelsen, en användardefinierad egenskaps uppsättning som beskriver händelsen, metadata, till exempel dess offset i partitionen, dess nummer i Stream-sekvensen och tidstämpel på service sidan där den godkändes.

![Diagram som visar den äldre till en nyare sekvens med händelser.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Fördelar med att använda partitioner
Event Hubs är utformad för att hjälpa till med bearbetning av stora mängder händelser och partitionering hjälper till på två sätt:

- Även om Event Hubs är en PaaS-tjänst, finns det en fysisk verklighet under och underhåll av en logg som bevarar händelse ordningen kräver att dessa händelser hålls samman i den underliggande lagringen och dess repliker och att det resulterar i ett data flödes tak för en sådan logg. Med partitionering kan du använda flera parallella loggar för samma händelsehubben och därmed multiplicera den tillgängliga kapaciteten för rå IO-dataflöde.
- Dina egna program måste kunna fortsätta att bearbeta mängden händelser som skickas till en Event Hub. Det kan vara komplicerat och kräver avsevärd, utskalad, parallell bearbetnings kapacitet. Kapaciteten för en enskild process för att hantera händelser är begränsad, så du behöver flera processer. Partitioner är hur din lösning försätter dessa processer och ser till att varje händelse har en klar bearbetnings ägare. 

### <a name="number-of-partitions"></a>Antal partitioner
Antalet partitioner anges när det skapas och måste vara mellan 1 och 32 i Event Hubs standard. Antalet partitioner kan vara upp till 2000 partitioner per kapacitets enhet i Event Hubs Dedicated. 

Vi rekommenderar att du väljer minst så många partitioner som du förväntar dig att kräva i kontinuerliga [data flödes enheter (data flödes enheter)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) under den högsta belastningen för ditt program för just den händelsehubben. Du bör beräkna med en enda partition med en data flödes kapacitet på 1 data flödes enheter (1 MB i, 2 MB ut). Du kan skala antal i namn området eller kapacitets enheterna för klustret oberoende av antalet partitioner. En Event Hub med 32 partitioner eller en Event Hub med 1 partition innebär exakt samma kostnad när namn området är inställt på 1 data flödes enheter-kapacitet. 

Antalet partitioner för en Event Hub i ett [dedikerat Event Hubs kluster](../articles/event-hubs/event-hubs-dedicated-overview.md) kan [ökas](../articles/event-hubs/dynamically-add-partitions.md) efter att händelsehubben har skapats, men distributionen av strömmar mellan partitioner kommer att ändras när den görs som mappning av partitionsnyckel till partitioner ändringar, så du bör försöka undvika sådana ändringar om den relativa ordningen på händelse frågor i ditt program.

Att ställa in antalet partitioner på det högsta tillåtna värdet är frestande, men Tänk alltid på att dina händelse strömmar måste struktureras så att du själva kan dra nytta av flera partitioner. Om du behöver absolut bevarat bevarat bevarande av alla händelser eller bara en fåtal av under strömmar, kanske du inte kan dra nytta av många partitioner. Dessutom gör många partitioner bearbetnings sidan mer komplex. 


### <a name="mapping-of-events-to-partitions"></a>Mappning av händelser till partitioner
Du kan använda en partitionsnyckel för att mappa inkommande händelsedata till specifika partitioner för att ordna data. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Den bearbetas via en statisk hash-funktion som skapar partitionstilldelningen. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen.

Händelseutfärdaren känner bara till sin partitionsnyckel, inte den partition som händelserna publiceras till. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition.

Genom att ange en partitionsnyckel kan du hålla samman relaterade händelser i samma partition och i exakt den ordning som de skickades. Partitionsnyckel är en sträng som härleds från din program kontext och identifierar interrelationen mellan händelserna. En sekvens med händelser som identifieras av en partitionsnyckel är en *data ström*. En partition är en multiplexad logg lagring för många sådana strömmar. 

> [!NOTE]
> Även om du kan skicka händelser direkt till partitioner, rekommenderar vi inte det, särskilt när hög tillgänglighet är viktigt för dig. Den nedgraderar tillgängligheten för en Event Hub till partition-Level. Mer information finns i [tillgänglighet och konsekvens](../articles/event-hubs/event-hubs-availability-and-consistency.md).

