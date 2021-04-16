---
title: Dataflöden
description: En översikt över dataflöden i Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 4769cc8abe121625f3bf77785cd681c0f649d166
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567697"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Dataflöden i Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Vad är dataflöden?

Dataflöden är visuellt utformade datatransformationer i Azure Synapse Analytics. Med dataflöden kan datatekniker utveckla datatransformeringslogik utan att skriva kod. Resulterande dataflöden körs som aktiviteter inom Azure Synapse Analytics som använder utskalade Apache Spark kluster. Dataflödesaktiviteter kan operationaliseras med befintliga funktioner Azure Synapse Analytics, kontroll, flöde och övervakning.

Dataflöden ger en helt visuell upplevelse utan att kodning krävs. Dina dataflöden körs på Synapse-hanterade körningskluster för utskalade databearbetning. Azure Synapse Analytics hanterar all kodöversättning, sökvägsoptimering och körning av dina dataflödesjobb.

## <a name="getting-started"></a>Komma igång

Dataflöden skapas från fönstret Utveckla i Synapse Studio. Om du vill skapa ett dataflöde väljer du plustecknet bredvid **Utveckla** och väljer **sedan Dataflöde**. 

![Nytt dataflöde](media/data-flow/new-data-flow.png)

Den här åtgärden tar dig till dataflödesarbetsytan, där du kan skapa din transformeringslogik. Välj **Lägg till källa** för att börja konfigurera din källtransformering. Mer information finns i [Källtransformering](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Redigera dataflöden

Dataflödet har en unik redigeringsarbetsyta som är utformad för att göra det enkelt att skapa transformeringslogik. Dataflödesarbetsytan är uppdelad i tre delar: det översta fältet, diagrammet och konfigurationspanelen. 

![Skärmbild som visar dataflödesarbetsytan med den översta stapeln, grafen och konfigurationspanelen märkt.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

Diagrammet visar transformeringsströmmen. Den visar ursprunget för källdata när de flödar till en eller flera mottagare. Om du vill lägga till en ny källa väljer du **Lägg till källa.** Om du vill lägga till en ny transformering väljer du plustecknet i det nedre högra högra av en befintlig transformering. Läs mer om hur du [hanterar dataflödesdiagrammet](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Skärmbild som visar diagramdelen av arbetsytan med en söktextruta.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurationspanel

Konfigurationspanelen visar de inställningar som är specifika för den valda transformeringen. Om ingen transformering har valts visas dataflödet. I den övergripande dataflödeskonfigurationen kan du lägga till parametrar via **fliken** Parametrar. Mer information finns i [Dataflödesparametrar.](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Varje transformering innehåller minst fyra konfigurationsflikar.

#### <a name="transformation-settings"></a>Transformeringsinställningar

Den första fliken i varje transformerings konfigurationsfönster innehåller de inställningar som är specifika för transformeringen. Mer information finns på dokumentationssidan för transformeringen.

![Fliken Källinställningar](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimera

Fliken **Optimera** innehåller inställningar för att konfigurera partitioneringsscheman. Mer information om hur du optimerar dina dataflöden finns i [prestandaguiden för mappning av dataflöden.](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Skärmbild som visar fliken Optimera](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspektera

Fliken **Granska** innehåller en vy över metadata för dataströmmen som du transformerar. Du kan se kolumnantal, kolumnerna har ändrats, de kolumner som lagts till, datatyper, kolumnordningen och kolumnreferenser. **Inspect** är en skrivskyddade vy över dina metadata. Du behöver inte ha felsökningsläget aktiverat för att se metadata i **fönstret** Inspektera.

![Fliken Granska](media/data-flow/inspect.png)

När du ändrar formen på dina data genom omvandlingar ser du flödet för metadataändringar i **fönstret** Inspektera. Om det inte finns något definierat schema i din källtransformering visas inte metadata i **fönstret** Inspektera. Brist på metadata är vanligt i schemaavdriftsscenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om felsökningsläget är aktiverat ger fliken **Dataförhandsgranskning** dig en interaktiv ögonblicksbild av data vid varje transformering. Mer information finns i [Förhandsgranskning av data i felsökningsläge.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela dataflödet, till exempel validerings- och felsökningsinställningar. Du kan även visa den underliggande JSON-koden och dataflödesskriptet för din transformeringslogik.

## <a name="available-transformations"></a>Tillgängliga transformningar

Visa [översikten över mappning av](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) dataflödestransformering för att hämta en lista över tillgängliga omvandlingar.

## <a name="data-flow-activity"></a>Dataflödesaktivitet

Dataflöden operationaliseras i Azure Synapse Analytics pipelines med hjälp av [dataflödesaktiviteten](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Allt en användare behöver göra är att ange vilken integreringskörning som ska användas och skicka in parametervärden. Mer information finns i [Azure Integration Runtime.](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)

## <a name="debug-mode"></a>Felsökningsläge

Med felsökningsläget kan du interaktivt se resultatet av varje transformeringssteg när du skapar och felsöker dina dataflöden. Felsökningssessionen kan användas både i när du skapar dataflödeslogiken och kör pipelinefelsökningskörningar med dataflödesaktiviteter. Mer information finns i dokumentationen [om felsökningsläge.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="monitoring-data-flows"></a>Övervaka dataflöden

Dataflödet integreras med befintliga Azure Synapse Analytics funktioner för övervakning. Information om hur du förstår övervakningsutdata för dataflöden finns i [Övervaka mappning av dataflöden.](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Teamet Azure Synapse Analytics skapat en prestandajusteringsguide som hjälper dig att optimera körningstiden för dina dataflöden när du har skapat din affärslogik. [](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [källtransformering](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Lär dig hur du skapar dina dataflöden [i felsökningsläge.](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
