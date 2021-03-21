---
title: Modell-variabler – Azure Time Series Insights Gen2 | Microsoft Docs
description: Modellvariabler
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: fd08dc98fa47617bbc7c8d1fff895377837a7327
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736742"
---
# <a name="time-series-model-variables"></a>Variabler för tids serie modell

I den här artikeln beskrivs tids serie modellens variabler som anger formel-och beräknings regler för händelser.

Varje variabel kan vara en av tre typer: *numeric*, *kategoriska* och *aggregation*.

* **Numeriska** typer fungerar med kontinuerliga numeriska värden.
* **Kategoriska** -typer fungerar med en definierad uppsättning diskreta värden.
* **Aggregerade** typer kombinerar flera variabler av samma typ (antingen alla numeriska eller alla kategoriska).

I följande tabell visas vilka egenskaper som är relevanta för varje variabel typ.

[![Tids serie modell variabel tabell](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numeriska variabler

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabel filter | Filter är valfria villkorliga satser för att begränsa antalet rader som ska beaktas för beräkning. |
| Variabel värde | Telemetri värden som används för beräkning som kommer från enheten eller sensorer eller omvandlas med hjälp av Time Series-uttryck. Variabler av typen numerisk typ måste vara av typen *Double*.|
| Variabel interpolation | Interpolation anger hur du återskapar en signal med hjälp av befintliga data. Alternativen för *steg* och *linjär* interpolation är tillgängliga för numeriska variabler. |
| Variabel agg regering | Utför beräkningar genom de [agg regerings funktioner som stöds för numeriska variabel typer](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Variabler följer följande JSON-exempel:

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

## <a name="categorical-variables"></a>Kategoriska-variabler

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabel filter | Filter är valfria villkorliga satser för att begränsa antalet rader som ska beaktas för beräkning. |
| Variabel värde | Telemetridata som används för beräkning som kommer från enheten eller sensorer. Kategoriska-variabler måste vara antingen *långa* eller *strängar*. |
| Variabel interpolation | Interpolation anger hur du återskapar en signal med hjälp av befintliga data. Alternativet *Step* -interpolation är tillgängligt för kategoriska-variabler. |
| Variabel kategorier | Kategorier skapar en mappning mellan värdena som kommer från enheten eller sensorer till en etikett. |
| Variabel standard kategori | Standard kategorin är för alla värden som inte mappas i egenskapen "Categories". |

Variabler följer följande JSON-exempel:

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

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabel filter | Filter är valfria villkorliga satser för att begränsa antalet rader som ska beaktas för beräkning. |
| Variabel agg regering | Utför beräkningar genom de [agg regerings funktioner som stöds för mängd varierande typer](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

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

Variabler lagras i typ definitionen för en tids serie modell och kan anges infogade via API: er för att åsidosätta eller komplettera den lagrade definitionen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [tids serie modell](./concepts-model-overview.md).

* Läs mer om hur du definierar variabler i linje med [API: er för frågor](./concepts-query-overview.md).
