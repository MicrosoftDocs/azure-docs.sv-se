---
title: Skalbarhet – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du skalar Azure-Event Hubs med partitioner och data flödes enheter.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601292"
---
# <a name="scaling-with-event-hubs"></a>Skalning med Event Hubs

Det finns två faktorer som påverkar skalning med Event Hubs.
*   Genomflödesenheter
*   Partitioner

## <a name="throughput-units"></a>Genomflödesenheter

Data flödes kapaciteten för Event Hubs styrs av *data flödes enheter*. Genomflödesenheter är färdiga kapacitetsenheter. Med ett enda data flöde kan du:

* Ingress: upp till 1 MB per sekund eller 1000 händelser per sekund (beroende på vilket som kommer först).
* Utgående: upp till 2 MB per sekund eller 4096 händelser per sekund.

Utöver kapaciteten för köpta genomflödesenheter är den inkommande trafiken begränsad och en [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) returneras. Utgående trafik genererar inte begränsningsundantag, men är fortfarande begränsad till kapaciteten för de köpta genomflödesenheterna. Om du får felmeddelanden om publiceringsfrekvensen eller om du förväntar dig större utgående trafik kontrollerar du hur många genomflödesenheter du har köpt för namnområdet. Du kan hantera genomflödesenheter på bladet **Skala** för namnområdena på [Azure-portalen](https://portal.azure.com). Du kan också hantera data flödes enheter program mässigt med hjälp av [Event Hubs API: er](./event-hubs-samples.md).

Data flödes enheter köps och faktureras per timme. När de väl har köpts debiteras de för minst en timme. Upp till 20 data flödes enheter kan köpas för ett Event Hubs-namnområde och delas över alla händelse nav i namn området.

Funktionen för **Automatisk** ökning i Event Hubs skalas automatiskt upp genom att öka antalet data flödes enheter för att möta användnings behoven. Ökande data flödes enheter förhindrar begränsnings scenarier där:

- Data ingångs frekvensen överskrider den angivna data flödes enheten.
- Taxan för utgående begär Anden överskrider mängd enheter för data flöde.

Event Hubs tjänsten ökar data flödet när belastningen ökar utöver minimi tröskelvärdet, utan att begär Anden som misslyckats med ServerBusy-fel uppstår. 

Mer information om funktionen för automatiskt öknings funktionen finns i [skala data flödes enheter automatiskt](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitioner
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Skala dataflödesenheter automatiskt](event-hubs-auto-inflate.md)
- [Översikt över Event Hubs tjänsten](./event-hubs-about.md)
