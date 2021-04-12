---
title: Delta format i Azure Data Factory
description: Transformera och flytta data från en delta Lake med delta formatet
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: weetok
ms.openlocfilehash: 6d9d2b0d185750cf8ed8192661f28a2b82d88b78
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222546"
---
# <a name="delta-format-in-azure-data-factory"></a>Delta format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du kopierar data till och från en delta Lake som lagras i [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) eller [Azure Blob Storage](connector-azure-blob-storage.md) med delta formatet. Den här kopplingen är tillgänglig som en [infogad data uppsättning](data-flow-source.md#inline-datasets) i mappa data flöden som både en källa och en mottagare.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Den här kopplingen är tillgänglig som en [infogad data uppsättning](data-flow-source.md#inline-datasets) i mappa data flöden som både en källa och en mottagare.

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en delta källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `delta` | ja | `delta` | format |
| Filsystem | Container/File-systemet för delta Lake | ja | Sträng | Fil Systems |
| Mappsökväg | Direct of delta sjö | ja | Sträng | folderPath |
| Komprimerings typ | Den komprimerings typ som tillhör delta tabellen | nej | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Komprimerings nivå | Välj om komprimeringen ska slutföras så fort som möjligt eller om den resulterande filen ska komprimeras optimalt. | krävs om `compressedType` har angetts. | `Optimal` eller `Fastest` | compressionLevel |
| Res tid | Välj om du vill fråga en äldre ögonblicks bild av en delta tabell | nej | Fråga efter tidsstämpel: tidsstämpel <br> Fråga efter version: heltal | timestampAsOf <br> versionAsOf |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importera schema

Delta är endast tillgängligt som en infogad data uppsättning och har som standard inte ett associerat schema. Hämta kolumnens metadata genom att klicka på knappen **Importera schema** på fliken **projektion** . Detta gör att du kan referera till kolumn namn och data typer som anges av sökkorpus. Om du vill importera schemat måste en [fel söknings session för data flöde](concepts-data-flow-debug-mode.md) vara aktiv och du måste ha en befintlig definitions fil för common data service för att peka på.
 

### <a name="delta-source-script-example"></a>Skript exempel för delta källa

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en delta mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `delta` | ja | `delta` | format |
| Filsystem | Container/File-systemet för delta Lake | ja | Sträng | Fil Systems |
| Mappsökväg | Direct of delta sjö | ja | Sträng | folderPath |
| Komprimerings typ | Den komprimerings typ som tillhör delta tabellen | nej | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| Komprimerings nivå | Välj om komprimeringen ska slutföras så fort som möjligt eller om den resulterande filen ska komprimeras optimalt. | krävs om `compressedType` har angetts. | `Optimal` eller `Fastest` | compressionLevel |
| Vakuum | Ange tröskelvärde för kvarhållning i timmar för äldre versioner av tabell. Värdet 0 eller lägre är 30 dagar | ja | Integer | Dammsug |
| Uppdaterings metod | Ange vilka uppdaterings åtgärder som tillåts på delta Lake. För metoder som inte infogas krävs en föregående Alter Row-omvandling för att markera rader. | ja | `true` eller `false` | bort <br> infognings bara <br> uppdaterings bara <br> merge |
| Optimerad skrivning | Uppnå högre data flöde för Skriv åtgärd genom att optimera intern blandning i Spark-körningar. Det innebär att du kan märka färre partitioner och filer som har större storlek | nej | `true` eller `false` | optimizedWrite: sant |
| Komprimera automatiskt | När en Skriv åtgärd har slutförts kommer Spark automatiskt att köra ```OPTIMIZE``` kommandot för att organisera om data, vilket resulterar i fler partitioner om det behövs, för bättre Läs prestanda i framtiden | nej | `true` eller `false` |    autokomprimera: sant |

### <a name="delta-sink-script-example"></a>Skript exempel för delta mottagare

Det associerade data flödes skriptet är:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Kända begränsningar

När du skriver till en delta-mottagare är det en känd begränsning där antalet rader som skrivs inte returneras i övervaknings resultatet.

## <a name="next-steps"></a>Nästa steg

* Skapa en [käll omvandling](data-flow-source.md) i mappnings data flödet.
* Skapa en [Sink-transformering](data-flow-sink.md) i mappnings data flödet.
* Skapa en [Alter Row-omvandling](data-flow-alter-row.md) för att markera rader som INSERT, Update, upsert eller Delete.
