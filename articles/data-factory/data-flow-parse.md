---
title: Parsa datatransformering i data flöde för mappning
description: Parsa inbäddade kolumn dokument
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710200"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Parsa omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd parse-omvandlingen för att parsa kolumner i dina data i dokument formulär. De aktuella typer av inbäddade dokument som stöds och som kan parsas är JSON och avgränsad text.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Konfiguration

I konfigurations panelen för att parsa omvandling väljer du först den typ av data som finns i de kolumner som du vill parsa infogade. Parse-transformeringen innehåller även följande konfigurations inställningar.

![Parsa inställningar](media/data-flow/data-flow-parse-1.png "Parsa")

### <a name="column"></a>Kolumn

Precis som härledda kolumner och mängder, är det här du ändrar en avslutande kolumn genom att välja den i list rutan. Eller så kan du skriva in namnet på en ny kolumn här. ADF kommer att lagra parsade källdata i den här kolumnen.

### <a name="expression"></a>Uttryck

Använd uttrycks verktyget för att ange källan för parsningen. Detta kan vara så enkelt som du bara väljer käll kolumnen med de data som du vill parsa, eller så kan du skapa komplexa uttryck som ska tolkas.

### <a name="output-column-type"></a>Typ av utdata kolumn

Här kan du konfigurera målets utgående schema från den parsning som ska skrivas till en enda kolumn.

![Exempel på analys](media/data-flow/data-flow-parse-2.png "Exempel på analys")

I det här exemplet har vi definierat parsning av det inkommande fältet "jsonString" som är oformaterad text, men formaterat som en JSON-struktur. Vi kommer att lagra de parsade resultaten som JSON i en ny kolumn med namnet "JSON" med det här schemat:

```(trade as boolean, customers as string[])```

Kontrol lera att resultatet är korrekt mappat genom att referera till fliken Granska och förhandsgranska data.

## <a name="examples"></a>Exempel

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Exempel

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Nästa steg

* Använd den [sammanslagna omvandlingen](data-flow-flatten.md) för att pivotera rader till kolumner.
* Använd [transformeringen härledd kolumn](data-flow-derived-column.md) för att pivotera kolumner till rader.
