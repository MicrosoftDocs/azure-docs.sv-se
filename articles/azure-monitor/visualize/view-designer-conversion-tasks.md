---
title: Azure Monitor Visa designer till arbets böcker konvertering av vanliga uppgifter
description: Vanliga uppgifter vid över gång från vyer till arbets böcker i Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 4e9d9e65dedae083138f3b29a2a609e124ce0fc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624725"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Visa designer till arbets böcker konvertering av vanliga uppgifter
[View Designer](view-designer.md) är en funktion i Azure Monitor som gör att du kan skapa anpassade vyer som hjälper dig att visualisera data i arbets ytan Log Analytics, med diagram, listor och tids linjer. De fasas ut och ersätts med arbets böcker som tillhandahåller ytterligare funktioner. Den här artikeln beskriver aktiviteter som är vanliga i att konvertera vyer till arbets böcker.


## <a name="quickstart-with-preset-view-designer-templates"></a>Snabb start med förinställd Visa designer-mallar

Arbets böcker i Log Analytics arbets ytor har redan mallar gjorda för att matcha vissa vyer i View Designer. Under kategorin **View Designer-guider** väljer du **Visa designer över gångs guide** för att lära dig om dina alternativ eller väljer en av de förinställda mallarna.

![Exempel på mallar](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Aktiverar tids intervalls filter
View Designer har ett inbyggt standard tids intervall filter, men i arbets böcker är den här inställningen inte aktive rad som standard. Med arbets böcker kan användarna skapa egna tids intervalls filter som kan vara mer relevanta för sina data loggar. Stegen för att generera filtret visas nedan:

Välj alternativet **Lägg till parametrar** . Standard **formatet** är inställt på *Pills*.

![Lägg till param](media/view-designer-conversion-tasks/add-param.png)

 Välj knappen **Lägg till parameter** .

![Lägg till parameter](media/view-designer-conversion-tasks/add-parameter.png)

I text rutan **parameter namn** på menyn marginal List skriver du *TimeRange*. Ange **parameter typ** som *intervall väljare*. Markera kryss rutan **krävs?** .

![Parameter meny](media/view-designer-conversion-tasks/parameter-menu.png)

Spara parametern i det övre vänstra hörnet på menyn i sido panelen. Du kan lämna List *rutan som avmarkerad som standard* eller välja ett standardvärde **TimeRange** -värde, till exempel *24 timmar*. Välj **klar redigering**.

Parametrar kan användas i frågor genom att lägga till klammerparenteser {} runt parameter namnet. Mer information om parametrar finns i [arbets bokens dokumentation om parametrar](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Uppdatera frågor med parametern TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Alternativ 1: Välj TimeRange från List rutan tidsintervall

![Tids parameter](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Alternativ 2: uppdatera dina logg frågor

Lägg till raden i frågan: `| where TimeGenerated {TimeRange}` som i följande exempel:

Ursprunglig fråga
```KQL
search * 
| summarize count() by Type
```

Uppdaterad fråga
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Inklusive en lista
De flesta vyer i vyn designer innehåller en lista och du kan återskapa den här standard listan i en arbets bok.

![Panel lista](media/view-designer-conversion-tasks/tile-list.png)

Lägg till en visualisering genom att klicka på **Lägg till fråga** från cell alternativen.

![Lägg till param](media/view-designer-conversion-tasks/add-param.png)

Visnings verktyget använder en standard fråga som matchar syntaxen från det ursprungliga exemplet. Detta kan uppdateras genom att ändra frågan till det uppdaterade formuläret som i följande exempel:

Ursprunglig fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Uppdaterad fråga
```KQL
search * 
| summarize Count = count() by Type
```

Då skapas en lista som ser ut ungefär så här:

![Lista exempel](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Aktivera miniatyr diagram
En vanlig funktion för rutnät är att lägga till miniatyr diagram för att sammanfatta olika data mönster över tid. View Designer erbjuder funktionen **Aktivera miniatyr diagram** för alla listor, precis som arbets böcker. Om du vill lägga till miniatyr diagram i dina data som matchar Visa designer, kan du koppla data till den ursprungliga frågan som i följande exempel:

Ursprunglig fråga
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Uppdaterad fråga
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Välj **kolumn inställningar**.
![Kolumn inställningar](media/view-designer-conversion-tasks/column-settings.png)

Uppdatera List rutan **kolumn åter givning** så att den är ett *Spark-fält*.
![Miniatyr diagram](media/view-designer-conversion-tasks/sparkline.png)

Spara inställningarna och kör frågan igen för att uppdatera tabellen så att den innehåller ett miniatyr diagram.

Det resulterande rutnätet ser ut ungefär så här: ![ exempel på miniatyr diagram](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Avancerade cell inställningar
Om du vill spegla View Designer kan du utföra uppgifter som att ändra storleken på arbets boks cellerna eller lägga till PIN-filer och externa länkar till loggar.

Välj kugg hjuls ikonen längst ned i varje cell för att få åtkomst till **Avancerade inställningar** .

![Avancerade inställningar](media/view-designer-conversion-tasks/advanced-settings.png)

Då visas en meny med olika alternativ:

![Inställningar för avancerade inställningar](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Markera motsvarande kryss rutor om du vill lägga till en PIN-kod och en länk till en extern fråga. Om du vill lägga till en rubrik i cellen skriver du önskad rubrik i avsnittet **diagram rubrik** .

Som standard är alla arbets boks celler inställda på att ta hela sidans bredd, men du kan justera det genom att skala cellen nedåt under fliken **stil** på menyn **Avancerade inställningar**

![Format för avancerade inställningar](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Ytterligare parametrar
Välj **Lägg till parameter** för att skapa en ny parameter i din arbets bok. 

Om du vill välja en prenumeration skriver du *prenumeration* i fältet **parameter namn** på menyn på sidan och väljer *prenumerations väljare* i list rutan **parameter typ**

![Menyn prenumeration](media/view-designer-conversion-tasks/subscription-filter.png)

Om du vill välja en resurs skriver du *resurs* i fältet **parameter namn** på menyn på sidan och väljer *resurs väljare* i list rutan **parameter typ** .

![Resurs meny](media/view-designer-conversion-tasks/resource-filter.png)

Detta lägger till List rutor så att du kan komma åt dina olika prenumerationer och resurser.

![List rutan prenumerations resurs](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Nästa steg
- [Panelkonverteringar](view-designer-conversion-tiles.md)
