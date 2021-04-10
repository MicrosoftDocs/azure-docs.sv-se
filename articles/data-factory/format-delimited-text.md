---
title: Avgränsat text format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar avgränsat text format i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: daf3691b48f7bf12e9ef51de7d4253dad9dbd2b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026878"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Avgränsat text format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa de avgränsade textfilerna eller skriva data i avgränsat text format**. 

Avgränsat text format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av den avgränsade text data uppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **DelimitedText**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` .  | Ja      |
| columnDelimiter  | De/tecknen som används för att avgränsa kolumner i en fil. <br>Standardvärdet är **kommatecken `,`**. När kolumn avgränsaren definieras som en tom sträng, vilket innebär att ingen avgränsare görs, tas hela raden som en enda kolumn.<br>För närvarande stöds inte kolumn avgränsare som tom sträng eller flera tecken för att mappa data flöde, men inte kopierings aktivitet.  | Inga       |
| rowDelimiter     | Det enstaka tecknen eller "\r\n" som används för att avgränsa rader i en fil. <br>Standardvärdet är något av följande värden **vid läsning: ["\r\n", "\r", "\n"]** och **"\n" eller "\r\n" vid skrivning** genom att mappa data flöde och kopiera aktivitet. <br>När rad avgränsaren har angetts till ingen avgränsare (tom sträng), måste kolumn avgränsaren anges som ingen avgränsare (tom sträng), vilket innebär att hela innehållet ska behandlas som ett enda värde.<br>För närvarande stöds inte rad avgränsare som en tom sträng för data flöde för mappning, men inte kopierings aktivitet. | Inga       |
| quoteChar        | Det enkla tecknet för att citera kolumn värden om det innehåller kolumn avgränsare. <br>Standardvärdet är **dubbla citat tecken** `"` . <br>När `quoteChar` definieras som en tom sträng innebär det att det inte finns något citat tecken och kolumnvärdet inte är citerat och `escapeChar` används för att undanta kolumn avgränsaren och sig själv. | Inga       |
| escapeChar       | Det enda tecken som ska Escape-citat innanför ett citerat värde.<br>Standardvärdet är **omvänt snedstreck `\`**. <br>När `escapeChar` har definierats som en tom sträng `quoteChar` måste du även ange en tom sträng, och i så fall se till att alla kolumn värden inte innehåller avgränsare. | Inga       |
| firstRowAsHeader | Anger om du vill behandla/skapa den första raden som en rubrik rad med kolumn namn.<br>Tillåtna värden är **True** och **false** (standard).<br>När första raden som rubrik är falskt, antecknings gränssnittet för för hands versions-och Sök efter-aktivitet genererar automatiskt kolumn namn som Prop_ {n} (från 0), kopierings aktiviteten kräver [explicit mappning](copy-activity-schema-and-type-mapping.md#explicit-mapping) från källan till Sink och letar upp kolumner efter ordnings tal (från och med 1) och visar kolumner med namnet som Column_ {n} (från 1).  | Inga       |
| nullValue        | Anger sträng representationen för null-värde. <br>Standardvärdet är en **tom sträng**. | Inga       |
| encodingName     | Kodnings typen som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Data flödet för antecknings mappning stöder inte UTF-7-kodning. | Inga       |
| compressionCodec | Komprimerings-codec som används för att läsa/skriva textfiler. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **TarGzip**, **tar**, **fästning** eller **lz4**. Standardvärdet är inte komprimerat. <br>**Obs!** kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate", "TarGzip" och "tar". <br>**Obs!** när du använder kopierings aktivitet för att expandera **ZipDeflate** / **TarGzip** / **tar** filer och skriver till filbaserat mottagar data lager som standardfiler extraheras till mappen: `<path specified in dataset>/<folder named as source compressed file>/` , använder `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` du på [kopierings aktivitets källan](#delimited-text-as-source) för att kontrol lera om namnet på den eller de komprimerade filerna är kvar som mappstruktur. | Inga       |
| compressionLevel | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](/dotnet/api/system.io.compression.compressionlevel) . | Inga       |

Nedan visas ett exempel på en avgränsad text uppsättning på Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av avgränsad text källa och mottagare.

### <a name="delimited-text-as-source"></a>Avgränsad text som källa 

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* källa \**** .

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSource**. | Ja      |
| formatSettings | En grupp med egenskaper. Läs **Inställningar för avgränsad text** i tabellen nedan. |  Inga       |
| storeSettings  | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . | Inga       |

**Läs inställningar för avgränsad text** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextReadSettings**. | Ja      |
| skipLineCount | Anger antalet **icke-tomma** rader som ska hoppas över vid läsning av data från indatafiler. <br>Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. | Inga       |
| compressionProperties | En grupp egenskaper för hur man dekomprimerar data för en angiven komprimerings-codec. | Inga       |
| preserveZipFileNameAsFolder<br>(*under `compressionProperties` -> `type` som `ZipDeflateReadSettings`*) |  Gäller när indata-dataset konfigureras med **ZipDeflate** -komprimering. Anger om käll filens zip-filnamn ska bevaras som mappstruktur under kopieringen.<br>-Om värdet är **true (standard)**, Data Factory skriver zippade filer till `<path specified in dataset>/<folder named as source zip file>/` .<br>– Om värdet är **false** skriver data Factory zippade filer direkt till `<path specified in dataset>` . Se till att du inte har dubbla fil namn i olika käll-zip-filer för att undvika racing eller oväntat beteende.  | Inga |
| preserveCompressionFileNameAsFolder<br>(*under `compressionProperties` -> `type` som `TarGZipReadSettings` eller `TarReadSettings`*)  | Gäller när indata-dataset konfigureras med **TarGzip** / **tar** komprimering. Anger om du vill bevara det komprimerade fil namnet för källan som mappstruktur under kopieringen.<br>– När värdet är **true (standard)**, Data Factory skriver expanderade filer till `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Om det är inställt på **false** Data Factory skriver expanderade filer direkt till `<path specified in dataset>` . Se till att du inte har dubbla fil namn i olika källfiler för att undvika racing eller oväntat beteende. | Inga |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Avgränsad text som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* mottagare \**** .

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSink**. | Ja      |
| formatSettings | En grupp med egenskaper. Se tabellen med **avgränsade text skriv inställningar** nedan. |    Inga      |
| storeSettings  | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings` .  | Inga       |

**Inställningar för avgränsad text** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextWriteSettings**. | Ja                                                   |
| fileExtension | Fil namns tillägget som används för att namnge utdatafilerna, till exempel, `.csv` `.txt` . Det måste anges när `fileName` har inte angetts i data uppsättningen utgående DelimitedText. När fil namnet har kon figurer ATS i data uppsättningen för utdata används den som mottagar fil namn och fil namns inställningen ignoreras.  | Ja när fil namnet inte anges i data uppsättningen för utdata |
| maxRowsPerFile | När du skriver data till en mapp kan du välja att skriva till flera filer och ange max rader per fil.  | Inga |
| fileNamePrefix | Gäller när `maxRowsPerFile` har kon figurer ATS.<br> Ange prefixet för fil namn när du skriver data till flera filer, vilket resulterade i det här mönstret: `<fileNamePrefix>_00000.<fileExtension>` . Om inget anges skapas prefixet för fil namn automatiskt. Den här egenskapen gäller inte när källan är filbaserad lagring eller [partition-alternativ-aktiverat data lager](copy-activity-performance-features.md).  | Inga |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

I mappa data flöden kan du läsa och skriva till avgränsat text format i följande data lager: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en avgränsad text källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Jokertecken sökvägar | Alla filer som matchar sökvägen för jokertecken kommer att bearbetas. Åsidosätter mappen och fil Sök vägen som angetts i data uppsättningen. | nej | Sträng [] | wildcardPaths |
| Partitionens rot Sök väg | För fildata som är partitionerade kan du ange en rot Sök väg för partitionen för att kunna läsa partitionerade mappar som kolumner | nej | Sträng | partitionRootPath |
| Lista över filer | Om källan pekar på en textfil som visar en lista över filer som ska bearbetas | nej | `true` eller `false` | fileList |
| Flera rader | Innehåller käll filen rader som sträcker sig över flera rader. Multiline-värden måste vara citat tecken. | Nej `true` eller `false` | multiLineRow |
| Kolumn som ska lagra fil namn | Skapa en ny kolumn med käll filens namn och sökväg | nej | Sträng | rowUrlColumn |
| Efter slut för ande | Ta bort eller flytta filerna efter bearbetning. Fil Sök vägen börjar från container roten | nej | Ta bort: `true` eller `false` <br> Fart `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Timestamp | modifiedAfter <br> modifiedBefore |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Käll exempel

Bilden nedan är ett exempel på en avgränsad text käll konfiguration i mappnings data flöden.

![DelimitedText-källa](media/data-flow/delimited-text-source.png)

Det associerade data flödes skriptet är:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Data flödes källor har stöd för en begränsad uppsättning Linux-globbing som stöds av Hadoop-filsystem

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en avgränsad text mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Rensa mappen | Om målmappen rensas innan den skrivs | nej | `true` eller `false` | truncate |
| Fil namns alternativ | Namngivnings formatet för de data som skrivits. Som standard är en fil per partition i format `part-#####-tid-<guid>` | nej | Mönster: sträng <br> Per partition: sträng [] <br> Namn fil som kolumn data: sträng <br> Utdata till en enskild fil: `['<fileName>']` <br> Namnge mapp som kolumn data: sträng | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| Offert alla | Omge alla värden i citat tecken | nej | `true` eller `false` | quoteAll |

rowFolderUrlColumn:

### <a name="sink-example"></a>Sink-exempel

Bilden nedan är ett exempel på en avgränsad text Sink-konfiguration i mappa data flöden.

![DelimitedText-mottagare](media/data-flow/delimited-text-sink.png)

Det associerade data flödes skriptet är:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
