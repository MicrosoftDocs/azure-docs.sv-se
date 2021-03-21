---
title: XML-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar XML-format i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386364"
---
# <a name="xml-format-in-azure-data-factory"></a>XML-format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa XML-filerna**. 

XML-format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md). Den stöds som källa men inte Sink.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av XML-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **XML**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| encodingName     | Kodnings typen som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Inga       |
| nullValue | Anger sträng representationen för null-värde.<br/>Standardvärdet är en **tom sträng**. | Inga |
| komprimering | Grupp egenskaper för att konfigurera fil komprimering. Konfigurera det här avsnittet när du vill utföra komprimering/expandering under aktivitets körningen. | Inga |
| typ<br>(*under `compression`*) | Komprimerings-codec som används för att läsa/skriva XML-filer. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **TarGzip**, **tar**, **fästning** eller **lz4**. Standardvärdet är inte komprimerat.<br>**Obs!** kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate", "TarGzip" och "tar".<br>**Obs!** när du använder kopierings aktivitet för att expandera **ZipDeflate** / **TarGzip** / **tar** filer och skriver till filbaserat mottagar data lager som standardfiler extraheras till mappen: `<path specified in dataset>/<folder named as source compressed file>/` , använder `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` du på [kopierings aktivitets källan](#xml-as-source) för att kontrol lera om namnet på den eller de komprimerade filerna är kvar som mappstruktur. | Nej.  |
| nivå<br/>(*under `compression`*) | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](/dotnet/api/system.io.compression.compressionlevel) . | Inga       |

Nedan visas ett exempel på XML-datauppsättningen på Azure Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av XML-källan.

Lär dig mer om att mappa XML-data och ta bort data lager/format från [schema mappning](copy-activity-schema-and-type-mapping.md). När du för hands Grans kar XML-filer visas data med en JSON-hierarki och du använder JSON-sökvägen för att peka på fälten.

### <a name="xml-as-source"></a>XML som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\* källa \**** . Lär dig mer från [XML-kopplingens beteende](#xml-connector-behavior).

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **XmlSource**. | Ja      |
| formatSettings | En grupp med egenskaper. Läs tabellen **XML-läsa inställningar** nedan. | Inga       |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Inga       |

