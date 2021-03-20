---
title: Sortera omvandling i data flöde för mappning
description: Azure Data Factory mappa data sorterings omvandling
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81606327"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Sortera omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med sorterings omvandlingen kan du sortera inkommande rader i den aktuella data strömmen. Du kan välja enskilda kolumner och sortera dem i stigande eller fallande ordning.

> [!NOTE]
> Mappning av data flöden utförs i Spark-kluster som distribuerar data över flera noder och partitioner. Om du väljer att partitionera om dina data i en efterföljande omvandling kan du förlora din sortering på grund av reshuffling av data.

## <a name="configuration"></a>Konfiguration

![Sorterings inställningar](media/data-flow/sort.png "Sortera")

**SKIFT läges okänsligt:** Om du vill ignorera Skift läge vid sortering av sträng eller textfält

**Sortera endast inom partitioner:** Allteftersom data flöden körs i Spark är varje data ström indelad i partitioner. Den här inställningen sorterar endast data inom de inkommande partitionerna i stället för att sortera hela data strömmen. 

**Sorterings villkor:** Välj vilka kolumner som ska sorteras efter och i vilken ordning sorteringen ska ske. Ordningen bestämmer sorterings prioriteten. Välj om nullvärden ska visas i början eller slutet av data strömmen.

### <a name="computed-columns"></a>Beräknade kolumner

Om du vill ändra eller extrahera ett kolumn värde innan du tillämpar sorteringen, hovrar du över kolumnen och väljer beräknad kolumn. Då öppnas uttrycks verktyget för att skapa ett uttryck för sorterings åtgärden i stället för att använda ett kolumn värde.

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

![Sorterings inställningar](media/data-flow/sort.png "Sortera")

Data flödes skriptet för ovanstående sorterings konfiguration finns i kodfragmentet nedan.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Nästa steg

Efter sorteringen kanske du vill använda den [sammanställda omvandlingen](data-flow-aggregate.md)
