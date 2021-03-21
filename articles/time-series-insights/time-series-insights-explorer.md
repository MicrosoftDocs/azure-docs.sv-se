---
title: Utforska data med hjälp av Utforskaren-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du använder Azure Time Series Insights Explorer för att visa dina IoT-data.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95020120"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights gen1 Explorer

> [!CAUTION]
> Det här är en gen1-artikel.

I den här artikeln beskrivs funktionerna och alternativen för Azure Time Series Insights gen1 [Explorer-webbappen](https://insights.timeseries.azure.com/). Azure Time Series Insights Explorer visar de kraftfulla funktionerna för data visualisering som tillhandahålls av tjänsten och kan nås i din egen miljö.

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Du får en global vy över dina data, vilket gör att du snabbt kan verifiera din IoT-lösning och undvika kostsamma drift stopp för verksamhets kritiska enheter. Du kan identifiera dolda trender, upptäcka avvikelser och utföra rotor Saks analyser i nära real tid.

> [!TIP]
> En guidad rundtur i demonstrations miljön finns i snabb starten för [Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Lär dig mer om att köra frågor mot data med hjälp av Azure Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Spela upp föregående video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"komma igång med Azure Time Series Insights med hjälp av en Azure IoT Solution Accelerator."</a>

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda Azure Time Series Insights Explorer måste du:

- Skapa en Azure Time Series Insightss miljö. Mer information finns [i så här kommer du igång med Azure Time Series Insights](./time-series-insights-get-started.md).
- [Ge åtkomst](./concepts-access-policies.md) till ditt konto i-miljön.
- Lägg till en händelse källa för [IoT Hub](./how-to-ingest-data-iot-hub.md) eller [Event Hub](./how-to-ingest-data-event-hub.md) .

## <a name="explore-and-query-data"></a>Utforska och fråga efter data

I minuter när du ansluter din händelse källa till din Azure Time Series Insights-miljö kan du utforska och fråga dina tids serie data.

1. Starta genom att öppna [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com/) i webbläsaren. Välj en miljö på vänster sida i fönstret. Alla miljöer som du har åtkomst till visas i alfabetisk ordning.

1. När du har valt en miljö använder du antingen **från** och **till** konfigurationer överst eller väljer och drar över det tidsintervall som du vill använda. Välj förstorings glaset i det övre högra hörnet eller högerklicka på det valda TimeSpan och välj **Sök**.

1. Du kan också uppdatera tillgänglighet automatiskt varje minut genom att välja knappen **Automatisk på** . Knappen **automatiskt på** gäller bara tillgänglighets diagrammet, inte innehållet i huvud visualiseringen.

1. Azure Cloud-ikonen tar dig till din miljö i Azure Portal.

   [![Val av Azure Time Series Insightss miljö](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Sedan visas ett diagram som visar antalet händelser under det valda tidsintervallet. Här har du ett antal kontroller:

    - **Panelen villkors redigerare**: term området är den plats där du frågar din miljö. Den finns på vänster sida av skärmen:
      - **Mått**: den här List rutan visar alla numeriska kolumner (**dubbla** tal).
      - **Dela efter**: den här List rutan visar kategoriska kolumner (**strängar**).
      - Du kan aktivera steg-interpolation, Visa minsta och högsta och justera y-axeln från kontroll panelen bredvid **mått**. Du kan också justera om data som visas är antal, medelvärde eller summan av data.
      - Du kan lägga till upp till fem villkor för att visa på samma x-axel. Välj **Lägg** till för att lägga till en ny term eller Använd knappen **klona den här termen** för att lägga till en kopia av en befintlig term.

        [![Val av villkor, filtrering och frågor](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikat**: Använd predikatet för att snabbt filtrera händelser med hjälp av en uppsättning operander som anges i följande tabell. Om du utför en sökning genom att välja eller klicka på, uppdateras predikatet automatiskt utifrån sökningen. Operands typer som stöds är:

         |Åtgärd  |Typer som stöds  |Kommentarer  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **datetime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **Sträng**, **bool**, **Double**, **datetime**, **TimeSpan**, **Null**        |         |
         |**FÖR**     | **Sträng**, **bool**, **Double**, **datetime**, **TimeSpan**, **Null**        |  Alla operander ska vara av samma typ eller vara **Null** -konstant.        |
         |**HADE**     | **Sträng**        |  Endast strängar med konstanta strängar är tillåtna på höger sida. En tom sträng och **Null** är inte tillåtet.       |

      - **Exempelfrågor**

         [![Exempel på gen1-frågor](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Du kan använda Slider-verktyget för **intervall storlek** för att zooma in och ut ur intervall under samma TimeSpan. Skjutreglaget ger en mer exakt kontroll av förflyttningen mellan stora sektorer, som visar jämna trender nedåt till sektorer som är små som i millisekunder, vilket gör att du kan visa och analysera detaljerade, högupplösta klipp av dina data. Skjutreglagets standard start punkt anges som den mest optimala vyn av data från ditt val för att balansera upplösning, frågans hastighet och granularitet.

1. Verktyget **tids pensel** är det enkelt att navigera från ett TimeSpan till ett annat.

1. Välj ikonen **Spara** för att spara din aktuella fråga och dela den med andra användare av miljön. När du väljer ikonen **Öppna** kan du granska alla dina sparade frågor och eventuella delade frågor för andra användare i miljöer som du har åtkomst till.

   [![Frågor](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualisera data

1. Använd verktyget **perspektiv vy** för en samtidig visning av upp till fyra unika frågor. Knappen **perspektivvy** visas i det övre högra hörnet i diagrammet.

   [![Välj frågor som ska läggas till i perspektiv fönstret](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Visa ett diagram för att visuellt utforska dina data och Använd **diagram** verktygen:

    - **Välj** eller **Klicka på** ett visst tidsintervall eller en enskild data serie.
    - I ett TimeSpan-val kan du zooma eller utforska händelser.
    - I en data serie kan du dela serien efter en annan kolumn, lägga till serien som en ny term, Visa endast den valda serien, utesluta den valda serien, pinga serien eller utforska händelser från den valda serien.
    - I filter ytan till vänster i diagrammet kan du granska alla visade data serier och sortera efter värde eller namn. Du kan också Visa alla data serier eller fästa eller ej fästa serier. Du kan välja en enskild data serie och dela serien efter en annan kolumn, lägga till serien som en ny term, Visa endast den markerade serien, ta bort den markerade serien, fästa serien eller utforska händelser från den valda serien.
    - När du visar flera villkor samtidigt kan du stapla, ta del av och granska ytterligare data om en data serie och använda samma y-axel för alla termer. Använd knapparna i det övre högra hörnet i diagrammet.

    [![Diagram verktygets övre högra hörn alternativ inställningar](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Använd **termisk karta** för att snabbt hitta unika eller avvikande data serier i en specifik fråga. Endast en sökterm kan visualiseras som en termisk karta.

    [Time Series Insights Explorer termisk karta diagram](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (Media/Time-Series-Insights-Explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. När du utforskar händelser genom att markera eller högerklicka på panelen **händelser** blir panelen händelser tillgänglig. Här kan du granska alla dina obehandlade händelser och exportera dina händelser som JSON-eller CSV-filer. Azure Time Series Insights lagrar alla rå data.

    [![Händelser](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Välj fliken **statistik** när du har utforskat händelser för att exponera mönster och kolumn statistik.

    - **Mönster**: den här funktionen utvärderar de mest statistiskt signifikanta mönstren i ett valt data område. Du behöver inte titta på tusentals händelser för att förstå vilka mönster som kräver mest tid och energi. Med Azure Time Series Insights kan du gå direkt till dessa statistiskt betydande mönster för att fortsätta utföra en analys. Den här funktionen är också användbar för obduktions undersökningar i historiska data.
    - **Kolumn statistik**: kolumn statistik innehåller diagram och tabeller som avbryter data från varje kolumn i den valda data serien under det valda tidsintervallet.

      [![STATISTIK kolumn diagram och alternativ](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nu har du lärt dig om viktiga funktioner, konfigurations inställningar och visnings alternativ som är tillgängliga i Azure Time Series Insights Explorer-webbappen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostiserar och löser problem](time-series-insights-diagnose-and-solve-problems.md) i din Azure Time Series Insightss miljö.

- Ta den guidade guidade visningen av [Azure Time Series Insights](time-series-quickstart.md) .