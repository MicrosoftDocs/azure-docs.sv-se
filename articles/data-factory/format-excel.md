---
title: Excel-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar Excel-formatet i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: bef29bc958253be0498442f842dda67105ce799b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386534"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Excel-filerna**. Azure Data Factory stöder både ". xls" och ". xlsx".

Excel-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md). Den stöds som källa men inte Sink. 

**Obs!** formatet ". xls" stöds inte när [http](connector-http.md)används. 

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Excel-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Excel**.   | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . | Ja      |
| Inställda SheetName        | Namnet på Excel-kalkylbladet för att läsa data.                       | Ange `sheetName` eller `sheetIndex` |
| sheetIndex | Excel-kalkylbladets index för att läsa data från 0. | Ange `sheetName` eller `sheetIndex` |
| intervall            | Cell intervallet i det aktuella kalkyl bladet för att hitta de selektiva data, t. ex.:<br>-Ej angiven: läser hela kalkyl bladet som en tabell från den första icke-tomma raden och kolumnen<br>- `A3`: läser en tabell som börjar från den aktuella cellen, identifierar dynamiskt alla rader nedan och alla kolumner till höger<br>- `A3:H5`: läser det här fasta intervallet som en tabell<br>- `A3:A3`: läser den här enkla cellen | Inga       |
| firstRowAsHeader | Anger om den första raden i angivet kalkyl blad/intervall ska behandlas som en rubrik rad med kolumn namn.<br>Tillåtna värden är **True** och **false** (standard). | Inga       |
| nullValue        | Anger sträng representationen för null-värde. <br>Standardvärdet är en **tom sträng**. | Inga       |
| komprimering | Grupp egenskaper för att konfigurera fil komprimering. Konfigurera det här avsnittet när du vill utföra komprimering/expandering under aktivitets körningen. | Inga |
| typ<br/>(*under `compression`*) | Komprimerings-codec som används för att läsa/skriva JSON-filer. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **TarGzip**, **tar**, **fästning** eller **lz4**. Standardvärdet är inte komprimerat.<br>**Obs!** kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate", "TarGzip" och "tar".<br>**Obs!** när du använder kopierings aktivitet för att expandera **ZipDeflate** -filer och skriva till filbaserat mottagar data lager, extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/` . | Nej.  |
| nivå<br/>(*under `compression`*) | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](/dotnet/api/system.io.compression.compressionlevel) . | Inga       |

Nedan visas ett exempel på en Excel-datauppsättning på Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Excel-källan.

### <a name="excel-as-source"></a>Excel som källa 

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* källa \**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **ExcelSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . | Inga       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

I data flöden för mappning kan du läsa Excel-formatet i följande data lager: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Du kan peka på Excel-filer antingen med Excel-datauppsättning eller med en [infogad data uppsättning](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en Excel-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** . När du använder en infogad data uppsättning visas ytterligare fil inställningar, som är samma som de egenskaper som beskrivs i avsnittet [Egenskaper för data mängd](#dataset-properties) .

| Name                      | Beskrivning                                                  | Krävs | Tillåtna värden                                            | Skript egenskap för data flöde         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Jokertecken sökvägar           | Alla filer som matchar sökvägen för jokertecken kommer att bearbetas. Åsidosätter mappen och fil Sök vägen som angetts i data uppsättningen. | nej       | Sträng []                                                  | wildcardPaths                     |
| Partitionens rot Sök väg       | För fildata som är partitionerade kan du ange en rot Sök väg för partitionen för att kunna läsa partitionerade mappar som kolumner | nej       | Sträng                                                    | partitionRootPath                 |
| Lista över filer             | Om källan pekar på en textfil som visar en lista över filer som ska bearbetas | nej       | `true` eller `false`                                         | fileList                          |
| Kolumn som ska lagra fil namn | Skapa en ny kolumn med käll filens namn och sökväg       | nej       | Sträng                                                    | rowUrlColumn                      |
| Efter slut för ande          | Ta bort eller flytta filerna efter bearbetning. Fil Sök vägen börjar från container roten | nej       | Ta bort: `true` eller `false` <br> Fart `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtrera efter senast ändrad   | Välj att filtrera filer baserat på när de senast ändrades | nej       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Käll exempel

Bilden nedan är ett exempel på en konfiguration av en Excel-källa i mappa data flöden med data uppsättnings läge.

![Excel-källa](media/data-flow/excel-source.png)

Det associerade data flödes skriptet är:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Om du använder en infogad data uppsättning visas följande käll alternativ i data flödet för att mappa data.

![Infogad Excel-datakälla](media/data-flow/excel-source-inline-dataset.png)

Det associerade data flödes skriptet är:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)