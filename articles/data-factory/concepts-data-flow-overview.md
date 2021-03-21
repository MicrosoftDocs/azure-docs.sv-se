---
title: Mappa dataflöden
description: En översikt över att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: 1529d2d546227880fee71d1823482e040d2e57c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564328"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mappa data flöden i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>Vad är Mappa dataflöden?

Mappning av data flöden är visuellt utformad med data transformationer i Azure Data Factory. Data flöden gör det möjligt för data tekniker att utveckla data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Data Factory pipelines som använder utskalade Apache Spark kluster. Data flödes aktiviteter kan användas med befintliga Azure Data Factory schemaläggning, kontroll, flöde och övervakning.

Genom att mappa data flöden får du en helt visuell upplevelse utan kodning som krävs. Dina data flöden körs på ADF-hanterade körnings kluster för att skala ut data bearbetningen. Azure Data Factory hanterar all kod översättning, Sök vägs optimering och körning av dina data flödes jobb.

## <a name="getting-started"></a>Komma igång

Data flöden skapas från fönstret fabriks resurser som pipelines och data uppsättningar. Om du vill skapa ett data flöde väljer du plus tecknet bredvid **fabriks resurser** och väljer sedan **data flöde**. 

![Nytt data flöde](media/data-flow/new-data-flow.png)

Den här åtgärden tar dig till data flödets arbets yta där du kan skapa din omvandlings logik. Välj **Lägg till källa** för att börja konfigurera din käll omvandling. Mer information finns i [käll omvandling](data-flow-source.md).

## <a name="authoring-data-flows"></a>Redigera data flöden

Kart data flödet har en unik redigerings yta som är utformad för att skapa omvandlings logik enkelt. Data flödes arbets ytan är uppdelad i tre delar: det översta fältet, grafen och konfigurations panelen. 

![Skärm bild som visar data flödets arbets yta med översta stapel-, graf-och konfigurations panelen märkt.](media/data-flow/canvas-1.png "Arbetsyta")

### <a name="graph"></a>Graph

I diagrammet visas omvandlings strömmen. Den visar härkomst för källdata när den flödar till en eller flera handfat. Om du vill lägga till en ny källa väljer du **Lägg till källa**. Om du vill lägga till en ny omvandling väljer du plus tecknet längst ned till höger i en befintlig omvandling. Läs mer om hur du [hanterar data flödes diagrammet](concepts-data-flow-manage-graph.md).

