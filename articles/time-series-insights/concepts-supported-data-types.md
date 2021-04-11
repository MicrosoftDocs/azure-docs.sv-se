---
title: Data typer som stöds – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om de data typer som stöds i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 8e8e6f007e19d2514a5f75dbdda7da13e1f8be3c
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505186"
---
# <a name="supported-data-types"></a>Datatyper som stöds

I följande tabell visas de data typer som stöds av Azure Time Series Insights Gen2

| Datatyp | Beskrivning | Exempel | [Syntax för Time Series-uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Egenskaps kolumn namn i Parquet
|---|---|---|---|---|
| **boolesk** | En datatyp med ett av två tillstånd: `true` eller `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` eller `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Uttryckt i [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. Datetime-egenskaper lagras alltid i UTC-format. Tids zons förskjutningar, om det är korrekt formaterat, används och sedan värden som lagras i UTC. Se [det här](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) avsnittet för mer information om miljöns tidstämpel egenskap och datetime-förskjutningar | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Om "eventProcessedLocalTime" är tids stämpling för händelse källan: `$event.$ts` . Om det är en annan JSON-egenskap: `$event.eventProcessedLocalTime.DateTime` eller `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Ett tal med dubbel precision 64-bit  | `"value": 31.0482941` | `$event.value.Double` eller `$event['value'].Double` |  `value_double`
| **long** | Ett signerat 64-bitars heltal  | `"value" : 31` | `$event.value.Long` eller `$event['value'].Long` |  `value_long`
| **sträng** | Text värden måste bestå av giltig UTF-8. Null-och tomma strängar behandlas likadant. |  `"site": "DIM_MLGGG"`| `$event.site.String` eller `$event['site'].String`| `site_string`
| **dynamisk** | En komplex (icke-primitiv) typ som består av antingen en matris eller en egenskaps uppsättning (ord lista). För närvarande lagras bara JSON-matriser med primitiver eller matriser med objekt som inte innehåller något av de TS-ID: n eller Tidsstämpelns egenskaper som är dynamiska. Läs den här [artikeln](./concepts-json-flattening-escaping-rules.md) för att förstå hur objekt kommer att förenklas och matriser kan vara avregistrerade. Nytto Last egenskaper som lagras som den här typen är bara tillgängliga genom att välja `Explore Events` i Time Series Insights Explorer för att Visa obehandlade händelser eller via [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API: t för klient sidans tolkning. |  `"values": "[197, 194, 189, 188]"` | Det finns ännu inte stöd för att referera till dynamiska typer i ett Time Series-uttryck | `values_dynamic`

> [!NOTE]
> 64-bitars heltals värden stöds, men det största talet som Azure Time Series Insights Explorer kan Express är 9 007 199 254 740 991 (2 ^ 53-1) på grund av JavaScript-begränsningar. Om du arbetar med siffror i din data modell ovanför detta kan du minska storleken genom att skapa en [tids serie modell variabel](./concepts-variables.md#numeric-variables) och [konvertera](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) värdet.

> [!NOTE]
> **Sträng** typen kan inte ha värdet null:
>
> * Ett [Time Series-uttryck (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) uttryckt i en [Time Series-fråga](/rest/api/time-series-insights/reference-query-apis) som jämför värdet för en tom **sträng (') mot** **Null** fungerar på samma sätt: `$event.siteid.String = NULL` motsvarar `$event.siteid.String = ''` .
> * API: et kan returnera **Null** -värden även om ursprungliga händelser innehöll tomma strängar.
> * Ta inte beroende av **Null** -värden i **sträng** kolumner om du vill göra jämförelser eller utvärderingar, behandla dem på samma sätt som tomma strängar.

## <a name="sending-mixed-data-types"></a>Skicka blandade data typer

Din Azure Time Series Insights Gen2-miljö är starkt skriven. Om enheter eller Taggar skickar data av olika typer för en enhets egenskap lagras värdena i två separata kolumner och [sammanslagningen ()-funktionen](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) ska användas när du definierar variabel uttryck för tids serie modellen i API-anrop.

I Azure Time Series Insights Explorer kan du automatiskt slå samman separata kolumner i samma enhets egenskap. I exemplet nedan skickar sensorn en `PresentValue` egenskap som kan vara både lång eller dubbel. Om du vill fråga efter alla lagrade värden (oavsett datatyp) för `PresentValue` egenskapen väljer `PresentValue (Double | Long)` du och staplarna kommer att sammanslagna åt dig.

[![Automatisk sammanslagning i Utforskaren](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelse nytto Last. Kapslade objekt blir förenklade och matriser kan antingen lagras som `dynamic` eller utplattas för att producera flera händelser beroende på din miljö konfiguration och JSON-form. Läs mer om hur du kan läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Nästa steg

* Läs om hur du kommer att lagra händelser genom att läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md) .

* Förstå din miljös [data flödes begränsningar](./concepts-streaming-ingress-throughput-limits.md)

* Lär dig mer om [händelse källor](concepts-streaming-ingestion-event-sources.md) för att mata in strömmande data.
