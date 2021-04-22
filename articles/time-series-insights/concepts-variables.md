---
title: Modellvariabler – Azure Time Series Insights Gen2-| Microsoft Docs
description: Modellvariabler
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872973"
---
# <a name="time-series-model-variables"></a>Time Series Model-variabler

I den här artikeln beskrivs variablerna för Tidsseriemodell som anger formler och beräkningsregler för händelser.

Varje variabel kan vara en av tre typer: *numerisk,* *kategorisk* och *aggregera*.

* **Numeriska** typer fungerar med kontinuerliga numeriska värden.
* **Kategoriska typer** fungerar med en definierad uppsättning diskreta värden.
* **Mängdsorter** kombinerar flera variabler av en enda typ (antingen alla numeriska eller alla kategoriska).

I följande tabell visas vilka egenskaper som är relevanta för varje variabel.

[![Tabell för tidsseriemodellvariabler](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numeriska variabler

| Variabelegenskap | Description |
| --- | ---|
| Variabelfilter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabelvärde | Telemetrivärden som används för beräkning kommer från enheten eller sensorerna eller transformeras med hjälp av Time Series-uttryck. Numeriska kindvariabler måste vara `Double` antingen `Long` eller för att matcha datatypen för inkommande data.|
| Variabelinterpolering | Interpolering anger hur en signal rekonstrueras med hjälp av befintliga data. *Alternativ* för *steg-* och linjär interpolering är tillgängliga för numeriska variabler. |
| Variabel aggregering | Utför beräkningar via [aggregeringsfunktioner som stöds för numeriska variabelsorter](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Variablerna följer följande JSON-exempel:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Kategoriska variabler

| Variabelegenskap | Description |
| --- | ---|
| Variabelfilter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabelvärde | Telemetrivärden som används för beräkning kommer från enheten eller sensorerna. Kategoriska kindvariabler måste vara antingen `Long` eller för att matcha `String` datatypen för inkommande data. |
| Variabelinterpolering | Interpolering anger hur en signal rekonstrueras med hjälp av befintliga data. Alternativet  Steginterpolering är tillgängligt för kategoriska variabler. |
| Variabelkategorier | Kategorier skapar en mappning mellan de värden som kommer från enheten eller sensorer till en etikett. |
| Standardkategori för variabel | Standardkategorin är för alla värden som inte mappas i egenskapen "categories". |

Variablerna följer följande JSON-exempel:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Aggregera variabler

| Variabelegenskap | Description |
| --- | ---|
| Variabelfilter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabel aggregering | Utför beräkningar via de aggregeringsfunktioner [som stöds för Mängdvariabel-typer](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Variabler följer följande JSON-exempel:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Variabler lagras i typdefinitionen för en tidsseriemodell och kan anges infogade via API:er för att åsidosätta eller komplettera den lagrade definitionen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Time Series Model](./concepts-model-overview.md).

* Läs mer om hur du definierar infogade variabler med [fråge-API:er.](./concepts-query-overview.md)
