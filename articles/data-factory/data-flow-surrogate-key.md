---
title: Transformering av surrogat nyckel i mappnings data flödet
description: Så här använder du Azure Data Factory mappningen av data Flow surrogat Key för att generera sekventiella nyckel värden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93147192"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformering av surrogat nyckel i mappnings data flödet 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd surrogat Key Transformation för att lägga till ett ökande nyckel värde till varje datarad. Detta är användbart när du skapar dimensions tabeller i en analys data modell med stjärn schema. I ett stjärn schema kräver varje medlem i dimensions tabellerna en unik nyckel som inte är en företags nyckel.

## <a name="configuration"></a>Konfiguration

![Transformering av surrogat nyckel](media/data-flow/surrogate.png "Transformering av surrogat nyckel")

**Nyckel kolumn:** Namnet på den genererade surrogat nyckel kolumnen.

**Start värde:** Det lägsta nyckel värde som ska genereras.

## <a name="increment-keys-from-existing-sources"></a>Öka nycklar från befintliga källor

Om du vill starta sekvensen från ett värde som finns i en källa, rekommenderar vi att du använder en cache-mottagare för att spara värdet och använder en härledd kolumn omvandling för att lägga till de två värdena tillsammans. Använd en cachelagrad sökning för att hämta utdata och lägga till den i den genererade nyckeln. Mer information finns i cache- [Sinks](data-flow-sink.md#cache-sink) och [cachelagrade sökningar](concepts-data-flow-expression-builder.md#cached-lookup).

![Ökning av surrogat nyckel](media/data-flow/cached-lookup-example.png "Ökning av surrogat nyckel")

### <a name="increment-from-existing-maximum-value"></a>Öka från det befintliga Max värdet

För att dirigera nyckelvärdet med föregående Max, finns det två tekniker som du kan använda baserat på var dina källdata finns.

#### <a name="database-sources"></a>Databas källor

Använd ett alternativ för SQL-fråga för att välja MAX () från källan. Till exempel `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Surrogat nyckel fråga](media/data-flow/surrogate-key-max-database.png "Omvandlings fråga för surrogat nyckel")

#### <a name="file-sources"></a>Fil källor

Om ditt föregående Max värde finns i en fil använder du `max()` funktionen i den sammanställda omvandlingen för att hämta det föregående Max värdet:

![Surrogat nyckel fil](media/data-flow/surrogate-key-max-file.png "Surrogat nyckel fil")

I båda fallen måste du skriva till en cache-mottagare och söka efter värdet. 


## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exempel

![Transformering av surrogat nyckel](media/data-flow/surrogate.png "Transformering av surrogat nyckel")

Data flödes skriptet för ovanstående surrogat nyckel konfiguration finns i kodfragmentet nedan.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Nästa steg

I de här exemplen används [kopplings](data-flow-join.md) -och [härledda kolumn](data-flow-derived-column.md) omvandlingar.
