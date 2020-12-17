---
title: Övervaknings Azure Time Series Insights data referens | Microsoft Docs
description: Referens dokumentation för övervakning av Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632800"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Övervaknings Azure Time Series Insights data referens

Lär dig mer om data och resurser som samlats in av Azure Monitor från din Azure Time Series Insights-miljö. Mer information om hur du samlar in och analyserar övervaknings data finns i [övervaknings Time Series Insights]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Mått

I det här avsnittet visas alla automatiskt insamlade plattforms mått som samlas in för Azure Time Series Insights. En lista över alla Azure Monitor support mått (inklusive Azure Time Series Insights) finns i [Azure Monitor mått som stöds](../azure-monitor/platform/metrics-supported.md). Resurs leverantören för dessa mått är [Microsoft. TimeSeriesInsights/miljö/eventsources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) och [Microsoft. TimeSeriesInsights/miljöer](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Ingress
 
|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|IngressReceivedBytes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|
|IngressReceivedInvalidMessages|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|
|IngressReceivedMessages|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|
|IngressReceivedMessagesCountLag|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Genomsnitt|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|
|IngressReceivedMessagesTimeLag|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|
|IngressStoredBytes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|
|IngressStoredEvents|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|

### <a name="storage"></a>Storage

|Mått|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|
|---|---|---|---|---|
|WarmStorageMaxProperties|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|
|WarmStorageUsedProperties|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|

## <a name="resource-logs"></a>Resursloggar

I det här avsnittet visas de typer av resurs loggar som du kan samla in för din Azure Time Series Insightss miljö.

| Kategori | Visningsnamn | Beskrivning |
|----- |----- |----- |
| Ingress | TSIIngress | Kategorin Ingressn spårar fel som inträffar i ingångs pipeline. Den här kategorin innehåller fel som uppstår när händelser tas emot (till exempel fel vid anslutning till en händelse källa) och bearbetning av händelser (till exempel fel vid parsning av en händelse nytto Last). |

## <a name="schemas"></a>Scheman
Följande scheman används i Azure Time Series Insights

### <a name="tsiingress-table"></a>TSIIngress-tabell

| Egenskap | Beskrivning |
|----- |----- |
| TimeGenerated | Tid (UTC) som den här händelsen skapas i. |
| Plats | Platsen för resursen. |
| Kategori | Kategori för logg händelsen. |
| OperationName | Händelsens åtgärds namn. |
| CorrelationId | Korrelations-ID för begäran. |
| Nivå | Händelsens allvarlighets grad. |
| ResultDescription | Beskrivning av resultatet av åtgärden, till exempel "mottaget förbjudet fel". |
| Meddelande | Det meddelande som är associerat med felet. Innehåller information om vad som gått fel och hur du kan åtgärda felet. |
| Felkod | Koden som är associerad med felet |
| EventSourceType | Typ av händelse källa. Det kan antingen vara Event Hub eller IoT Hub. |
| EventSourceProperties | En samling egenskaper som är speciella för din händelse källa. Innehåller information som konsument gruppen och åtkomst nyckel namnet. |
