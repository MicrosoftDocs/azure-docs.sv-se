---
title: ta med fil
description: ta med fil
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025786"
---
Följande sammanfattar viktiga gränser i Azure Time Series Insights gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Pris nivåer och kapaciteter för SKU

Ingångs priser och kapaciteter för S1 och S2 SKU ger flexibilitet när du konfigurerar en ny Azure Time Series Insightss miljö. SKU-kapaciteten anger din dagliga ingångs taxa baserat på antalet händelser eller byte som lagras, beroende på vilket som kommer först. Observera att inkommande trafik mäts *per minut* och att **begränsningen** tillämpas med hjälp av token-Bucket. Ingress mäts i block om 1 KB. Till exempel skulle en faktisk 0,8-KB-händelse mätas som en händelse, och en händelse på 2,6 KB räknas som tre händelser.

| S1 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 1 GB (1 000 000 händelser) per dag | 30 GB (30 000 000 händelser) per månad |
| 10 | 10 GB (10 000 000 händelser) per dag | 300 GB (300 000 000 händelser) per månad |

| S2 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 10 GB (10 000 000 händelser) per dag | 300 GB (300 000 000 händelser) per månad |
| 10 | 100 GB (100 000 000 händelser) per dag | 3 TB (3 000 000 000 händelser) per månad |

> [!NOTE]
> Kapaciteter skalas linjärt, så en S1-SKU med kapacitet 2 stöder 2 2 000 000 GB händelser per dag och 60 GB (60 000 000-händelser) per månad.

S2 SKU-miljöer stöder betydligt fler händelser per månad och har en mycket högre ingångs kapacitet.

| SKU  | Antal händelser per månad  | Antal händelser per minut | Händelse storlek per minut  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000   |  720    |  720 KB   |
 |S2     |   300 000 000   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Egenskaps gränser

Gen1 egenskaps gränser beror på den SKU-miljö som är markerad. Angivna händelse egenskaper har motsvarande JSON-, CSV-och diagram kolumner som kan visas i [Azure Time Series Insights Explorer](../articles/time-series-insights/time-series-quickstart.md).

| SKU | Maximalt antal egenskaper |
| --- | --- |
| S1 | 600 egenskaper (kolumner) |
| S2 | 800 egenskaper (kolumner) |

### <a name="event-sources"></a>Händelsekällor

Högst två händelse källor stöds per instans.

* Lär dig hur du [lägger till en Event Hub-källa](../articles/time-series-insights/how-to-ingest-data-event-hub.md).
* Konfigurera [en IoT Hub-källa](../articles/time-series-insights/how-to-ingest-data-iot-hub.md).

### <a name="api-limits"></a>API-gränser

REST API gränser för Azure Time Series Insights gen1 anges i [REST API referens dokumentation](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).