**XML-läsa inställningar** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typen för formatSettings måste anges till **XmlReadSettings**. | Ja      |
| validationMode | Anger om XML-schemat ska verifieras.<br>Tillåtna värden är **ingen** (standard, ingen validering), **XSD** (validate med XSD), **DTD** (validate using DTD). | Inga |
| namn områden | Om namn området ska aktive ras när XML-filerna parsas. Tillåtna värden är: **Sant** (standard), **falskt**. | Inga |
| namespacePrefixes | Namn områdes-URI till prefix-mappning, som används för att namnge fält när XML-filen parsas.<br/>Om en XML-fil har namnrymd och namn område är aktiverat som standard är fält namnet detsamma som i XML-dokumentet.<br>Om ett objekt har definierats för namn områdets URI i den här kartan är fält namnet `prefix:fieldName` . | Inga |
| detectDataType | Anger om heltal, dubbla och booleska data typer ska identifieras. Tillåtna värden är: **Sant** (standard), **falskt**.| Inga |
| compressionProperties | En grupp egenskaper för hur man dekomprimerar data för en angiven komprimerings-codec. | Inga       |
| preserveZipFileNameAsFolder<br>(*under `compressionProperties` -> `type` som `ZipDeflateReadSettings`*)  | Gäller när indata-dataset konfigureras med **ZipDeflate** -komprimering. Anger om käll filens zip-filnamn ska bevaras som mappstruktur under kopieringen.<br>-Om värdet är **true (standard)**, Data Factory skriver zippade filer till `<path specified in dataset>/<folder named as source zip file>/` .<br>– Om värdet är **false** skriver data Factory zippade filer direkt till `<path specified in dataset>` . Se till att du inte har dubbla fil namn i olika käll-zip-filer för att undvika racing eller oväntat beteende.  | Inga |
| preserveCompressionFileNameAsFolder<br>(*under `compressionProperties` -> `type` som `TarGZipReadSettings` eller `TarReadSettings`*) | Gäller när indata-dataset konfigureras med **TarGzip** / **tar** komprimering. Anger om du vill bevara det komprimerade fil namnet för källan som mappstruktur under kopieringen.<br>– När värdet är **true (standard)**, Data Factory skriver expanderade filer till `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Om det är inställt på **false** Data Factory skriver expanderade filer direkt till `<path specified in dataset>` . Se till att du inte har dubbla fil namn i olika källfiler för att undvika racing eller oväntat beteende. | Inga |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

I mappa data flöden kan du läsa och skriva till XML-format i följande data lager: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Du kan peka på XML-filer antingen med hjälp av XML-datauppsättning eller med en [infogad data uppsättning](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en XML-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** . Lär dig mer från [XML-kopplingens beteende](#xml-connector-behavior). När du använder en infogad data uppsättning visas ytterligare fil inställningar, som är samma som de egenskaper som beskrivs i avsnittet [Egenskaper för data mängd](#dataset-properties) . 

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Jokertecken sökvägar | Alla filer som matchar sökvägen för jokertecken kommer att bearbetas. Åsidosätter mappen och fil Sök vägen som angetts i data uppsättningen. | Inga | Sträng [] | wildcardPaths |
| Partitionens rot Sök väg | För fildata som är partitionerade kan du ange en rot Sök väg för partitionen för att kunna läsa partitionerade mappar som kolumner | Inga | Sträng | partitionRootPath |
| Lista över filer | Om källan pekar på en textfil som visar en lista över filer som ska bearbetas | Inga | `true` eller `false` | fileList |
| Kolumn som ska lagra fil namn | Skapa en ny kolumn med käll filens namn och sökväg | Inga | Sträng | rowUrlColumn |
| Efter slut för ande | Ta bort eller flytta filerna efter bearbetning. Fil Sök vägen börjar från container roten | Inga | Ta bort: `true` eller `false` <br> Fart `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | Inga | Timestamp | modifiedAfter <br>modifiedBefore |
| Validerings läge | Anger om XML-schemat ska verifieras. | Inga | `None` (standard, ingen verifiering)<br>`xsd` (validera med XSD)<br>`dtd` (verifiera med hjälp av DTD). | validationMode |
| Namnrymder | Om namn området ska aktive ras när XML-filerna parsas. | Inga | `true` (standard) eller `false` | namn områden |
| Par av namnområdesprefix | Namn områdes-URI till prefix-mappning, som används för att namnge fält när XML-filen parsas.<br/>Om en XML-fil har namnrymd och namn område är aktiverat som standard är fält namnet detsamma som i XML-dokumentet.<br>Om ett objekt har definierats för namn områdets URI i den här kartan är fält namnet `prefix:fieldName` . | Inga | Matris med mönster`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Skript exempel för XML-källa

Skriptet nedan är ett exempel på en konfiguration av XML-källa i mappa data flöden med data uppsättnings läge.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Skriptet nedan är ett exempel på en konfiguration av en XML-källa med hjälp av inline dataset-läge.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Beteende för XML-koppling

Observera följande när du använder XML som källa.

- XML-attribut:

    - Attribut för ett element parsas som del fält för elementet i hierarkin.
    - Namnet på fältet attribut följer mönstret `@attributeName` .

- Verifiering av XML-schema:

    - Du kan välja att inte validera schemat, eller verifiera schemat med XSD eller DTD.
    - När du använder XSD eller DTD för att validera XML-filer, måste XSD/DTD anges i XML-filerna via relativ sökväg.

- Namn områdes hantering:

    - Namn området kan inaktive ras när data flödet används, och de attribut som definierar namn området kommer att tolkas som normala attribut.
    - När namn området är aktiverat följer namnet på elementet och attributen mönstret       `namespaceUri,elementName` och `namespaceUri,@attributeName` som standard. Du kan definiera namnområdesprefix för varje namnområdes-URI i källan, där namnen på elementet och attributen följer mönstret `definedPrefix:elementName` eller `definedPrefix:@attributeName` i stället.

- Värde kolumn:

    - Om ett XML-element har både enkla text värden och attribut/underordnade element, parsas det enkla textvärdet som värde för en "värde kolumn" med det inbyggda fält namnet `_value_` . Och den ärver även namn området för elementet även om det tillämpas.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)