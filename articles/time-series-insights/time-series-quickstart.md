---
title: 'Snabb start: Azure Time Series Insights Explorer-Azure Time Series Insights | Microsoft Docs'
description: Lär dig hur du kommer igång med Azure Time Series Insights Explorer. Visualisera stora volymer med IoT-data och guidade funktioner i din miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/30/2020
ms.openlocfilehash: dd5ed52187a7cd23142b59e20c91c6d125946a72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91613789"
---
# <a name="quickstart-explore-azure-time-series-insights-gen1"></a>Snabb start: utforska Azure Time Series Insights gen1

> [!CAUTION]
> Det här är en gen1-artikel.

Den här Azure Time Series Insights Explorer-snabb starten hjälper dig att komma igång med Azure Time Series Insights i en kostnads fri demonstrations miljö. I den här snabb starten får du lära dig hur du använder webbläsaren för att visualisera stora mängder IoT-data och upptäckter som är allmänt tillgängliga nu.

Azure Time Series Insights är en helt hanterad tjänst för analys, lagring och visualisering som gör det enklare att utforska och analysera miljarder IoT-händelser samtidigt. Du får en global vy över dina data så att du snabbt kan verifiera din IoT-lösning och undvika kostsamma drift stopp för verksamhets kritiska enheter. Azure Time Series Insights hjälper dig att identifiera dolda trender, upptäcka avvikelser och utföra rotor Saks analyser i nära real tid.

För ytterligare flexibilitet kan du lägga till Azure Time Series Insights i ett befintligt program via dess kraftfulla [REST-API: er](./concepts-query-overview.md) och [klient-SDK](https://github.com/microsoft/tsiclient). Du kan använda API: er för att lagra, fråga och använda Time Series-data i ett valfritt klient program. Du kan också använda klient-SDK: n för att lägga till UI-komponenter i ditt befintliga program.

Den här snabb starten i Azure Time Series Insights Explorer innehåller en guidad genom gång av funktioner.

> [!IMPORTANT]
> Skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte har skapat något redan.

## <a name="prepare-the-demo-environment"></a>Förbered demo miljön

1. I webbläsaren går du till gen1- [demon](https://insights.timeseries.azure.com/demo).

1. Om du uppmanas till det loggar du in på Azure Time Series Insights Explorer med dina autentiseringsuppgifter för Azure-kontot.

1. Sidan Azure Time Series Insights guidad visning visas. Välj **Nästa** för att starta den guidade visningen.

   [![Välkommen till snabb start – Välj nästa](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Utforska demo miljön

1. **Panelen för val av tid** visas. Använd den här panelen för att välja en tidsram som ska visualiseras.

   [![Panelen för val av tid](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Välj en tidsram och dra den i regionen. Välj sedan **Sök**.

   [![Välj en tidsram](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Azure Time Series Insights visar en diagram visualisering för den tidsram som du har angett. Du kan utföra olika åtgärder i linje diagrammet. Du kan till exempel filtrera, fästa, sortera och stapla.

   Gå tillbaka till **panelen för val av tid** genom att välja nedåtpilen som visas:

   [![Diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Välj **Lägg till** på **panelen villkor** för att lägga till ett nytt sökord.

   [![Lägg till panelen Sök villkor](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.

   [![Utforska händelser](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Ett rutnät med dina rå data visas från den region som du utforskar.

   [![Utforska händelser – rutnätsvy-datavy](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Markera och filtrera data

1. Redigera dina villkor för att ändra värdena i diagrammet. Lägg till en annan term för att korsa olika typer av värden.

   [![Lägga till ett villkor](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Lämna rutan **filter serie** tom om du vill visa alla valda sökord eller ange ett filter villkor i rutan **filter serie** för filtrering av Improvised-serier.

   [![Filtrera serie](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar.

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure Portal](https://portal.azure.com)väljer du **alla resurser**, letar reda på Azure Time Series Insights resurs gruppen.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

* Du är redo att skapa din egen Azure Time Series Insightss miljö. Läs [Planera din Azure Time Series Insightss miljö](time-series-insights-environment-planning.md).
