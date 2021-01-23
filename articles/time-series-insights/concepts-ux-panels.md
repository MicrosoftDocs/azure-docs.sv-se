---
title: Visualisera data i Time Series Insights Explorer-Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om funktioner och alternativ som är tillgängliga i Azure Time Series Insights Explorer.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 55e102cc7e27cbcd9a65999f590c8264f1ad4434
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736860"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights-utforskaren

I den här artikeln beskrivs de olika funktionerna och alternativen som är tillgängliga i Azure Time Series Insights Gen2 [demo Environment](https://insights.timeseries.azure.com/preview/demo).

## <a name="prerequisites"></a>Krav

För att komma igång med Azure Time Series Insights Explorer måste du:

* Ha en Azure Time Series Insights Gen2-miljö etablerad. Läs mer om hur du konfigurerar en instans genom att läsa självstudien om [Azure Time Series Insights Gen2](./tutorials-set-up-tsi-environment.md) .
* [Ge data åtkomst](./concepts-access-policies.md) till den Azure Time Series Insights Gen2-miljö som du skapade för kontot. Du kan ge åtkomst till andra och dig själv.
* Lägg till en händelse källa i Azure Time Series Insights Gen2-miljön för att skicka data till miljön:
  * Lär dig [hur du ansluter till en Event Hub](./how-to-ingest-data-event-hub.md)
  * Lär dig [hur du ansluter till en IoT-hubb](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Utforska Azure Time Series Insights Explorer

Azure Time Series Insights Explorer består av följande sju element:

[![Översikt över Azure Time Series Insights Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Miljö panel](#1-environment-panel): visar alla dina Azure Time Series Insights Gen2-miljöer.
1. [Navigerings fält](#2-navigation-bar): låter dig växla mellan sidorna **analysera** och **modeller** .
1. [Hierarkiträdet och Sök panelen](#3-hierarchy-tree-and-search-panel): låter dig välja och söka efter vissa data element som ska visas i diagrammet.
1. [Tids serie källa](#4-time-series-well): visar alla dina markerade data element.
1. [Diagram panel](#5-chart-panel): visar det aktuella arbets diagrammet.
1. [Tids linje](#6-time-editor-panel): gör att du kan ändra din arbets tids period.
1. [App-fältet](#7-app-bar): innehåller alternativ för användar hantering (till exempel aktuell klient) och gör att du kan ändra dem och språk inställningar.

## <a name="1-environment-panel"></a>1. miljö panel

I miljö panelen visas alla Azure Time Series Insights Gen2-miljöer som du har åtkomst till. Listan innehåller Gen2-miljöer och gen1-miljöer. Du behöver bara välja den miljö som du vill använda så att den omedelbart tas där.

1. Välj den nedrullningsbara pilen bredvid den miljö som visas.

   [![Miljö panel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Välj sedan den miljö som du vill använda.

## <a name="2-navigation-bar"></a>2. navigerings fält

  [![Navigerings fältet](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Använd navigerings fältet för att välja mellan två vyer:

* **Analysera**: Använd det för att rita och utföra omfattande analyser på dina modellerade eller avmodellerade tids serie data.
* **Modell**: Använd den för att skicka nya Azure Time Series Insights Gen2 typer, hierarkier och instanser till din tids serie modell.

### <a name="model-authoring"></a>Modell redigering

Azure Time Series Insights-Gen2 har stöd för åtgärderna för att skapa, läsa, uppdatera och ta bort (CRUD) i din tids serie modell.

[![Panelen för modells ökning](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tids serie modell typ**: du kan använda modell typer för tids serier för att definiera variabler eller formler för att utföra beräkningar. De är kopplade till en specifik tids serie modell instans. En typ kan ha en eller flera variabler.
* **Hierarki för tids serie modell**: hierarkier är systematiska organisationer av dina data. Hierarkier illustrerar relationerna mellan olika instanser i din tids serie modell.
* **Tids serie modell instans**: instanser är själva tids serien. I de flesta fall är de de som är **DeviceID** eller **AssetID**, som är den unika identifieraren för till gången i miljön.

Om du vill veta mer om tids serie modellen kan du läsa [modeller](./concepts-model-overview.md)för tids serier.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. hierarkiträdet och Sök panelen

I hierarkiträdet och Sök panelen kan du enkelt söka efter och navigera i [tids serie modellens](./concepts-model-overview.md) hierarki för att hitta de tids serie instanser som du vill visa i diagrammet. När du väljer dina instanser, läggs de inte bara till i det aktuella diagrammet, men de läggs också till i data källan.

[![Hierarkiträdet och Sök panelen](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

I rutan Sök resultat kan du också visa resultaten i en hierarkisk vy eller en listvy som gör det enkelt att hitta de instanser som du vill visa.

## <a name="4-time-series-well"></a>4. tids serie källa

Visar instans fälten och andra metadata som är kopplade till valda tids serie modell instanser. Genom att markera kryss rutorna på höger sida kan du dölja eller visa vissa instanser från det aktuella diagrammet.

  [![Gen2-källa](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Du kan ta bort vissa data element från dina aktuella data på ett bra sätt genom att välja den röda **borttagningen** (pappers korgen) på vänster sida av elementet. Med det här alternativet kan du också styra hur varje element visas i diagrammet. Du kan välja att lägga till min/högsta skuggor, data punkter, flytta elementet i tid och visualisera instansen på ett stegat sätt.

Dessutom kan du med utforsknings kontrollen skapa tids Skift och punkt diagram enkelt.  

  [![Alternativ för bra layout](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Om följande meddelande visas har instansen inga data under den valda tids perioden. Du löser problemet genom att öka tidsintervallet eller bekräfta att instansen skickar data.
>
> ![Ingen data avisering](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. diagram panel

I diagrammet kan du Visa Time Series-instanser som rader. Du kan minimera kontroll panelen för miljö panelen, data modellen och tids rymden genom att klicka på webb kontrollerna för att göra diagrammet större.

  [![Översikt över Gen2-diagram](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagram typ**: styr vilka data element som är tillgängliga för visualisering.

1. **Intervall storlek**: med skjutreglaget för intervall storlek kan du zooma in och ut ur intervall över samma tids period. Detta ger en mer exakt kontroll av rörelsen mellan stora sektorer av tiden som visar jämna trender nedåt till sektorer som är så små som i millisekunden, så att du kan granska detaljerade och högupplösta klipp av dina data. Skjutreglagets standard start punkt anges som den mest optimala vyn av data från ditt val. matchning av matchning, frågans hastighet och granularitet.

1. **Zooma och panorera**: Välj den här kontrollen för att zooma och panorera diagrammet.

1. **Kontroll av y-axel**: växlar mellan tillgängliga visnings alternativ för y-axeln:

    * `Stacked`: Varje rad har en enskild Y-axel.
    * `Overlap`: Använd det för att stapla flera rader på samma Y-axel, och data som ändras baserat på den valda linjen.
    * `Shared`: Alla Y-axelns data visas tillsammans.

1. **Markör element**: det data element som är markerat och dess associerade information.

Du kan öka detalj nivån i en viss data sektor genom **att vänsterklicka** på en data punkt i det aktuella diagrammet och hålla ned musen och sedan dra det valda fältet till den önskade slut punkten. **Högerklicka på** det blå, valda avsnittet och välj sedan **Zooma** enligt bilden nedan. Du kan också visa och hämta telemetri-händelserna i det valda tidsintervallet.

  [![Gen2 diagram-zoomning](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

När du har gjort en **zoom** -åtgärd visas den valda data uppsättningen. Välj format kontrollen för att gå igenom de tre befintliga y-axlarna för dina data.

  [![Y-axel i Gen2-diagrammet](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Här följer ett exempel på ett **överlappande diagram** :

  [![Överlappande diagram alternativ](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Knappen **fler åtgärder** expanderar för att visa **hämtningen som CSV**, **ansluta till Power BI**, **Visa diagram data som en tabell** och utforska alternativ för **RAW-händelser** .

  [![Alternativ för fler åtgärder](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Läs mer om alternativet **Anslut till Power BI** i [Power BI Connector](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. tids redigerings panel

När du arbetar med Azure Time Series Insights Gen2 väljer du först ett tidsintervall. Det valda tidsintervallet styr den data uppsättning som är tillgänglig för manipulering med Azure Time Series Insights Gen2 Update-widgetar.

  [![Panelen för val av tid](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> En del av tids linjen är markerad i en gul eller orange färg för att visa vilken mängd data som är tillgängliga i varmt lager.

Följande webb kontroller är tillgängliga i Azure Time Series Insights Gen2 för att välja din arbets tids period.

  [![Bra kontroll för undersökning](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Skjutreglage för ursprungligt datum intervall**: Använd de två slut punkts kontrollerna genom att dra dem under den tids period som du vill ha. Det här interna datum intervallet begränsas av skjutreglaget för yttre datum intervall.

1. **Öka och minska datum intervall knappar**: öka eller minska din tids period genom att välja någon av knapparna för det intervall du vill ha.

1. **Tids period, Dölj kontroll**: med den här webb kontrollen kan du dölja alla kontroller förutom det inre datum intervallets skjutreglage.

1. **Skjutreglage för yttre datum intervall**: Använd slut punkts kontrollerna för att välja det yttre datum intervallet, som är tillgängligt för den interna datum intervalls kontrollen.

1. **Skjutreglage för tidsintervall**: Använd den för att snabbt växla mellan förinställda tids markeringar, till exempel de senaste **30 minuterna**, de **senaste 12 timmarna** eller ett **anpassat intervall**. Om du ändrar det här värdet ändras även de tillgängliga intervall intervallen som diskuteras i skjutreglaget för intervall storlek.

   [![Till och från markerings panelen](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. app-fältet

Navigerings panelen för Azure Time Series Insights Gen2 visas överst i appen. Det innehåller följande funktioner:

### <a name="current-session-share-link-control"></a>Aktuell länk kontroll för sessions-resurs

  [![Delningsikon](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Välj den nya **delnings** ikonen för att dela en URL-länk med ditt team.

  [![Dela din instans-URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Avsnittet klient

  [![Val av klient](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Visar din aktuella Azure Time Series Insights Gen2 information om inloggnings konto.
* Använd den för att växla mellan tillgängliga teman.
* Använd den för att Visa Gen2 [demo miljö](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Val av tema

Välj din profil ikon som finns i det övre högra hörnet för att välja ett nytt tema. Välj sedan **ändra tema**.

  [![Val av tema](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Val av språk är också tillgängligt genom att välja din profil ikon.

Azure Time Series Insights Explorer stöder två teman:

* **Ljust tema**: standard temat visas i det här dokumentet.
* **Mörkt tema**: återger Utforskaren så som visas här:

  [![Valt mörkt tema](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 miljö kontroller

### <a name="gen2-terms-panel"></a>Gen2 villkors panel

Det här avsnittet gäller endast befintliga gen1-miljöer som försöker använda Explorer i det uppdaterade användar gränssnittet. Du kanske vill använda gen1-produkten och Gen2-produkten i kombination. Vi har lagt till vissa funktioner från det befintliga användar gränssnittet i den uppdaterade Utforskaren, men du kan få den fullständiga användar gränssnitts upplevelsen för en gen1-miljö i den nya Azure Time Series Insights Explorer.

I stället för-hierarkin visas panelen för Azure Time Series Insights Gen2-villkor. På panelen villkor kan du definiera frågor i din miljö. Använd den för att även filtrera data baserat på ett predikat.

  [![Where-frågegrupp](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Azure Time Series Insights Gen2 terms Editor-panelen tar följande parametrar:

**Där**: Använd WHERE-satsen för att snabbt filtrera händelser med hjälp av en uppsättning operander som anges i följande tabell. Om du utför en sökning genom att välja en operand uppdateras predikatet automatiskt baserat på den sökningen. De operands typer som stöds är följande:

| Åtgärd    | Typer som stöds    | Kommentarer |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Sträng, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Sträng, bool, Double, DateTime, TimeSpan, NULL | Alla operander ska vara av samma typ eller vara NULL-konstant. |
| `HAS` | Sträng | Endast strängar med konstanta strängar är tillåtna på höger sida. En tom sträng och NULL är inte tillåtet. |

Om du vill veta mer om frågor och data typer som stöds kan du läsa [tids serie uttryck (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax).

### <a name="examples-of-where-clauses"></a>Exempel på WHERE-satser

  [![WHERE-sats exempel](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Mått**: en nedrullningsbar lista som visar alla numeriska kolumner (**dubbla**) som du kan använda som element för det aktuella diagrammet.

**Dela efter**: den här List rutan visar alla tillgängliga kategoriska-kolumner (strängar) i din modell som du kan gruppera dina data efter. Du kan lägga till upp till fem villkor för att visa på samma x-axel. Ange de parametrar som du vill använda och välj sedan **Lägg** till för att lägga till en ny term.

  [![Frågad och filtrerad vy en](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Du kan visa och dölja element i diagram panelen genom att välja ikonen synlig, som du ser i följande bild. Om du vill ta bort frågor helt väljer du det röda **krysset**.

  [![Avbryta en frågad och filtrerad alternativ](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att mata in [data](./concepts-ingestion-overview.md) i din miljö.

* Kolla in artikeln om [lagring](concepts-storage.md).

* Läs om [data modellering](./concepts-model-overview.md) i Azure Time Series Insights Gen2.

* Lär dig [hur du diagnostiserar och felsöker](./how-to-diagnose-troubleshoot.md) din miljö.
