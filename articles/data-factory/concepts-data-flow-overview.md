---
title: Mappa dataflöden
description: En översikt över mappning av dataflöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/11/2021
ms.openlocfilehash: 26d119fb6880f4c539e0064db424699bc5d22f96
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478683"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mappa dataflöden i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Vad är Mappa dataflöden?

Mappning av dataflöden är visuellt utformade datatransformationer i Azure Data Factory. Med dataflöden kan datatekniker utveckla datatransformeringslogik utan att skriva kod. Resulterande dataflöden körs som aktiviteter i Azure Data Factory som använder utskalade Apache Spark kluster. Dataflödesaktiviteter kan operationaliseras med befintliga funktioner Azure Data Factory, kontroll, flöde och övervakning.

Mappning av dataflöden ger en helt visuell upplevelse utan att kodning krävs. Dina dataflöden körs på ADF-hanterade körningskluster för utskalade databearbetning. Azure Data Factory hanterar all kodöversättning, sökvägsoptimering och körning av dina dataflödesjobb.

## <a name="getting-started"></a>Komma igång

Dataflöden skapas från fönstret med fabriksresurser som pipelines och datauppsättningar. Om du vill skapa ett dataflöde väljer du plustecknet bredvid **Fabriksresurser** och väljer **sedan Dataflöde**. 

![Nytt dataflöde](media/data-flow/new-data-flow.png)

Den här åtgärden tar dig till dataflödesarbetsytan, där du kan skapa din transformeringslogik. Välj **Lägg till källa** för att börja konfigurera din källtransformering. Mer information finns i [Källtransformering](data-flow-source.md).

## <a name="authoring-data-flows&quot;></a>Redigera dataflöden

Mappning av dataflöde har en unik redigeringsarbetsyta som är utformad för att göra det enkelt att skapa transformeringslogik. Dataflödesarbetsytan är uppdelad i tre delar: det översta fältet, diagrammet och konfigurationspanelen. 

![Skärmbild som visar dataflödesarbetsytan med den översta stapeln, grafen och konfigurationspanelen märkt.](media/data-flow/canvas-1.png &quot;Arbetsyta")

### <a name="graph"></a>Graph

Diagrammet visar transformeringsströmmen. Den visar ursprunget för källdata när de flödar till en eller flera mottagare. Om du vill lägga till en ny källa väljer du **Lägg till källa.** Om du vill lägga till en ny transformering väljer du plustecknet i det nedre högra högra av en befintlig transformering. Läs mer om hur du [hanterar dataflödesdiagrammet](concepts-data-flow-manage-graph.md).

![Skärmbild som visar diagramdelen av arbetsytan med en söktextruta.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurationspanel

Konfigurationspanelen visar de inställningar som är specifika för den valda transformeringen. Om ingen transformering har valts visas dataflödet. I den övergripande dataflödeskonfigurationen kan du lägga till parametrar via **fliken** Parametrar. Mer information finns i [Mappa dataflödesparametrar](parameters-data-flow.md).

Varje transformering innehåller minst fyra konfigurationsflikar.

#### <a name="transformation-settings"></a>Transformeringsinställningar

Den första fliken i varje transformerings konfigurationsfönster innehåller de inställningar som är specifika för transformeringen. Mer information finns på dokumentationssidan för transformeringen.

![Fliken Källinställningar](media/data-flow/source1.png "Fliken Källinställningar")

#### <a name="optimize"></a>Optimera

Fliken **Optimera** innehåller inställningar för att konfigurera partitioneringsscheman. Mer information om hur du optimerar dina dataflöden finns i [prestandaguiden för mappning av dataflöden.](concepts-data-flow-performance.md)

![Skärmbild som visar fliken Optimera, som innehåller alternativet Partition, Partitionstyp och Antal partitioner.](media/data-flow/optimize.png)

#### <a name="inspect&quot;></a>Inspektera

Fliken **Granska** innehåller en vy över metadata för dataströmmen som du transformerar. Du kan se antalet kolumner, kolumnerna har ändrats, kolumnerna har lagts till, datatyper, kolumnordningen och kolumnreferenserna. **Inspect** är en skrivskyddade vy över dina metadata. Du behöver inte ha felsökningsläget aktiverat för att se metadata i **fönstret** Inspektera.

![Inspektera](media/data-flow/inspect1.png &quot;Inspektera")

När du ändrar formen på dina data genom omvandlingar ser du flödet för metadataändringar i **fönstret** Inspektera. Om det inte finns något definierat schema i din källtransformering visas inte metadata i **fönstret** Inspektera. Brist på metadata är vanligt i schemaavdriftsscenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om felsökningsläget är aktiverat ger **fliken Dataförhandsgranskning** dig en interaktiv ögonblicksbild av data vid varje transformering. Mer information finns i [Dataförhandsgranskning i felsökningsläge.](concepts-data-flow-debug-mode.md#data-preview)

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela dataflödet, som att spara och validera. Du kan även visa den underliggande JSON-koden och dataflödesskriptet för din transformeringslogik. Mer information finns i [dataflödesskriptet](data-flow-script.md).

## <a name="available-transformations"></a>Tillgängliga transformningar

Visa [översikten över mappning av](data-flow-transformation-overview.md) dataflödestransformering för att hämta en lista över tillgängliga omvandlingar.

## <a name="data-flow-data-types"></a>Dataflödesdatatyper

array binary boolean complex decimal date float integer long map short string timestamp

## <a name="data-flow-activity"></a>Dataflödesaktivitet

Mappningsdataflöden operationaliseras i ADF-pipelines med hjälp av [dataflödesaktiviteten](control-flow-execute-data-flow-activity.md). Allt en användare behöver göra är att ange vilken integreringskörning som ska användas och skicka in parametervärden. Mer information finns i [Azure Integration Runtime.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Felsökningsläge

Med felsökningsläget kan du interaktivt se resultatet av varje transformeringssteg när du skapar och felsöker dina dataflöden. Felsökningssessionen kan användas både i när du skapar dataflödeslogiken och kör pipelinefelsökningskörningar med dataflödesaktiviteter. Mer information finns i dokumentationen [om felsökningsläge.](concepts-data-flow-debug-mode.md)

## <a name="monitoring-data-flows"></a>Övervaka dataflöden

Mappningsdataflödet integreras med befintliga Azure Data Factory funktioner för övervakning. Information om hur du förstår övervakningsutdata för dataflöden finns i [Övervaka mappning av dataflöden.](concepts-data-flow-monitoring.md)

Teamet Azure Data Factory skapat en guide [](concepts-data-flow-performance.md) för prestandajustering som hjälper dig att optimera körningstiden för dina dataflöden när du har skapat din affärslogik.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [källtransformering](data-flow-source.md).
* Lär dig hur du skapar dina dataflöden [i felsökningsläge.](concepts-data-flow-debug-mode.md)
