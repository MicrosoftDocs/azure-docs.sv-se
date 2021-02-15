---
title: ORC-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar ORC-format i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 8973692b90cc9d6caa852616bf2962371d25abfa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386466"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC-format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Orc-filerna eller skriva data i Orc-format**. 

ORC-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Orc**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| compressionCodec         | Den komprimerings-codec som ska användas när du skriver till ORC-filer. Vid läsning från ORC-filer bestämmer data fabrikerna automatiskt komprimerings-codecen baserat på filens metadata.<br>De typer som stöds är **none**, **zlib**, **fästfunktionen** (default) och **LZO**. Obs! kopierings aktiviteten stöder för närvarande inte LZO vid läsning/skrivning av ORC-filer. | Inga      |

Nedan visas ett exempel på en ORC-datauppsättning på Azure Blob Storage:

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Observera följande punkter:

* Komplexa data typer (t. ex. MAP, LIST, STRUCT) stöds för närvarande bara i data flöden, inte i kopierings aktivitet. Om du vill använda komplexa typer i data flöden ska du inte importera filschemat i data uppsättningen och lämna schemat tomt i data uppsättningen. I käll omvandlingen importerar du sedan projektionen.
* Tomt utrymme i kolumn namnet stöds inte.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-källan och Sink.

### <a name="orc-as-source"></a>ORC som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* källa \**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **OrcSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Inga       |

### <a name="orc-as-sink"></a>ORC som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* mottagare \**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type för kopierings aktivitetens Sink måste anges till **OrcSink**. | Ja      |
| formatSettings | En grupp med egenskaper. Se **Orc Write Settings** Table nedan. |    Inga      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Inga       |

**Skriv inställningar för Orc** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **OrcWriteSettings**. | Ja                                                   |
| maxRowsPerFile | När du skriver data till en mapp kan du välja att skriva till flera filer och ange max rader per fil.  | Inga |
| fileNamePrefix | Gäller när `maxRowsPerFile` har kon figurer ATS.<br> Ange prefixet för fil namn när du skriver data till flera filer, vilket resulterade i det här mönstret: `<fileNamePrefix>_00000.<fileExtension>` . Om inget anges skapas prefixet för fil namn automatiskt. Den här egenskapen gäller inte när källan är filbaserad lagring eller [partition-alternativ-aktiverat data lager](copy-activity-performance-features.md).  | Inga |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

I mappa data flöden kan du läsa och skriva till ORC-format i följande data lager: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Du kan peka på ORC-filer antingen med hjälp av ORC-datauppsättning eller med en [infogad data uppsättning](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en ORC-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

När du använder en infogad data uppsättning visas ytterligare fil inställningar, som är samma som de egenskaper som beskrivs i avsnittet [Egenskaper för data mängd](#dataset-properties) .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `orc` | ja | `orc` | format |
| Jokertecken sökvägar | Alla filer som matchar sökvägen för jokertecken kommer att bearbetas. Åsidosätter mappen och fil Sök vägen som angetts i data uppsättningen. | nej | Sträng [] | wildcardPaths |
| Partitionens rot Sök väg | För fildata som är partitionerade kan du ange en rot Sök väg för partitionen för att kunna läsa partitionerade mappar som kolumner | nej | Sträng | partitionRootPath |
| Lista över filer | Om källan pekar på en textfil som visar en lista över filer som ska bearbetas | nej | `true` eller `false` | fileList |
| Kolumn som ska lagra fil namn | Skapa en ny kolumn med käll filens namn och sökväg | nej | Sträng | rowUrlColumn |
| Efter slut för ande | Ta bort eller flytta filerna efter bearbetning. Fil Sök vägen börjar från container roten | nej | Ta bort: `true` eller `false` <br> Fart `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Timestamp | modifiedAfter <br> modifiedBefore |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Käll exempel

Det associerade data flödes skriptet för en ORC-käll konfiguration är:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en ORC-mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

När du använder en infogad data uppsättning visas ytterligare fil inställningar, som är samma som de egenskaper som beskrivs i avsnittet [Egenskaper för data mängd](#dataset-properties) .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara `orc` | ja | `orc` | format |
| Rensa mappen | Om målmappen rensas innan den skrivs | nej | `true` eller `false` | truncate |
| Fil namns alternativ | Namngivnings formatet för de data som skrivits. Som standard är en fil per partition i format `part-#####-tid-<guid>` | nej | Mönster: sträng <br> Per partition: sträng [] <br> Som data i kolumnen: sträng <br> Utdata till en enskild fil: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Sink-exempel

Det associerade data flödes skriptet för en ORC Sink-konfiguration är:

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>Använda egen värd Integration Runtime

> [!IMPORTANT]
> Om du inte kopierar ORC-filer **som du har** befogenhet att kopiera med egen värd integration runtime, t. ex. mellan lokala och molnbaserade data lager, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller openjdk** och **Microsoft Visual C++ 2010 Redistributable Package** på din IR-dator. Mer information finns i följande stycke.

För kopiering som körs på egen värd-IR med ORC-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabeln *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och Ställ in system miljö variabeln JAVA_HOME därför.
- **Installera Visual c++ 2010 Redistributable Package**: Visual c++ 2010 Redistributable Package installeras inte med IR-installationer med egen värd. Du hittar den [här](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Om du kopierar data till/från ORC-format med hjälp av självbetjäning Integration Runtime och träffa fel som säger "ett fel uppstod vid anrop till Java, meddelande: **Java. lang. OutOfMemoryError: Java-heap-utrymme**", kan du lägga till en miljö variabel `_JAVA_OPTIONS` på den dator som är värd för den egna IR-enheten för att justera den minsta/högsta heap-storleken för JVM för att få en

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabeln `_JAVA_OPTIONS` med värde `-Xms256m -Xmx16g` . Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda maximalt `Xmx` mängd minne. Som standard använder ADF min 64 MB och Max 1G.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
