---
title: Ranknings omvandling i data flöde för mappning
description: Så här använder du Azure Data Factory dess ranknings omvandling för data flöde generera en rangordnings kolumn
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: b7adb6bf13cba5f886b442515e8ba5661cfeb8ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490932"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Ranknings omvandling i data flöde för mappning 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd rankning-omvandlingen för att generera en ordnad rangordning baserat på sorterings villkor som anges av användaren. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Konfiguration

![Rangordnings inställningar](media/data-flow/rank-configuration.png "Rangordnings inställningar")

**SKIFT läges okänsligt:** Om en sorterings kolumn är av typen sträng, kommer ärendet att delas in i rangordningen. 

**Tätare:** Om den är aktive rad blir rang kolumnen tätare. Antalet rang räknas som ett löp nummer och ranknings värden kommer inte att hoppas över efter en förbindelse.

**Rangordnings kolumn:** Namnet på rang kolumnen som genererades. Den här kolumnen är av typen Long.

**Sorterings villkor:** Välj vilka kolumner som ska sorteras efter och i vilken ordning sorteringen ska ske. Ordningen bestämmer sorterings prioriteten.

Konfigurationen ovan tar inkommande basket-data och skapar en rang kolumn med namnet ' pointsRanking '. Raden med det högsta värdet för kolumn *punkter* har värdet 1 för *pointsRanking* .

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exempel

![Rangordnings inställningar](media/data-flow/rank-configuration.png "Rangordnings inställningar")

Data flödes skriptet för ovanstående rang konfiguration finns i följande kodfragment.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Nästa steg

Filtrera rader baserat på rang värden med hjälp av [filter omvandlingen](data-flow-filter.md).
