---
title: Gen2 för JSON-förenkling och undantags regler – Azure Time Series Insights-| Microsoft Docs
description: Lär dig mer om JSON-förenkling, undantag och mat ris hantering i Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 9f768982e69f785c146f026040a91f7a63eef64c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673459"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON-utplattning, undantag och matrishantering

Din Azure Time Series Insights Gen2-miljö skapar dynamiskt kolumner i dina varma och kalla lager, efter en viss uppsättning namngivnings konventioner. När en händelse matas in används en uppsättning regler för JSON-nyttolasten och egenskaps namnen. Detta inkluderar undantag för vissa specialtecken och förenkling av kapslade JSON-objekt. Det är viktigt att känna till de här reglerna så att du förstår hur formen på din JSON påverkar hur dina händelser lagras och frågas. I tabellen nedan finns en fullständig lista över regler. Exempel på en & B visar också hur du effektivt kan gruppera flera tids serier i en matris.

> [!IMPORTANT]
>
> * Granska reglerna nedan innan du väljer en [Time Series ID-egenskap](./how-to-select-tsid.md) och/eller din händelse källans [tidstämpel](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). Om ditt TS-ID eller tidstämpel är inom ett kapslat objekt eller innehåller ett eller flera av specialtecknen nedan är det viktigt att du ser till att egenskaps namnet som du anger matchar kolumn namnet *när* inmatnings reglerna har tillämpats. Se exempel [B](concepts-json-flattening-escaping-rules.md#example-b) nedan.

| Regel | Exempel-JSON | [Syntax för Time Series-uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Egenskaps kolumn namn i Parquet
|---|---|---|---|
| Data typen Azure Time Series Insights Gen2 läggs till i slutet av kolumn namnet som "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| [Egenskapen timestamp](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) för händelse källan sparas i Azure Time Series Insights Gen2 som "timestamp" i Storage och värdet som lagras i UTC. Du kan anpassa tids stämplings egenskapen för händelse källan så att den uppfyller lösningens behov, men kolumn namnet i varmt och kallt lagrings utrymme är "timestamp". Andra datetime JSON-egenskaper som inte är tids stämpling för händelse källan sparas med "_datetime" i kolumn namnet, som anges i regeln ovan.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| JSON-egenskaps namn som innehåller specialtecknen. [\ och "föregås av [" och "]  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| I ["och"] finns det ytterligare undantag av enkla citat tecken och omvänt snedstreck. Ett enkelt citat sätt skrivs som \ och ett omvänt snedstreck skrivs som \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Kapslade JSON-objekt förenklas med en punkt som avgränsare. Det finns stöd för att kapsla upp till 10 nivåer. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long``$event['series']['value'].Long`eller`$event.series['value'].Long` |  `series.value_long` |
| Matriser med primitiva typer lagras som dynamisk typ |  ```"values": [154, 149, 147]``` | Dynamiska typer kan bara hämtas via [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) -API: et | `values_dynamic` |
| Matriser som innehåller objekt har två beteenden beroende på objektets innehåll: om något av de TS-ID: n eller timestamp-egenskaperna finns i objekten i en matris, kommer matrisen att avregistreras så att den inledande JSON-nyttolasten genererar flera händelser. På så sätt kan du gruppera flera händelser i en JSON-struktur. Alla toppnivå egenskaper som är peer-kopplade till matrisen kommer att sparas med varje objekt som inte har registrerats. Om dina TS-ID: n och tidstämpeln *inte* finns i matrisen, kommer de att sparas fullständigt som dynamisk typ. | Se exempel [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)och [C](concepts-json-flattening-escaping-rules.md#example-c) nedan
| Matriser som innehåller blandade element är inte förenklade. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Dynamiska typer kan bara hämtas via [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) -API: et | `values_dynamic` |
| 512 tecken är namn gränsen för JSON-egenskapen. Om namnet överskrider 512 tecken kommer det att trunkeras till 512 och ' _< ' hashkoden ' > ' läggs till. **Observera** att detta även gäller för egenskaps namn som har sammanfogats från utplattat objekt, vilket anger en kapslad objekt Sök väg. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Förstå det dubbla beteendet för matriser

Matriser med objekt lagras antingen hela eller delas upp i flera händelser beroende på hur du har utformat dina data. På så sätt kan du använda en matris för att gruppera händelser och undvika att upprepa egenskaper för telemetri som definieras på rot objekt nivån. Batching kan vara fördelaktigt eftersom det resulterar i färre Event Hubs eller IoT Hub meddelanden som skickas.

Men i vissa fall är matriser som innehåller objekt bara meningsfulla i sammanhanget för andra värden. Om du skapar flera händelser återges data meningslöst. För att säkerställa att en objekt mat ris lagras som en dynamisk typ följer du vägledningen för data modellering nedan och tar en titt på [exempel C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Så här vet du om min matris med objekt kommer att skapa flera händelser

Om en eller flera av dina Time Series-ID: n är inkapslade i objekt i en matris, *eller* om din händelse källans tidstämpel egenskap är kapslad, kommer inmatnings motorn att dela upp den för att skapa flera händelser. De egenskaps namn som du har angett för dina TS-ID: n och/eller tidsstämpeln bör följa förenklings reglerna ovan och kommer därför att indikera formen på din JSON. Se exemplen nedan och kolla in guiden om hur du [väljer en Time Series ID-egenskap.](./how-to-select-tsid.md)

### <a name="example-a"></a>Exempel A

Time Series-ID vid objekt roten och Tidsstämpelns kapslade \
**ID för miljö tids serie:**`"id"`\
**Tids stämpling för händelse Källa:**`"values.time"`\
**JSON-nytto last:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Resultat i Parquet-fil:**\
Konfigurationen och nytto lasten ovan kommer att producera tre kolumner och fyra händelser

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Exempel B

Sammansatt tids serie-ID med en egenskap kapslad \
**ID för miljö tids serie:** `"plantId"` särskilt `"telemetry.tagId"`\
**Tids stämpling för händelse Källa:**`"timestamp"`\
**JSON-nytto last:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Resultat i Parquet-fil:**\
Konfigurationen och nytto lasten ovan kommer att producera fyra kolumner och sex händelser

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  – 31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | – 30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Exempel C

Time Series-ID och tidsstämpel finns på objekt roten \
**ID för miljö tids serie:**`"id"`\
**Tids stämpling för händelse Källa:**`"timestamp"`\
**JSON-nytto last:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Resultat i Parquet-fil:**\
Konfigurationen och nytto lasten ovan kommer att producera tre kolumner och en händelse

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Nästa steg

* Förstå din miljös [data flödes begränsningar](./concepts-streaming-ingress-throughput-limits.md)
