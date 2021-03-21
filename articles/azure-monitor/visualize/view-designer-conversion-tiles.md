---
title: Panel konverteringar Azure Monitor Visa designer till arbets böcker
description: Information om hur du konverterar paneler till arbets böcker vid över gången från vyer i Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043345"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor Visa designer panels konverteringar
[View Designer](view-designer.md) är en funktion i Azure Monitor som gör att du kan skapa anpassade vyer som hjälper dig att visualisera data i arbets ytan Log Analytics, med diagram, listor och tids linjer. De fasas ut och ersätts med arbets böcker som tillhandahåller ytterligare funktioner. Den här artikeln innehåller information om hur du konverterar olika paneler till arbets böcker.

## <a name="donut--list-tile"></a>Ring & List panel

![Ring lista](media/view-designer-conversion-tiles/donut-list.png)

Att återskapa ring & List panelen i arbets böcker omfattar två separata visualiseringar. Det finns två alternativ för ring delen.
För båda början genom att välja **Lägg till fråga** och klistra in den ursprungliga frågan från View Designer i cellen.

**Alternativ 1:** Välj **cirkel diagram** i list rutan **visualisering** : ![ visualiserings meny för cirkel diagram](media/view-designer-conversion-tiles/pie-chart.png)

**Alternativ 2:** Välj **Ange efter fråga** i list rutan **visualisering** och Lägg till `| render piechart` i frågan:

 ![Visualiserings meny](media/view-designer-conversion-tiles/set-by-query.png)

**Exempel**

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Uppdaterad fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Information om hur du skapar en lista och aktiverar miniatyr diagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Nedan visas ett exempel på hur ring & List panelen kan tolkas om i arbets böcker:

![Arbets böcker för ring lista](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Linje diagram & List panel
![Linje diagram lista](media/view-designer-conversion-tiles/line-list.png) 

För att återskapa linje diagram delen uppdaterar du frågan på följande sätt:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Uppdaterad fråga
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Det finns två alternativ för att visualisera linje diagrammet

**Alternativ 1:** Välj **linje diagram** i list rutan **visualisering** :
 
 ![Linje Diagram-menyn](media/view-designer-conversion-tiles/line-visualization.png)

**Alternativ 2:** Välj **Ange efter fråga** i list rutan **visualisering** och Lägg till `| render linechart` i frågan:

 ![Visualiserings meny](media/view-designer-conversion-tiles/set-by-query.png)

**Exempel**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Information om hur du skapar en lista och aktiverar miniatyr diagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Följande är ett exempel på hur linje diagrammet & List panelen kan tolkas om i arbets böcker:

![Linje diagram lista arbets böcker](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>List panelen nummer &

 ![Panel lista](media/view-designer-conversion-tiles/tile-list-example.png)

Uppdatera frågan på panelen tal enligt följande:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Uppdaterad fråga
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Ändra List rutan visualisering till **paneler** och välj sedan **panel inställningar**.
 ![Panel visualisering](media/view-designer-conversion-tiles/tile-visualization.png)

Lämna avsnittet **title** tomt och välj **vänster**. Ändra värdet för **use-kolumnen:** to **Count** och **Column rendering** till **Big Number**:

![Panel inställningar](media/view-designer-conversion-tiles/tile-settings.png)

 
Information om hur du skapar en lista och aktiverar miniatyr diagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Följande är ett exempel på hur List panelen för nummer & kan tolkas om i arbets böcker:

![Lista med antal arbets böcker](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Tidslinje och lista

 ![Tids linje lista](media/view-designer-conversion-tiles/time-list.png)

Uppdatera frågan på tids linjen enligt följande:

Ursprunglig fråga
```KQL
search * 
| sort by TimeGenerated desc
```

Uppdaterad fråga
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Det finns två alternativ för att visualisera frågan som ett stapeldiagram:

**Alternativ 1:** Välj **stapeldiagram** i list rutan **visualisering** : ![ Barchart visualisering](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Alternativ 2:** Välj **Ange efter fråga** i list rutan **visualisering** och Lägg till `| render barchart` i frågan:

 ![Visualiserings meny](media/view-designer-conversion-tiles/set-by-query.png)

 
Information om hur du skapar en lista och aktiverar miniatyr diagram finns i artikeln om [vanliga uppgifter](view-designer-conversion-tasks.md).

Följande är ett exempel på hur tids linje & List panelen kan tolkas om i arbets böcker:

![Arbets tids böcker-lista](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över över gången Visa designer till arbets böcker](view-designer-conversion-overview.md)
