---
title: Tids parametrar för Azure Monitor arbets böcker
description: Lär dig hur du ställer in tids parametrar så att användarna kan ställa in tids kontexten för analyser. Tids parametrarna används av nästan alla rapporter.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717170"
---
# <a name="workbook-time-parameters"></a>Tids parametrar för arbets bok

Med tids parametrar kan användarna ange tids kontexten för analysen och det används av nästan alla rapporter. Det är relativt enkelt att konfigurera och använda – så att författarna kan ange de tidsintervall som ska visas i list rutan, inklusive alternativet för anpassade tidsintervall. 

## <a name="creating-a-time-parameter"></a>Skapa en tids parameter
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `TimeRange`
    2. Parameter typ: `Time range picker`
    3. Kunna `checked`
    4. Tillgängliga tidsintervall: senaste timmen, de senaste 12 timmarna, de senaste 24 timmarna, de senaste 48 timmarna, de senaste 3 dagarna, senaste 7 dagarna och alternativet Tillåt anpassat tidsintervall
5. Skapa parametern genom att välja Spara i verktygsfältet.

    ![Bild som visar skapandet av en tids intervalls parameter](./media/workbooks-time/time-settings.png)

Så här kommer arbets boken att se ut i Read-mode.

![Bild som visar en tids intervall parameter i läsläge](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referera till en tids parameter
### <a name="via-bindings"></a>Via bindningar
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. De flesta arbets boks kontroller stöder en intervall väljare för _tidsintervall_ . Öppna List rutan tidsintervall och välj i gruppen _Tidsintervalls_ `{TimeRange}` parametrar längst ned.
3. Detta binder tids intervall parametern till tidsintervallet för diagrammet. Tidsintervallet för exempel frågan är nu de senaste 24 timmarna.
4. Kör fråga för att visa resultaten

    ![Bild som visar en tidsintervalls parameter som refereras via bindningar](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>I KQL
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. I KQL anger du ett tids omfattnings filter med hjälp av parametern: `| where timestamp {TimeRange}`
3. Detta utökar tiden för utvärdering av fråga till `| where timestamp > ago(1d)` , vilket är tids intervall svärdet för parametern.
4. Kör fråga för att visa resultaten

    ![Bild som visar ett tidsintervall som refereras i KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>I text 
1. Lägg till en text kontroll i arbets boken.
2. I markdown anger du `The chosen time range is {TimeRange:label}`
3. Välj _klar redigering_
4. Text kontrollen visar text: _det valda tidsintervallet är de senaste 24 timmarna_

## <a name="time-parameter-options"></a>Alternativ för tids parameter
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Intervall etikett | Senaste 24 timmarna |
| `{TimeRange:label}` | Intervall etikett | Senaste 24 timmarna |
| `{TimeRange:value}` | Tids intervalls värde | > sedan (1d) |
| `{TimeRange:query}` | Fråga för tidsintervall | > sedan (1d) |
| `{TimeRange:start}` | Start tid för tidsintervall | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | Slut tid för tidsintervall | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | Kornigt tidsintervall | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Använda parameter alternativ i en fråga
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Nästa steg

* [Kom igång](./workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](./workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.