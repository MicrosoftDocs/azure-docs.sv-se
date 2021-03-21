---
title: Data flödes begränsningar för strömnings inmatning – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om inkommande data flödes gränser i Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 72b78f49ed8b7016b92262de7256492fa6d5d7d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676809"
---
# <a name="streaming-ingestion-throughput-limits"></a>Data flödes gränser för strömning

Azure Time Series Insights Gen2 strömma data ingångs begränsningar beskrivs nedan.

> [!TIP]
> Läs [Planera din Azure Time Series Insights Gen2-miljö](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) för en omfattande lista över alla begränsningar.

## <a name="per-environment-limitations"></a>Begränsningar per miljö

I allmänhet visas ingångs priser som faktor för antalet enheter i din organisation, frekvens för händelse utsläpp och storleken på varje händelse:

* **Antal enheter** × **händelse utsläpps frekvens** × **storlek på varje händelse**.

Som standard kan Azure Time Series Insights Gen2 mata in inkommande data med en hastighet på **upp till 1 megabyte per sekund (Mbit/s) per Azure Time Series Insights Gen2-miljö**. Det finns ytterligare begränsningar [per nav-partition](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Miljö stöd för att mata in hastigheter på upp till 2 Mbit/s kan tillhandahållas av begäran.
> * Kontakta oss om du behöver mer data flöde genom att skicka ett support ärende via Azure Portal.

* **Exempel 1:**

    Contoso-leverans har 100 000 enheter som genererar en händelse tre gånger per minut. Storleken på en händelse är 200 byte. De använder en IoT Hub med fyra partitioner som Azure Time Series Insights Gen2 händelse källa.

  * Inmatnings takten för Azure Time Series Insights Gen2-miljön skulle vara: **100 000 enheter * 200 byte/event * (3/60 Event/s) = 1 Mbit/s**.
    * Om du antar balanserade partitioner blir inmatnings takten per partition 0,25 Mbit/s.
    * Contosos överförings takt skulle ligga inom skalnings begränsningarna.

* **Exempel 2:**

    Contoso flotta Analytics har 10 000 enheter som genererar en händelse varje sekund. De använder en Event Hub med ett antal partitioner på 2 som Azure Time Series Insights händelse källa för Gen2. Storleken på en händelse är 200 byte.

  * Miljöns utmatnings frekvens skulle vara: **10 000 enheter * 200 byte/event * 1 händelse/SEK = 2 Mbit/s**.
    * Om du antar balanserade partitioner, blir deras per partitionerings hastighet 1 Mbit/s.
    * Contoso flottans analys "inmatnings takt är över miljön och partitionens gränser. De kan skicka en begäran om att Azure Time Series Insights Gen2 genom Azure Portal för att öka inmatnings takten för deras miljö, och skapa en Händelsehubben med fler partitioner som ligger inom gränserna.

## <a name="hub-partitions-and-per-partition-limits"></a>NAV partitioner och gränser per partition

När du planerar din Azure Time Series Insights Gen2-miljö är det viktigt att du funderar på konfigurationen av de händelse källor som du kommer att ansluta till Azure Time Series Insights Gen2. Både Azure IoT Hub och Event Hubs använder partitioner för att aktivera horisontell skalning för händelse bearbetning.

En *partition* är en ordnad sekvens med händelser som lagras i en hubb. Antalet partitioner anges under fasen skapande av hubb och kan inte ändras.

För Event Hubs partitionering bör du läsa mer om [hur många partitioner behöver jag?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

> [!NOTE]
> De flesta IoT-hubbar som används med Azure Time Series Insights Gen2 behöver bara fyra partitioner.

Oavsett om du skapar en ny hubb för din Azure Time Series Insights Gen2-miljö eller om du använder en befintlig, måste du beräkna din användnings takt per partition för att avgöra om den ligger inom gränserna.

Azure Time Series Insights Gen2 har för närvarande en **gräns för per partition på 0,5 Mbit/s**.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-/regionsspecifika överväganden

När en enhet skapas i IoT Hub tilldelas den permanent till en partition. När du gör det kan IoT Hub garantera händelse ordning (eftersom tilldelningen aldrig ändras).

En fast tilldelning av partitioner påverkar också Azure Time Series Insights Gen2-instanser som matar in data som skickas från IoT Hub underordnade. När meddelanden från flera enheter vidarebefordras till hubben med samma Gateway-enhets-ID kan de komma i samma partition samtidigt som de kan överskrida gränserna för varje partition.

**Påverkan**:

* Om en enda partition får en varaktig takt med att mata in över gränsen, är det möjligt att Azure Time Series Insights Gen2 inte synkroniserar all telemetri innan IoT Hub data lagrings perioden har överskridits. Därför kan skickade data gå förlorade om inmatnings gränserna överskrids konsekvent.

För att minimera denna omständighet rekommenderar vi följande metod tips:

* Beräkna dina priser per miljö och per partition innan du distribuerar din lösning.
* Se till att dina IoT Hub enheter är belastningsutjämnade i det fall där det är möjligt.

> [!IMPORTANT]
> För miljöer som använder IoT Hub som en händelse källa beräknar du inmatnings takten med antalet NAV enheter som används för att se till att priset sjunker under 0,5 Mbit/s per begränsning i partitionen.
>
> * Även om flera händelser anländer samtidigt, så överskrids inte gränsen.

  ![IoT Hub partitions diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Se följande resurser för att lära dig mer om hur du optimerar hubben och partitionerna:

* [IoT Hub skala](../iot-hub/iot-hub-scaling.md)
* [Event Hub-skala](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Event Hub-partitioner](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Nästa steg

* Läs om data [lagring](./concepts-storage.md)
