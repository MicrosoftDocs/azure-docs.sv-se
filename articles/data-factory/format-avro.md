---
title: Avro-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar Avro-format i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392603"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro-format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Avro-filerna eller skriva data i Avro-format**. 

Avro-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Avro**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| avroCompressionCodec | Den komprimerings-codec som ska användas när du skriver till Avro-filer. Vid läsning från Avro-filer bestämmer Data Factory automatiskt komprimerings-codecen baserat på filens metadata.<br>De typer som stöds är "**none**" (standard),**DEFLATE**","**fästning**". Obs! kopierings aktiviteten stöder för närvarande inte fästfunktionen vid läsning/skrivning av Avro-filer. | Inga       |

> [!NOTE]
> Tomt utrymme i kolumn namn stöds inte för Avro-filer.

Nedan visas ett exempel på en Avro-datauppsättning på Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-källan och Sink.

### <a name="avro-as-source"></a>Avro som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* källa \**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **AvroSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Inga       |

### <a name="avro-as-sink"></a>Avro som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* mottagare \**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **AvroSink**. | Ja      |
| formatSettings          | En grupp med egenskaper. Se **Avro Write Settings** Table nedan.| Inga      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Inga       |

**Skriv inställningar för Avro** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **AvroWriteSettings**. | Ja                                                   |
| maxRowsPerFile | När du skriver data till en mapp kan du välja att skriva till flera filer och ange max rader per fil.  | Inga |
| fileNamePrefix | Gäller när `maxRowsPerFile` har kon figurer ATS.<br> Ange prefixet för fil namn när du skriver data till flera filer, vilket resulterade i det här mönstret: `<fileNamePrefix>_00000.<fileExtension>` . Om inget anges skapas prefixet för fil namn automatiskt. Den här egenskapen gäller inte när källan är filbaserad lagring eller [partition-alternativ-aktiverat data lager](copy-activity-performance-features.md).  | Inga |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

I mappa data flöden kan du läsa och skriva till Avro-format i följande data lager: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en Avro-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Jokertecken sökvägar | Alla filer som matchar sökvägen för jokertecken kommer att bearbetas. Åsidosätter mappen och fil Sök vägen som angetts i data uppsättningen. | nej | Sträng [] | wildcardPaths |
| Partitionens rot Sök väg | För fildata som är partitionerade kan du ange en rot Sök väg för partitionen för att kunna läsa partitionerade mappar som kolumner | nej | Sträng | partitionRootPath |
| Lista över filer | Om källan pekar på en textfil som visar en lista över filer som ska bearbetas | nej | `true` eller `false` | fileList |
| Kolumn som ska lagra fil namn | Skapa en ny kolumn med käll filens namn och sökväg | nej | Sträng | rowUrlColumn |
| Efter slut för ande | Ta bort eller flytta filerna efter bearbetning. Fil Sök vägen börjar från container roten | nej | Ta bort: `true` eller `false` <br> Fart `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Timestamp | modifiedAfter <br> modifiedBefore |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en Avro-mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Rensa mappen | Om målmappen rensas innan den skrivs | nej | `true` eller `false` | truncate |
| Fil namns alternativ | Namngivnings formatet för de data som skrivits. Som standard är en fil per partition i format `part-#####-tid-<guid>` | nej | Mönster: sträng <br> Per partition: sträng [] <br> Som data i kolumnen: sträng <br> Utdata till en enskild fil: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Offert alla | Omge alla värden i citat tecken | nej | `true` eller `false` | quoteAll |

## <a name="data-type-support"></a>Data typs stöd

### <a name="copy-activity"></a>Kopieringsaktivitet
Avro [komplexa data typer](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, unioner och fasta) i kopierings aktiviteten.

### <a name="data-flows"></a>Dataflöden
När du arbetar med Avro-filer i data flöden kan du läsa och skriva komplexa data typer, men se till att ta bort det fysiska schemat från data uppsättningen först. I data flöden kan du ange din logiska projektion och härleda kolumner som är komplexa strukturer och automatiskt mappa dessa fält till en Avro-fil.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
