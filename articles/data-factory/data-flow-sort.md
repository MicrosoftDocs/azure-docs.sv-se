---
title: Sortera transformering i mappning av dataflöde
description: Azure Data Factory mappning av data sorterad transformering
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588742"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortera transformering i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med sorteringstransformering kan du sortera inkommande rader i den aktuella dataströmmen. Du kan välja enskilda kolumner och sortera dem i stigande eller fallande ordning.

> [!NOTE]
> Mappningsdataflöden körs på Spark-kluster som distribuerar data över flera noder och partitioner. Om du väljer att partitionera om dina data i en efterföljande transformering kan du förlora sorteringen på grund av omsuffring av data. Det bästa sättet att upprätthålla sorteringsordningen i ditt dataflöde är att ange en enskild partition på fliken Optimera på transformeringsfliken och hålla sorteringstransformationen så nära mottagaren som möjligt.

## <a name="configuration"></a>Konfiguration

![Sorteringsinställningar](media/data-flow/sort.png "Sortera")

**Okänsligt fall:** Om du vill ignorera fall vid sortering av sträng- eller textfält

**Sortera endast inom partitioner:** När dataflöden körs på Spark delas varje dataström in i partitioner. Den här inställningen sorterar endast data inom inkommande partitioner i stället för att sortera hela dataströmmen. 

**Sorteringsvillkor:** Välj vilka kolumner du sorterar efter och i vilken ordning sorteringen ska ske. Ordningen avgör sorteringsprioritet. Välj om null-värden ska visas i början eller slutet av dataströmmen.

### <a name="computed-columns"></a>Beräknade kolumner

Om du vill ändra eller extrahera ett kolumnvärde innan du tillämpar sorteringen hovrar du över kolumnen och väljer "beräknad kolumn". Då öppnas uttrycksverktyget för att skapa ett uttryck för sorteringsåtgärden i stället för att använda ett kolumnvärde.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exempel

![Sorteringsinställningar](media/data-flow/sort.png "Sortera")

Dataflödesskriptet för sorteringskonfigurationen ovan finns i kodfragmentet nedan.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Nästa steg

Efter sorteringen kanske du vill använda [aggregeringsomvandlingen](data-flow-aggregate.md)
