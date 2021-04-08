---
title: Filter omvandling i data flöde för mappning
description: Filtrera bort rader med hjälp av filter omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84112814"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filter omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Filtret Transforms tillåter rad filtrering baserat på ett villkor. Utdataströmmen innehåller alla rader som matchar filtrerings villkoret. Filter omvandlingen liknar en WHERE-sats i SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfiguration

Använd data flödets uttrycks verktyg för att ange ett uttryck för filter villkoret. Öppna uttrycks verktyget genom att klicka på den blå rutan. Filter villkoret måste vara av typen Boolean. Mer information om hur du skapar ett uttryck finns i [uttrycks verktygets](concepts-data-flow-expression-builder.md) dokumentation.

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en filter omvandling med namnet `FilterBefore1960` som tar i inkommande data ström `CleanData` . Filter villkoret är uttrycket `year <= 1960` .

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nästa steg

Filtrera ut kolumner med [Select-omvandlingen](data-flow-select.md)
