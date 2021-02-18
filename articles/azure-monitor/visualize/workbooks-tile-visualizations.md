---
title: Azure Monitor arbets boks panels visualiseringar
description: Lär dig mer om alla visualiseringar i Azure Monitor arbets bok panelen.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622817"
---
# <a name="tile-visualizations"></a>Panel visualiseringar

Paneler är ett bra sätt att Visa sammanfattnings data i arbets böcker. Bilden nedan visar ett vanligt användnings fall av paneler med sammanfattning på program nivå ovanpå ett detaljerat rutnät.

[![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Stöd för arbets boks paneler visar en rubrik, under rubrik, stor text, ikoner, metriska övertoningar, Spark-linje/staplar, sidfot osv.

## <a name="adding-a-tile"></a>Lägga till en panel

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Välj **Lägg till** och *Lägg till fråga* för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Ange **fullständig** storlek.
6. Ange visualiseringen som **paneler**.
7. Välj knappen **panel inställningar** för att öppna fönstret inställningar.
    1. I *rubrik*, ange:
        * Använd kolumn: `name` .
    2. I *vänster* anger du:
        * Använd kolumn: `Requests` .
        * Kolumn åter givning: `Big Number` .
        * Färgpalett: `Green to Red`
        * Minsta värde: `0` .
    3. I *längst ned* anger du:
        * Använd kolumn: `appName` .
8. Välj knappen **Spara och Stäng** längst ned i fönstret.

[![Skärm bild som visar Sammanfattning av panel med inställningar för fråga och panel.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Panelerna i Read-läge:

[![Skärm bild av vyn Sammanfattning av panel i Läs läge.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Spark-linjer i paneler

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Lägg till en tidsintervalls parameter med namnet `TimeRange` .
    1. Välj **Lägg till** och *Lägg sedan till parametrar*.
    2. I parameter kontrollen väljer du **Lägg till parameter**.
    3. Ange `TimeRange` i fältet *parameter namn* och välj `Time range picker` för *parameter typ*.
    4. Välj **Spara** längst upp i fönstret och välj sedan **klar redigering** i parameter kontrollen.
3. Välj **Lägg till** och *Lägg till fråga* för att lägga till en logg frågas kontroll under parameter kontrollen.
4. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
5. Använd Frågeredigeraren för att ange KQL för din analys.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Välj **Kör fråga**. (Se till att ange `TimeRange` ett värde som du väljer innan du kör frågan.)
7. Ange *visualiseringen* till "paneler".
8. Ange "fullständig" som *storlek* .
9. Välj **panel inställningar**.
    1. I *panelen* anger du:
        * Använd kolumn: `name` .
    2. I under *panel*, ange:
        *  Använd kolumn: `appNAme` .
    3. I *vänster* anger du:
        *  Använd kolumn: `Requests` .
        * Kolumn åter givning: `Big Number` .
        * Färgpalett: `Green to Red` .
        * Minsta värde: `0` .
    4. I *längst ned* anger du:
        * Använd kolumn: `Trend` .
        * Kolumn åter givning: `Spark line` .
        * Färgpalett: `Green to Red` .
        * Minsta värde: `0` .
10. Välj **Spara och Stäng** längst ned i fönstret.

![Skärm bild av panel visualisering med en spark-linje](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Panel storlekar

Författaren har ett alternativ för att ställa in panel bredden i panel inställningarna.

* `fixed` objekt

    Standard beteendet för paneler är att ha samma fast bredd, ungefär. 160 bild punkter brett, plus utrymmet runt panelerna.

    ![Skärm bild som visar paneler med fast bredd](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Varje rubrik kommer att krympa eller växa för att passa sitt innehåll. panelerna är dock begränsade till bredden på brickornas vy (ingen vågrät rullning).

    ![Skärm bild som visar paneler för automatisk bredd](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Varje rubrik är alltid hela bredden för brickornas vy, en rubrik per rad.

     ![Skärm bild som visar rutor med full storleks bredd](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Nästa steg

* Paneler stöder också sammansatt bar åter givning. Mer information finns i [dokumentationen för Composite bar bar](workbooks-composite-bar.md).
* Om du vill veta mer om tids parametrar som `TimeRange` finns i [dokumentationen för arbets boks tids parametrar](workbooks-time.md).