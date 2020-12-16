---
title: Dataflöden
description: En översikt över data flöden i Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592666"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Data flöden i Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Vad är data flöden?

Data flöden är visuellt utformade för data transformationer i Azure Synapse Analytics. Data flöden gör det möjligt för data tekniker att utveckla data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Synapse Analytics-pipeliner som använder uppskalade Apache Spark kluster. Data flödes aktiviteter kan hanteras med hjälp av befintliga funktioner för schemaläggning, styrning, flöde och övervakning i Azure Synapse Analytics.

Data flöden ger en helt visuell upplevelse utan kodning som krävs. Dina data flöden körs på Synapse-hanterade körnings kluster för att skala ut data bearbetningen. Azure Synapse Analytics hanterar all kod översättning, Sök vägs optimering och körning av dina data flödes jobb.

## <a name="getting-started"></a>Komma igång

Data flöden skapas från fönstret utveckla i Synapse Studio. Om du vill skapa ett data flöde väljer du plus tecknet bredvid **utveckla** och väljer sedan **data flöde**. 

![Nytt data flöde](media/data-flow/new-data-flow.png)

Den här åtgärden tar dig till data flödets arbets yta där du kan skapa din omvandlings logik. Välj **Lägg till källa** för att börja konfigurera din käll omvandling. Mer information finns i [käll omvandling](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Redigera data flöden

Data flödet har en unik redigerings yta som är utformad för att skapa omvandlings logik enkelt. Data flödes arbets ytan är uppdelad i tre delar: det översta fältet, grafen och konfigurations panelen. 

![Skärm bild som visar data flödets arbets yta med översta stapel-, graf-och konfigurations panelen märkt.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

I diagrammet visas omvandlings strömmen. Den visar härkomst för källdata när den flödar till en eller flera handfat. Om du vill lägga till en ny källa väljer du **Lägg till källa**. Om du vill lägga till en ny omvandling väljer du plus tecknet längst ned till höger i en befintlig omvandling. Läs mer om hur du [hanterar data flödes diagrammet](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Skärm bild som visar graf-delen av arbets ytan med en söktext ruta.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurations panel

Konfigurations panelen visar inställningarna för den aktuella valda omvandlingen. Om ingen omvandling väljs visas data flödet. I den övergripande data flödes konfigurationen kan du lägga till parametrar via fliken **parametrar** . Mer information finns i [data flödes parametrar](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Varje omvandling innehåller minst fyra konfigurations flikar.

#### <a name="transformation-settings"></a>Omvandlings inställningar

Den första fliken i varje omvandlings konfigurations fönster innehåller inställningar som är speciella för omvandlingen. Mer information finns på dokument sidan för omvandling.

![Fliken käll inställningar](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimera

Fliken **Optimize** innehåller inställningar för att konfigurera partitionerings scheman. Mer information om hur du optimerar dina data flöden finns i [prestanda guiden för att mappa data flödet](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Skärm bild som visar fliken Optimize](media/data-flow/optimize.png)

#### <a name="inspect"></a>Allmänt

Fliken **Granska** visar metadata för den data ström som du transformerar. Du kan se kolumn antal, kolumnerna har ändrats, kolumnerna tillagda, data typer, kolumn ordning och kolumn referenser. **Granska** är en skrivskyddad vy av dina metadata. Du behöver inte ha aktiverat fel söknings läge för att se metadata i rutan **Granska** .

![Fliken Granska](media/data-flow/inspect.png)

När du ändrar formen på dina data med omvandlingar visas flödet ändringar i metadata i rutan **Granska** . Om det inte finns ett definierat schema i din käll omvandling visas inte metadata i rutan **Granska** . Brist på metadata är vanligt i schema avvikelse scenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om fel söknings läget är på visar fliken **data förhands granskning** en interaktiv ögonblicks bild av data vid varje transformering. Mer information finns [i förhands granskning av data i fel söknings läge](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela data flödet, t. ex. verifierings-och fel söknings inställningar. Du kan även visa den underliggande JSON-koden och data flödes skriptet för din omvandlings logik.

## <a name="available-transformations"></a>Tillgängliga omvandlingar

Visa [Översikt över mappnings data flödet](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för att hämta en lista över tillgängliga transformeringar.

## <a name="data-flow-activity"></a>Data flödes aktivitet

Data flöden används i pipeline för Azure Synapse Analytics med [data flödes aktiviteten](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Alla användare behöver göra är att ange vilken integration runtime som ska användas och skicka in parameter värden. Mer information finns i [Azure integration runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Felsökningsläge

Fel söknings läge gör att du interaktivt kan se resultatet av varje omformnings steg medan du skapar och felsöker dina data flöden. Felsökningssessionen kan användas både i när du skapar din data flödes logik och kör fel söknings körning av pipelinen med data flödes aktiviteter. Mer information finns i dokumentationen om [fel söknings läge](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Övervaka data flöden

Data flödet integreras med befintliga funktioner i Azure Synapse Analytics-övervakning. Information om hur du förstår utdata för data flödes övervakning finns i [övervaka mappning av data flöden](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Azure Synapse Analytics-teamet har skapat en [prestanda justerings guide](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) som hjälper dig att optimera körnings tiden för dina data flöden när du har skapat din affärs logik.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [käll omvandling](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Lär dig hur du skapar dina data flöden i [fel söknings läge](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
