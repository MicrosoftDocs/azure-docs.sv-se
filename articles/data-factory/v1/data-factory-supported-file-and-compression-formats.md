---
title: Fil-och komprimerings format i Azure Data Factory
description: Lär dig mer om de fil format som stöds av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7b53d9cd64c50d8305714878324dd355eb6d1840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368735"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Fil-och komprimerings format som stöds av Azure Data Factory
*Det här avsnittet gäller för följande anslutningar: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), [Azure Data Lake Store](data-factory-azure-datalake-connector.md), [File System](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [http](data-factory-http-connector.md)och [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [fil format och komprimerings-codecar som stöds i Data Factory](../supported-file-formats-and-compression-codecs.md).

Azure Data Factory stöder följande fil format typer:

* [Text format](#text-format)
* [JSON-format](#json-format)
* [Avro-format](#avro-format)
* [ORC-format](#orc-format)
* [Parquet-format](#parquet-format)

## <a name="text-format"></a>Text format
Om du vill läsa från en textfil eller skriva till en textfil anger du `type` egenskapen i `format` avsnittet i data uppsättningen till text format. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [TextFormat-exempel](#textformat-example).

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| columnDelimiter |Det tecken som används för att avgränsa kolumner i en fil. Du kan överväga att använda ett sällsynt icke utskrivbart tecken som kanske inte finns i dina data. Ange till exempel "\u0001" som representerar början av rubrik (SOH). |Endast ett tecken är tillåtet. **Standardvärdet** är **kommatecken (,)**. <br/><br/>Om du vill använda ett Unicode-tecken läser du [Unicode-tecken](https://en.wikipedia.org/wiki/List_of_Unicode_characters) för att hämta motsvarande kod för den. |Inga |
| rowDelimiter |Det tecken som används för att avgränsa rader i en fil. |Endast ett tecken är tillåtet. **Standardvärdet** är något av följande värden vid läsning: **["\r\n", "\r", "\n"]** och **"\r\n"** vid skrivning. |Inga |
| escapeChar |Det specialtecken som används för att undanta en kolumnavgränsare i innehållet i indatafilen. <br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Exempel: om du har kommatecken (,) som kolumn avgränsare, men vill ha ett kommatecken i texten (till exempel: "Hello, World"), kan du definiera "$" som escape-tecken och använda strängen "Hej $, World" i källan. |Inga |
| quoteChar |Det tecken som används för att referera till ett strängvärde. Kolumn- och radavgränsarna innanför citattecknen behandlas som en del av strängvärdet. Den här egenskapen gäller både in- och utdatauppsättningar.<br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Om du till exempel använder kommatecken (,) som kolumnavgränsare, men vill använda ett kommatecken i texten (till exempel <Hello, world>), kan du definiera " (dubbla citattecken) som citattecknet och använda strängen "Hello, world" i källan. |Inga |
| nullValue |Ett eller flera tecken som används för att representera ett null-värde. |Ett eller flera tecken. **Standardvärdena** är **"\N" och "NULL"** vid läsning och **"\N"** vid skrivning. |Inga |
| encodingName |Ange kodningsnamnet. |Ett giltigt kodningsnamn. Se [Egenskapen Encoding.EncodingName](/dotnet/api/system.text.encoding). Exempel: windows-1250 or shift_jis. **Standardvärdet** är **UTF-8**. |Inga |
| firstRowAsHeader |Anger om den första raden ska behandlas som en rubrik. För en indatauppsättning läser Data Factory den första raden som en rubrik. För en utdatauppsättning skriver Data Factory den första raden som en rubrik. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Sant<br/><b>False (standard)</b> |Inga |
| skipLineCount |Anger hur många rader som ska hoppas över vid läsning av data från indatafiler. Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Inga |
| treatEmptyAsNull |Anger om du vill hantera null-strängar eller tomma strängar som ett null-värde vid läsning av data från en indatafil. |**True (standard)**<br/>Falskt |Inga |

### <a name="textformat-example"></a>TextFormat-exempel
I följande JSON-definition för en data uppsättning anges några av de valfria egenskaperna.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Om du vill använda ett `escapeChar` i stället för `quoteChar` ersätter du raden med `quoteChar` med följande escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Användningsscenarier för firstRowAsHeader och skipLineCount
* Du kopierar från en källa som inte är filbaserad till en textfil och vill lägga till en rubrikrad som innehåller schemametadata (till exempel: SQL-schema). Ange `firstRowAsHeader` som true i utdatauppsättningen för det här scenariot.
* Du kopierar från en textfil som innehåller en rubrikrad till en mottagare som inte är filbaserad och vill ignorera raden. Ange `firstRowAsHeader` som true i indatauppsättningen.
* Du kopierar från en textfil och vill hoppa över några rader i början som antingen inte innehåller några data eller som innehåller rubrikinformation. Ange `skipLineCount` för att ange antalet rader som ska hoppas över. Om resten av filen innehåller en rubrikrad kan du också ange `firstRowAsHeader`. Om både `skipLineCount` och `firstRowAsHeader` anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen

## <a name="json-format"></a>JSON-format
För att **Importera/exportera en JSON-fil som-är i/från Azure Cosmos DB**, se avsnittet [Importera/exportera JSON-dokument](data-factory-azure-documentdb-connector.md#importexport-json-documents) i [flytta data till/från Azure Cosmos DB](data-factory-azure-documentdb-connector.md) artikeln.

Om du vill parsa JSON-filerna eller skriva data i JSON-format anger du `type` egenskapen i `format` avsnittet till **JsonFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example).

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| filePattern |Ange mönstret för de data som lagras i varje JSON-fil. Tillåtna värden är: **setOfObjects** och **arrayOfObjects**. **Standardvärdet** är **setOfObjects**. Detaljerad information om dessa mönster finns i avsnittet om [JSON-filmönster](#json-file-patterns). |Inga |
| jsonNodeReference | Om du vill iterera och extrahera data från objekten i ett matrisfält med samma mönster anger du JSON-sökvägen för matrisen. Den här egenskapen stöds endast när du kopierar data från JSON-filer. | Inga |
| jsonPathDefinition | Ange JSON-sökvägsuttrycket för varje kolumnmappning med ett anpassat kolumnnamn (inled med liten bokstav). Den här egenskapen stöds endast när du kopierar data från JSON-filer, och du kan extrahera data från objekt eller matriser. <br/><br/> För fält under rotobjektet börjar du med $; för fält inuti matrisen som väljs av egenskapen `jsonNodeReference` börjar du från matriselementet. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example). | Inga |
| encodingName |Ange kodningsnamnet. En lista över giltiga kodningsnamn finns i avsnittet om egenskapen [Encoding.EncodingName](/dotnet/api/system.text.encoding). Exempel: windows-1250 or shift_jis. **Standardvärdet** är: **UTF-8**. |Inga |
| nestingSeparator |Tecken som används för att avgränsa kapslingsnivåer. Standardvärdet är ”.” (punkt). |Inga |

### <a name="json-file-patterns"></a>JSON-filmönster

Kopierings aktiviteten kan parsa följande mönster för JSON-filer:

- **Typ I: setOfObjects**

    Varje fil som innehåller ett enskilt objekt eller flera radavgränsade/sammanfogade objekt. När det här alternativet väljs i en utdatauppsättning genererar kopieringsaktiviteten en enskild JSON-fil med ett objekt per rad (radavgränsade).

    * **Exempel på JSON med enskilda objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exempel med radavgränsad JSON**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exempel med sammanfogad JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Varje fil innehåller en matris med objekt.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat-exempel

**Fall 1: Kopiera data från JSON-filer**

Se följande två exempel när du kopierar data från JSON-filer. Allmänna punkter att Observera:

**Exempel 1: hämta data från objektet och matrisen**

I det här exemplet mappas ett JSON-rotobjekt till en enskild post i tabellformat. Om du har en JSON-fil med följande innehåll:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
och du vill kopiera den till en Azure SQL-tabell i följande format, genom att extrahera data från både objekten och matrisen:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i avsnittet [Mappa käll data uppsättnings kolumner till kolumner med mål data uppsättningar](data-factory-map-columns.md) .
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. Om du vill kopiera data från matrisen kan du använda **matris [x]. Property** för att extrahera värdet för den aktuella egenskapen från XTH-objektet, eller så kan du använda **matris [*]. Property** för att hitta värdet från alla objekt som innehåller egenskapen.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Exempel 2: korstillämpa flera objekt med samma mönster från en matris**

I det här exemplet omvandlas ett JSON-rotobjekt till flera poster i tabellform. Om du har en JSON-fil med följande innehåll:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
som du vill kopiera till en Azure SQL-tabell i följande format genom att förenkla data i matrisen och korskoppla med den gemensamma rotinformationen:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i avsnittet [Mappa käll data uppsättnings kolumner till kolumner med mål data uppsättningar](data-factory-map-columns.md) .
- `jsonNodeReference` anger att data ska itereras och extraheras från objekten med samma mönster under **matrisens** orderLines.
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. I det här exemplet finns ”ordernumber”, ”orderdate” och ”city” under rotobjektet med JSON-sökvägen som börjar med ”$.”, medan ”order_pd” och ”order_price” definieras med sökvägen från matriselementet utan ”$.”.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Observera följande punkter:**

* Om `structure` och `jsonPathDefinition` inte har definierats i Data Factory-datauppsättningen identifierar kopieringsaktiviteten schemat från det första objektet och förenklar hela objektet.
* Om JSON-indata har en matris konverterar kopieringsaktiviteten som standard hela matrisvärdet till en sträng. Du kan välja att extrahera data från den med hjälp av `jsonNodeReference` och/eller `jsonPathDefinition`, eller hoppa över det genom att inte ange den i `jsonPathDefinition`.
* Om det finns dubblettnamn på samma nivå väljer kopieringsaktiviteten det sista.
* Egenskapsnamn är skiftlägeskänsliga. Två egenskaper med samma namn men med olika skiftlägen behandlas som två olika egenskaper.

**Fall 2: Skriva data till JSON-fil**

Om du har följande tabell i SQL Database:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

och för varje post förväntar du dig att skriva till ett JSON-objekt i följande format:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Utdatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt `structure` definierar avsnittet anpassade egenskaps namn i målfilen, `nestingSeparator` (Standardvärdet är ".") används för att identifiera kapslings lagret från namnet. Det här avsnittet är **valfritt** såvida du inte vill ändra egenskapsnamnet som jämförs med källkolumnnamnet, eller kapsla vissa av egenskaperna.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="avro-format"></a>AVRO-format
Om du vill parsa Avro-filerna eller skriva data i Avro-format anger du egenskapen `format` `type` till **AvroFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "AvroFormat",
}
```

Om du vill använda Avro-format i en Hive-tabell kan du läsa [Apache Hive själv studie kursen](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observera följande punkter:  

* [Komplexa data typer](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, unioner och fasta).

## <a name="orc-format"></a>ORC-format
Om du vill parsa ORC-filerna eller skriva data i ORC-format ange du egenskapen `format` `type` till **OrcFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Om du inte kopierar ORC-filer **som de är** mellan lokala och molnbaserade datalager måste du installera JRE 8 (Java Runtime Environment) på din gateway-dator. En 64-bitars gateway kräver 64-bitars JRE och en 32-bitars gateway kräver 32-bitars JRE. Du hittar båda versionerna [här](https://go.microsoft.com/fwlink/?LinkId=808605). Välj lämplig version.
>
>

Observera följande punkter:

* Komplexa datatyper stöds inte (STRUCT, MAP, LIST, UNION)
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

## <a name="parquet-format"></a>Parquet-format
Om du vill parsa Parquet-filer eller skriva data i Parquet-format anger du egenskapen `format` `type` till **ParquetFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Om du inte kopierar Parquet-filer **som de är** mellan lokala och molnbaserade datalager måste du installera JRE 8 (Java Runtime Environment) på din gateway-dator. En 64-bitars gateway kräver 64-bitars JRE och en 32-bitars gateway kräver 32-bitars JRE. Du hittar båda versionerna [här](https://go.microsoft.com/fwlink/?LinkId=808605). Välj lämplig version.
>
>

Observera följande punkter:

* Komplexa datatyper stöds inte (MAP, LIST)
* Parquet-filer har följande komprimeringsrelaterade alternativ: NONE, SNAPPY, GZIP och LZO. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en Parquet-fil väljer Data Factory SNAPPY, som är standard för Parquet-formatet. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

## <a name="compression-support"></a>Stöd för komprimering
Bearbetning av stora data uppsättningar kan orsaka I/O-och nätverks Flask halsar. Komprimerade data i butikerna kan därför inte bara påskynda data överföringen i nätverket och spara disk utrymme, men ger också betydande prestanda förbättringar för bearbetning av Big data. För närvarande stöds komprimering för filbaserade data lager, till exempel Azure Blob eller lokalt fil system.  

Om du vill ange komprimering för en data uppsättning använder du **komprimerings** egenskapen i data uppsättnings-JSON som i följande exempel:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Anta att exempel data uppsättningen används som utdata för en kopierings aktivitet, komprimerar utdata med GZIP-codec med optimalt förhållande och skriver sedan komprimerade data i en fil med namnet pagecounts.csv. gz i Azure Blob Storage.

> [!NOTE]
> Komprimerings inställningar stöds inte för data i **AvroFormat**, **OrcFormat** eller **ParquetFormat**. När du läser filer i dessa format kan Data Factory identifiera och använda komprimerings-codecen i metadata. När du skriver till filer i dessa format, väljer Data Factory Standard-komprimerings-codec för formatet. Till exempel ZLIB för OrcFormat och fästning för ParquetFormat.   

**Komprimerings** avsnittet har två egenskaper:  

* **Typ:** komprimerings-codecen, som kan vara **gzip**, **DEFLATE**, **bzip2** eller **ZipDeflate**.  
* **Nivå:** komprimerings förhållandet, som kan vara **optimalt** eller **snabbast**.

  * **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.
  * **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra.

    Mer information finns i avsnittet [komprimerings nivå](/dotnet/api/system.io.compression.compressionlevel) .

När du anger `compression` en egenskap i en JSON för indata-datauppsättning kan pipelinen läsa komprimerade data från källan. När du anger egenskapen i en datauppsättnings-JSON för utdata kan kopierings aktiviteten skriva komprimerade data till målet. Här följer några exempel scenarier:

* Läs GZIP-komprimerade data från en Azure-Blob, expandera den och skriv resultat data till Azure SQL Database. Du definierar indata för Azure Blob-datauppsättningen med `compression` `type` JSON-egenskapen som gzip.
* Läs data från en oformaterad textfil från det lokala fil systemet, komprimera den med GZip-format och skriv komprimerade data till en Azure-blob. Du definierar en data uppsättning för Azure Blob-utdata med `compression` `type` JSON-egenskapen som gzip.
* Läs. zip-fil från FTP-server, expandera den för att hämta filerna i och landa filerna i Azure Data Lake Store. Du definierar en inkommande FTP-datauppsättning med `compression` `type` JSON-egenskapen som ZipDeflate.
* Läs en GZIP-komprimerad data från en Azure-Blob, komprimera den, komprimera den med BZIP2 och skriv resultat data till en Azure-blob. Du definierar indata-Azure Blob-datauppsättningen med `compression` `type` inställningen gzip och data uppsättningen för utdata med `compression` `type` värdet bzip2 i det här fallet.   


## <a name="next-steps"></a>Nästa steg
I följande artiklar om filbaserade data lager som stöds av Azure Data Factory:

- [Azure Blob Storage](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Fil system](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)