![Skärm bild som visar graf-delen av arbets ytan med en söktext ruta.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Konfigurations panel

Konfigurations panelen visar inställningarna för den aktuella valda omvandlingen. Om ingen omvandling väljs visas data flödet. I den övergripande data flödes konfigurationen kan du lägga till parametrar via fliken **parametrar** . Mer information finns i [mappa data flödes parametrar](parameters-data-flow.md).

Varje omvandling innehåller minst fyra konfigurations flikar.

#### <a name="transformation-settings"></a>Omvandlings inställningar

Den första fliken i varje omvandlings konfigurations fönster innehåller inställningar som är speciella för omvandlingen. Mer information finns på dokument sidan för omvandling.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

#### <a name="optimize"></a>Optimera

Fliken **Optimize** innehåller inställningar för att konfigurera partitionerings scheman. Mer information om hur du optimerar dina data flöden finns i [prestanda guiden för att mappa data flödet](concepts-data-flow-performance.md).

![Skärm bild som visar fliken optimering, som innehåller partitionsalternativ, partitionstyper och antalet partitioner.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Allmänt

Fliken **Granska** visar metadata för den data ström som du transformerar. Du kan se kolumn antal, kolumnerna har ändrats, kolumnerna tillagda, data typer, kolumn ordning och kolumn referenser. **Granska** är en skrivskyddad vy av dina metadata. Du behöver inte ha aktiverat fel söknings läge för att se metadata i rutan **Granska** .

![Allmänt](media/data-flow/inspect1.png "Allmänt")

När du ändrar formen på dina data med omvandlingar visas flödet ändringar i metadata i rutan **Granska** . Om det inte finns ett definierat schema i din käll omvandling visas inte metadata i rutan **Granska** . Brist på metadata är vanligt i schema avvikelse scenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om fel söknings läget är på visar fliken **data förhands granskning** en interaktiv ögonblicks bild av data vid varje transformering. Mer information finns [i förhands granskning av data i fel söknings läge](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela data flödet, t. ex. Spara och verifiera. Du kan även visa den underliggande JSON-koden och data flödes skriptet för din omvandlings logik. Mer information finns i [data flödes skriptet](data-flow-script.md).

## <a name="available-transformations"></a>Tillgängliga omvandlingar

Visa [Översikt över mappnings data flödet](data-flow-transformation-overview.md) för att hämta en lista över tillgängliga transformeringar.

## <a name="data-flow-activity"></a>Data flödes aktivitet

Mappning av data flöden fungerar i ADF-pipeline med [data flödes aktiviteten](control-flow-execute-data-flow-activity.md). Alla användare behöver göra är att ange vilken integration runtime som ska användas och skicka in parameter värden. Mer information finns i [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

## <a name="debug-mode"></a>Felsökningsläge

Fel söknings läge gör att du interaktivt kan se resultatet av varje omformnings steg medan du skapar och felsöker dina data flöden. Felsökningssessionen kan användas både i när du skapar din data flödes logik och kör fel söknings körning av pipelinen med data flödes aktiviteter. Mer information finns i dokumentationen om [fel söknings läge](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Övervaka data flöden

Kart data flödet integreras med befintliga Azure Data Factory övervakningsfunktionerna. Information om hur du förstår utdata för data flödes övervakning finns i [övervaka mappning av data flöden](concepts-data-flow-monitoring.md).

Azure Data Factorys teamet har skapat en [prestanda justerings guide](concepts-data-flow-performance.md) som hjälper dig att optimera körnings tiden för dina data flöden när du har skapat din affärs logik.

## <a name="available-regions"></a>Tillgängliga regioner

= = = = = = = Mappa data flöden är tillgängliga i följande regioner i ADF:

| Azure-region | Data flöden i ADF |
| ------------ | ----------------- |
|  Australien, centrala | |
| Australien, centrala 2 | |
| Australien, östra | ✓ |
| Australien, sydöstra   | ✓ |
| Brasilien, södra  | ✓ |
| Kanada, centrala | ✓ |
| Indien, centrala | ✓ |
| Central US    | ✓ |
| Kina, östra |      |
| Kina, östra 2  |   |
| Kina, icke-regional | |
| Kina, norra |     |
| Kina, norra 2 | |
| Asien, östra | ✓ |
| East US   | ✓ |
| USA, östra 2 | ✓ |
| Frankrike, centrala | ✓ |
| Frankrike, södra  | |
| Tyskland, centrala (suverän) | |
| Tyskland, regionsoberoende (suverän) | |
| Tyskland, norra (offentlig) | |
| Tyskland nordöstra (suverän) | |
| Tyskland, västra centrala (offentlig) |  |
| Japan, östra | ✓ |
| Japan, västra |  |
| Sydkorea, centrala | ✓ |
| Sydkorea, södra | |
| USA, norra centrala  | ✓ |
| Europa, norra  | ✓ |
| Östra Norge | |
| Norge, väst | |
| Sydafrika, norra    | ✓ |
| Sydafrika, västra |  |
| USA, södra centrala  | |
| Indien, södra | |
| Sydostasien    | ✓ |
| Schweiz, norra |   |
| Schweiz, västra | |
| Förenade Arabemiraten Central | |
| Förenade Arabemiraten, norra |  |
| Storbritannien, södra  | ✓ |
| Storbritannien, västra |     |
| USA DoD, centrala | |
| USA DoD, östra | |
| US Gov, Arizona |      |
| US Gov, regionsoberoende | |
| US Gov, Texas | |
| US Gov, Virginia |     |
| USA, västra centrala |     |
| Europa, västra   | ✓ |
| Indien, västra | |
| USA, västra   | ✓ |
| USA, västra 2 | ✓ |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [käll omvandling](data-flow-source.md).
* Lär dig hur du skapar dina data flöden i [fel söknings läge](concepts-data-flow-debug-mode.md).
